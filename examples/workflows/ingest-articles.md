# Ingest Articles Workflow

Turn one URL, a batch of URLs, or an index page into clean markdown files filed into `knowledge/library/` (or `knowledge/inbox/` when classification is uncertain).

## When to Use

- You read something good and want it in the library before the site goes dark
- You have a list of URLs to save in one pass
- You have a "top guides" / blog index / curated list and want to ingest the posts it links to

## Invocation

```
Ingest this article: <url>
```

```
Ingest these articles:
- <url1>
- <url2>
- <url3>
```

```
Ingest articles from this page: <url>
```

Or via slash command:

```
/ingest-articles <url or list>
```

### Summary on or off

Summaries (TL;DR, Key Concepts, Notable Quotes) are **on by default**. Turn them off when the user says any of:

- "no summary"
- "skip the summary"
- "don't summarize"
- "capture only"
- "raw capture"
- Or passes a `--no-summary` / `no-summary` flag to the slash command

When summaries are off, set `summarized: false` in the frontmatter and omit the TL;DR / Key Concepts / Notable Quotes sections entirely (along with the `---` separator that follows them). The file goes: frontmatter → H1 title → Source blockquote → `## Full Article`.

If the user hasn't said either way and the request is ambiguous (e.g., a huge batch where summarizing everything would take a long time), ask once before starting.

## Inputs & Modes

Detect the mode from what the user provided:

| Input shape | Mode |
|---|---|
| One URL | **Single** |
| Multiple URLs (list, bullets, pasted lines) | **Batch** |
| One URL + phrasing like "from this page", "extract links from", "top guides" | **Index** |

If ambiguous, ask before fetching.

## Workflow

### Step 1 — Fetch

**Try the markdown version first.**
Many documentation sites serve a clean markdown variant at a `.md` URL. Derive a markdown candidate by adding `.md` only when the URL path does not already end with `.md`; if the URL has a query string or fragment, insert `.md` before the `?` or `#`.

Do a HEAD request against the derived markdown URL, following redirects as needed. If the final response is `200` and `Content-Type` contains `text/markdown`, fetch that version instead — it's authoritative content without nav chrome, ads, or JS artifacts. When the markdown version is used, note it in the report (Step 6) so the user knows the source was clean.

If the `.md` URL returns a non-200 status or a non-markdown content type, fall back to the normal HTML fetch below.

**For Single and Batch:**
Fetch each URL (or its `.md` variant if available). If you have a fetch-and-index tool available (e.g., context-mode), use it and label the source with the article title. Otherwise, fetch the page directly.

**For Index:**
1. Fetch the index page.
2. Extract links that look like articles — not nav, footer, social, author bios, or pagination.
3. Show the candidate list to the user:
   - Title (or anchor text), domain, URL
   - Group by section if the page has visible sections
4. Ask the user to confirm, trim, or add. **Do not ingest until they approve.**
5. Once approved, fall through to Batch mode with the confirmed list (check each URL for a `.md` variant).

### Step 2 — Extract content

**If the markdown version was fetched (Step 1):**
The content is already clean markdown. Extract the title from the first `#` heading, then use the body as-is. Author and publish date may not be present in the markdown version — leave blank rather than guessing. Skip the stripping rules below since there's no HTML chrome to remove.

**If the HTML version was fetched:**
For each article URL, pull from the indexed content:

- **Title** — `<h1>` or the most prominent heading, fallback to `<title>`
- **Author** — byline, `rel="author"`, or meta tags. Blank if not present.
- **Publish date** — from the page, meta tags, or structured data. Blank if absent.
- **Body** — the article content, converted to markdown. Strip:
  - Ads, newsletter CTAs, related-posts widgets, footer, share buttons
  - Cookie banners, paywalls, author-bio boilerplate
  - Comment sections
- Preserve: headings, lists, blockquotes, code blocks, inline images (as markdown image links — these will be localized in Step 5b), embedded links.

### Step 3 — Summarize (skip if summaries are off)

**If summaries are on (default):**
Write a TL;DR — 3–5 bullets that reflect the author's actual argument, not a hype summary. If you can't write honest bullets, flag the article as probably not worth saving and ask the user.

Optionally extract:
- **Key Concepts** — named frameworks or coined terms worth coming back to
- **Notable Quotes** — direct quotes worth citing

Skip either sub-section if there's nothing to lift.

**If summaries are off:**
Skip this step entirely. Go straight to Step 4.

### Step 4 — Classify (confident-or-inbox)

Read `knowledge/codes.md` for the authoritative Dewey list.

**Classify to library when confident:**
- The article is clearly universal reference (frameworks, theory, thinker notes, durable craft knowledge)
- One Dewey code in `codes.md` is an obvious fit
- File under `knowledge/library/{range}/{category}/` (e.g., `knowledge/library/600-technology/658.8-marketing-management/`)

**Classify to work/ when clearly project-specific:**
- The article is reference for a specific active project or domain you track in `knowledge/work/`
- File under the matching `knowledge/work/<domain>/reference/` folder if one exists, otherwise `knowledge/work/reference/`

