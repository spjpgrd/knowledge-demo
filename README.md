# Personal Knowledge System

A structured personal knowledge base using a modified Dewey Decimal system. Designed for use with Obsidian and Claude Code (or any AI assistant that can read local files).

Two zones: a **library** of universal reference material classified by Dewey Decimal, and **work** docs tied to current projects.

---

## Getting Started

1. Clone this repo (or download it as a .zip and copy it over `/knowledge` and `_templates` to your own Obsidian vault. 
2. Open it as an Obsidian vault (or use any markdown editor)
3. Review `knowledge/codes.md` for the Dewey classification system
4. Drop content into `knowledge/inbox/` and classify it later
5. Use `_templates/library-article.md` when ingesting articles

## Structure

```
knowledge-demo/
├── _templates/              Obsidian templates (article, etc.)
├── knowledge/
│   ├── library/             Dewey-classified reference (frameworks, thinkers, theory)
│   │   ├── 000-computer-science-and-information/
│   │   ├── 100-philosophy-and-psychology/
│   │   ├── 200-religion/
│   │   ├── 300-social-sciences/
│   │   ├── 400-language/
│   │   ├── 500-science/
│   │   ├── 600-technology/
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
- You can target a broad area by number: "look at the `100` section" covers all philosophy and psychology

### Work

Project-specific docs organized by domain. Create subfolders as needed for your projects (PRDs, runbooks, research, reference).

### Inbox

Drop anything here — links, voice transcripts, screenshots, brain dumps. Classify later, either manually or with an AI assistant.

### Ideas

Half-formed concepts that aren't ready for the library or a task. Staging area for things that need more thought.

## Customization

- Edit `knowledge/codes.md` to add Dewey subcategories relevant to your work
- Add templates to `_templates/` for your common document types
- Create domain folders under `knowledge/work/` for your active projects
- Add writing samples to `knowledge/voice-samples/` if you want AI to match your voice
