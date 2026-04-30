# Personal Knowledge System

A structured personal knowledge base using a modified Dewey Decimal system. Designed for use with Obsidian and Claude Code (or any AI assistant that can read local files).

Two zones: a **library** of universal reference material classified by Dewey Decimal, and **work** docs tied to current projects.

---

## Getting Started

1. Clone this repo (or [download it as a .zip](https://github.com/spjpgrd/knowledge-demo/archive/refs/heads/main.zip)) and copy over `/knowledge` and `_templates` into your own Obsidian vault. 
2. Open it as an Obsidian vault (or use any markdown editor)
3. Review `knowledge/codes.md` for the Dewey classification system
4. Drop content into `knowledge/inbox/` and classify it later
5. Use `_templates/library-article.md` when ingesting articles (each article gets its own folder)
6. Use `_templates/library-companion.md` for companion files (transcripts, supplementary notes)
7. To automate ingestion with an AI assistant, see `examples/workflows/ingest-articles.md` and `examples/workflows/tidy-knowledge.md`

## Structure

```
knowledge-demo/
├── _templates/              Obsidian templates (article, etc.)
├── knowledge/
│   ├── library/             Dewey-classified reference (frameworks, thinkers, theory)
│   │   ├── 000-computer-science-and-information/
│   │   ├── 100-philosophy-and-psychology/
│   │   │   └── 150-psychology/
│   │   │       └── example-cognitive-biases/   ← each article is a folder
│   │   │           ├── example-cognitive-biases.md
│   │   │           └── notes.md                ← companion file
│   │   ├── 200-religion/
│   │   ├── 300-social-sciences/
│   │   ├── 400-language/
│   │   ├── 500-science/
│   │   ├── 600-technology/
│   │   │   └── 658-general-management/
│   │   │       └── example-first-90-days/
│   │   │           └── example-first-90-days.md
│   │   ├── 700-arts-and-recreation/
│   │   ├── 800-literature/
│   │   └── 900-history-and-geography/
│   ├── work/                Project-specific docs (PRDs, research, runbooks)
│   ├── inbox/               Workbench — dump anything here, classify later
│   ├── ideas/               Half-formed concepts (staging area)
│   ├── voice-samples/       Writing voice reference material
│   ├── archive/             Dead projects
│   ├── codes.md             Dewey code reference (authoritative)
│   └── README.md            Knowledge system guide
└── README.md                You are here
```

## Key Concepts

### Library (Dewey-classified)

Universal knowledge that outlives any single project. Articles, frameworks, thinker notes, theory.

- **Range folders** group by Dewey hundreds (e.g., `600-technology/`)
- **Category folders** nest under ranges (e.g., `600-technology/658.8-marketing-management/`)
- **Article folders** nest under categories — each article gets its own folder (`article-slug/article-slug.md`). Companion files (transcripts, PDFs, supplementary notes) go in the same folder with generic names (`transcript.md`, `source.pdf`).
- You can target a broad area by number: "look at the `100` section" covers all philosophy and psychology

### Work

Project-specific docs organized by domain. Create subfolders as needed for your projects (PRDs, runbooks, research, reference).

### Inbox

Drop anything here — links, voice transcripts, screenshots, brain dumps. Classify later, either manually or with an AI assistant.

### Ideas

Half-formed concepts that aren't ready for the library or a task. Staging area for things that need more thought.

## Workflows (optional)

If you use Claude Code or another AI assistant that can read local files, two workflows are included:

- **`examples/workflows/ingest-articles.md`** — turn one URL, a batch of URLs, or an index page into clean markdown filed under the right Dewey code. Triggered by phrases like *"Ingest this article: <url>"* or a `/ingest-articles` slash command. Also handles cross-linking — when a new article is ingested, existing library entries that link to that URL are rewritten to point at the local file instead.
- **`examples/workflows/tidy-knowledge.md`** — process anything sitting in `knowledge/inbox/` and file it into `library/` or `work/` based on the Dewey codes in `knowledge/codes.md`.

Both workflows are plain markdown — adapt or replace them to fit your assistant of choice.

## Saving articles from your browser (optional)

If you want to get real real, you can save articles straight from your browser. They land in a queue (`knowledge/inbox/`) for the ingest workflow to process whenever you're ready.

The shortcut is **Save to Knowledge Library**. You configure two things in it:

- **Append Text to {folder}** — point this at your `knowledge/inbox/` folder
- **File Path within the folder** — the filename to append URLs to (e.g., `queue.md`)

Then add this bookmarklet to your browser's bookmarks bar:

```
javascript:void(window.open('shortcuts://run-shortcut?name=Save to Library&input=text&text=%27+encodeURIComponent(location.href)))
```

Click it on any page to drop the URL into the queue.

If you use Safari, you can skip the bookmarklet and use **Share > Shortcuts > Save to Knowledge Library** instead.

## Customization

- Edit `knowledge/codes.md` to add Dewey subcategories relevant to your work
- Add templates to `_templates/` for your common document types
- Create domain folders under `knowledge/work/` for your active projects
- Add writing samples to `knowledge/voice-samples/` if you want AI to match your voice