**Park in inbox when uncertain:**
- No Dewey code in `codes.md` obviously fits
- Could go multiple places
- Might need a new Dewey code (propose to the user, but don't create the folder — leave in inbox for the next `/tidy-knowledge` pass)
- File under `knowledge/inbox/` with `dewey:` left blank in frontmatter

**Do not invent Dewey codes on the fly.** If a new one is warranted, flag it in the batch summary and let the user update `knowledge/codes.md` explicitly.

### Step 5 — Write the file

Use `_templates/library-article.md` as the starting structure. Fill in the frontmatter and body.

- If summaries are on: set `summarized: true` in the frontmatter and include the TL;DR / Key Concepts / Notable Quotes sections.
- If summaries are off: set `summarized: false` and omit those sections (and the `---` separator that follows them). The body goes frontmatter → H1 title → Source blockquote → `## Full Article`.

**Folder and filename:** Create a folder `kebab-case-title/` and place the article file inside it as `kebab-case-title.md` (folder name = file name). If the folder name collides with an existing entry, append the publication (e.g., `atomic-emails-userlist/`).

Companion files (transcripts, PDFs, supplementary notes) go in the same article folder with generic names: `transcript.md`, `source.pdf`, `notes.md`. Use the `_templates/library-companion.md` template for companion markdown files.

**Target folder rules:**
- Library: `knowledge/library/<dewey>/article-slug/article-slug.md`
- Work: `knowledge/work/<domain>/reference/article-slug/article-slug.md`
- Inbox: `knowledge/inbox/` (flat files are fine here — folders are created when filing to library or work)

### Step 5b — Download images

After writing the article file, download all images referenced in the markdown body and save them locally in the article folder. This preserves images against link rot.

1. **Scan** the written markdown for image references: `![alt](url)`
2. **Skip** images that are already local paths (no `http`).
3. **Download** each remote image into an `images/` subfolder inside the article folder, using the original filename (e.g., `images/kitchen.jpg`). Use `curl -sL -o` or Python `urllib` — whichever is available.
4. **Rewrite** the image references in the markdown file from the remote URL to the local relative path (e.g., `![kitchen](https://example.com/kitchen.jpg)` → `![kitchen](images/kitchen.jpg)`).
5. **Report** any images that failed to download — leave the original remote URL in place for those and add a `<!-- IMAGE DOWNLOAD FAILED: url -->` comment after the image reference so it's easy to find later.

**Folder structure after this step:**
```
article-slug/
├── article-slug.md
└── images/
    ├── photo-1.jpg
    ├── diagram.png
    └── ...
```

**Skip this step for inbox articles** — they're flat files without folders, so there's nowhere to put the images. Images will be localized when the article is filed to library or work during a `/tidy-knowledge` pass.

### Step 5c — Cross-link the library

After ingesting, check whether any existing library articles link out to a URL you just ingested. If so, rewrite those links to point to the local file instead. This turns the library into an interconnected knowledge base rather than a bag of isolated captures.

1. **For each ingested URL**, search `knowledge/library/` and `knowledge/work/` for markdown links pointing to that URL (grep for `](https://...the-url...)`). Also check common variants — with and without trailing slash, `http` vs `https`.
2. **For each match**, rewrite the link target from the remote URL to a relative path pointing to the newly ingested article's markdown file. Use relative paths from the linking file's location (e.g., `[Malleable Software](../../004-computer-science/malleable-software-ink-and-switch/malleable-software-ink-and-switch.md)`).
3. **Don't rewrite the `source_url` in frontmatter** — that's the canonical origin and should always stay as the original URL.
4. **Don't rewrite links in the newly ingested article itself** — only update *other* articles that reference it.
5. **Report** in Step 6 how many cross-links were updated and in which files.

This also works in reverse: when writing the new article (Step 5), check whether any URLs linked in its body already exist in the library (match against `source_url` in existing article frontmatter). If a linked URL has already been ingested, rewrite that link in the new article to point to the local file instead of the web.

### Step 6 — Report back

Summarize the batch:
- How many ingested
- Where each one was filed (or "parked in inbox" with the reason)
- Any articles skipped or flagged as low-value
- Any proposed new Dewey codes the user should review
- Any cross-links updated (which files now point to the new article locally, and which links in the new article were rewritten to point to existing library entries)

Do not commit or edit anything else outside the knowledge system.

## Guardrails

- **Never fabricate content.** If a section of the article didn't fetch cleanly, leave a `<!-- FETCH INCOMPLETE -->` marker where the gap is. Don't hallucinate the author's argument.
- **Credit the author.** Include `author` and `published_date` in frontmatter when available; if either is unknown, leave that field blank.
- **Respect the author's voice in the full-article section.** Don't paraphrase, don't shorten, don't "improve." The full-article block is a faithful capture.
- **Your voice is allowed in the TL;DR and Key Concepts** — those are your notes on the article, not the article itself.
- **No emoji in saved articles** unless the original uses them.
- **Don't ingest paywalled or login-gated content** you can't actually fetch. Flag and skip.

## Example

**Input:**
```
Ingest this article: https://userlist.com/blog/atomic-emails/
```

**Behavior:**
1. Fetch the article
2. Extract: title "Atomic Emails", author, publish date, body
3. Write TL;DR (3–5 bullets on the atomic-email method)
4. Classify: "Marketing management" → `658.8-marketing-management` under `600-technology` (confident, fits existing code)
5. Write to `knowledge/library/600-technology/658.8-marketing-management/atomic-emails/atomic-emails.md`
6. Report: "Ingested 1 article, filed under 658.8"

## Related Workflows

- `tidy-knowledge.md` — run this after a batch to clean up anything parked in the inbox
