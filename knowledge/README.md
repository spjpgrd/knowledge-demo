# Knowledge

Two zones: a **library** of universal reference material classified by Dewey Decimal, and **work** docs tied to current projects.

---

## Library (Dewey-classified)

Universal knowledge that outlives any single project. Organized by subject using real Dewey Decimal codes.

**Rules:**
- ALWAYS check `codes.md` before creating a new category directory
- Range folders: `{hundred}-{lowercase-hyphenated-name}` (e.g., `100-philosophy-and-psychology/`)
- Category folders: `{code}-{lowercase-hyphenated-name}` (e.g., `150-psychology/`)
- Categories nest under their range parent — to target a broad area, reference the range (e.g., "look at `100`")
- Only create range folders when they have at least one child category
- Start flat — only create subcategories when 6+ files in parent AND 3+ share a subcategory
- Never create empty directories
- Every file gets YAML frontmatter: `title`, `dewey`, `source`, `tags`, `format`, `added`

## Work

Project-specific docs. Create subfolders by domain:

| Folder | Contents |
|--------|----------|
| `prds/` | Specs and PRDs (by year/cycle) |
| `runbooks/` | How-to guides, tool setup |
| `reference/` | Links, PDFs, tool notes |
| `templates/` | Project-specific templates |

Adapt this table to your own projects and domains.

## Inbox

Drop anything here — voice transcripts, links, screenshots, brain dumps. Classify and file them when you're ready.

## Linking from Tasks

Reference knowledge files from task metadata:

```yaml
resource_refs:
  - knowledge/library/100-philosophy-and-psychology/150-psychology/example-article.md
  - knowledge/work/prds/2025/some-prd.md
```
