# Tidy Knowledge Workflow

Process unstructured captures into the knowledge system.

## When to Use

- You dropped files into `knowledge/inbox/`
- You want to extract knowledge from recent daily notes
- Weekly review: "anything in my scratchpad worth keeping?"

## The Prompt

```
Tidy my knowledge
```

or

```
/tidy-knowledge
```

## What It Does

### Step 1: Scan inbox

Read all files in `knowledge/inbox/` (skip README.md).

### Step 2: Scan daily notes (optional)

Ask the user: "Want me to also scan your recent daily notes for extractable knowledge?"

If yes, read files from `notes/daily/` (default: last 7 days). Extract:
- **Links with labels** — URLs with enough context to be useful later
- **Reusable insights/frameworks** — things that transcend the specific day
- **Action items** — flag for BACKLOG.md, don't file as knowledge
- **Skip:** standup items, ephemeral task notes, meeting logistics

### Step 3: Classify each item

For every extracted item, determine:

| Classification | Where it goes | Criteria |
|---------------|---------------|----------|
| Universal reference | `knowledge/library/` | Frameworks, thinker notes, theory. Outlives any project. |
| Work-specific | `knowledge/work/` | PRDs, research, runbooks. Tied to current projects. |
| Action item | `BACKLOG.md` | Has a verb, needs doing. Not knowledge. |
| Junk/duplicate | Delete | Already captured elsewhere, or not worth keeping. |

**For library items:**
1. Read `knowledge/codes.md` to find the right Dewey code
2. If no code fits, propose a new one (with the real Dewey number) and ask the user
3. Add YAML frontmatter: `title`, `dewey`, `source`, `tags`, `format`, `added`

**For work items:**
1. Match to an existing subfolder in `knowledge/work/`
2. If none fits, propose creating one and ask the user

### Step 4: Present the plan

Show a table:

```
| Item | Classification | Destination | Reasoning |
|------|---------------|-------------|-----------|
| munger-talk-notes.md | library | library/100-philosophy-and-psychology/153-mental-processes-and-intelligence/ | Decision-making framework notes |
| api-endpoints.md | work | work/runbooks/ | Project-specific how-to |
| "check deployment" | backlog | BACKLOG.md | Action item, not knowledge |
```

**Do not move anything until the user approves.**

### Step 5: Execute

On approval:
1. Move files to their destinations
2. Add frontmatter to library files that don't have it
3. Clean processed files from `knowledge/inbox/`

### Step 6: Leafing check

After moves, check each Dewey category in `library/`:
- If a category has 6+ files AND 3+ share a subcategory → propose creating the subcategory
- Reference `codes.md` for exact naming
- Never create empty directories

### Step 7: Summary

Report:
- Files filed (count by destination)
- Items flagged for backlog
- Items that need clarification
- Any new Dewey categories created
- Any leafing changes proposed

## Voice Transcript Handling

When an inbox file is a voice transcript or brain dump:
1. Read the full content
2. Extract the distinct ideas/topics (there may be several in one file)
3. Each idea becomes its own item in the classification table
4. The original transcript can be archived or deleted after extraction

## Adding New Dewey Codes

If content doesn't fit an existing code:
1. Look up the real Dewey Decimal number for the subject
2. Propose it to the user with the standard Dewey name
3. On approval, add it to `knowledge/codes.md`
4. Create the directory in `library/` using the naming convention in `codes.md` (`{code}-{lowercase-hyphenated-name}` under the appropriate range folder)
