---
name: knowledge-refactor
description: Use when user asks to refactor, digest, or process Obsidian inbox captures into permanent notes. Also invoke proactively during any vault session when an inbox folder contains ≥5 files without a *Refactored:* marker.
---

# Knowledge Refactor

Processes inbox captures → permanent typed notes → MOC links.

## Process

### 0. Discover Vault Structure

```bash
obsidian folders
```

Find the folder containing an `Inbox/` subfolder — this is `<KNOWLEDGE_ROOT>`. Also list MOC files (files named `MOC - *`) to identify `<MOC_FILES>`.

If no inbox folder exists, ask the user where their knowledge inbox is before proceeding.

### 1. Scan Inbox

```bash
obsidian files folder="<KNOWLEDGE_ROOT>/Inbox"
```

Read each file. Count captures without a `*Refactored:*` marker. If <3 total, report count and stop — not worth refactoring yet.

### 2. Categorize Each Capture

| Folder | What belongs |
|--------|-------------|
| `Concepts/` | Mental models, how things work, definitions |
| `Patterns/` | Reusable designs, topologies, prompting strategies |
| `Pitfalls/` | Bugs, gotchas, anti-patterns, things that went wrong |
| `Recipes/` | Step-by-step how-tos, runbooks, command sequences |
| `References/` | External links, docs, tools, papers |

Create subfolders under `<KNOWLEDGE_ROOT>/` if they don't exist yet.

### 3. Create Permanent Notes

For each capture worth promoting:

```bash
obsidian create path="<KNOWLEDGE_ROOT>/<Folder>/<Title>.md" content="---\ntags: [<topic>]\ntype: <type>\ndate: <TODAY>\n---\n\n# <Title>\n\n<synthesized content>\n\n## Related\n- [[<MOC_NAME>]]" silent overwrite
```

**Title**: Descriptive noun/verb phrase. Not "Bug #1". Yes: "Subagent Worktree Path Must Be Absolute".

**Content**: Synthesize — extract the *reusable insight*, not the event. Write as reference, not diary.

**Tags**: Derive from topic (e.g. `claude-code`, `agent-harness`). Don't invent tags — use what's already in the vault:
```bash
obsidian tags sort=count counts
```

### 4. Update MOC Files

For each new note, append a wikilink under the matching section in the relevant MOC file:

```bash
obsidian append file="<MOC_NAME>" content="\n- [[<Title>]] — one-line summary"
```

**Sections**: `## Concepts`, `## Patterns`, `## Pitfalls`, `## Recipes`, `## References` — match the folder chosen in step 2.

If no MOC file exists for the topic, ask the user before creating one.

### 5. Mark Inbox as Processed

```bash
obsidian append path="<KNOWLEDGE_ROOT>/Inbox/<filename>" content="\n\n---\n*Refactored: <TODAY>*"
```

## Proactive Suggestion

At natural pauses, count inbox files lacking a `*Refactored:*` marker. If ≥5, suggest:

> "Inbox has N unprocessed captures — good time to refactor?"

Do not interrupt mid-task. Suggest at session start or when user wraps up a topic.

## Quality Bar

Only promote captures with **reusable insight**. Skip:
- One-time facts that won't generalize
- Content already in an existing permanent note (update that note instead)
- Vague observations with no actionable takeaway

## Common Mistakes

- Copy-pasting raw inbox text → synthesize instead
- Creating duplicate notes → search existing notes first
- Linking to wrong MOC section → match section to folder type
- Marking inbox processed before all captures in that file are handled
