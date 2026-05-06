---
name: knowledge-refactor
description: Use when processing accumulated Obsidian inbox captures in Claude Knowledge vault — promotes raw captures to permanent typed notes and updates MOC wikilinks. Trigger when user asks to refactor, digest, or process inbox, or proactively suggest when ≥5 unprocessed captures exist.
---

# Knowledge Refactor

Processes `Claude Knowledge/Inbox/` captures → permanent typed notes → MOC links.

## Process

### 1. Scan Inbox

```bash
obsidian files folder="Claude Knowledge/Inbox" 2>&1
```

Read each inbox file. Count distinct captures (bullet points or new sections). If <3 total unprocessed, report count and stop — not worth refactoring yet.

### 2. Categorize Each Capture

| Folder | What belongs |
|--------|-------------|
| `Concepts/` | Mental models, how things work, definitions |
| `Patterns/` | Reusable designs, topologies, prompting strategies |
| `Pitfalls/` | Bugs, gotchas, anti-patterns, things that went wrong |
| `Recipes/` | Step-by-step how-tos, runbooks, command sequences |
| `References/` | External links, docs, tools, papers |

### 3. Create Permanent Notes

For each capture worth promoting:

```bash
obsidian create path="Claude Knowledge/<Folder>/<Title>.md" content="---\ntags: [claude-code]\ntype: concept\ndate: <TODAY>\n---\n\n# <Title>\n\n<synthesized content>\n\n## Related\n- [[MOC - Claude Code]]" silent overwrite
```

**Title**: Descriptive noun/verb phrase. Not "Bug #1". Yes: "Subagent Worktree Path Must Be Absolute".

**Content**: Synthesize — extract the *reusable insight*, not the event. Write as reference, not diary.

**Tags**: Use `claude-code` and/or `agent-harness` based on topic.

### 4. Update MOC Files

For each new note, append wikilink under the correct section:

```bash
obsidian append file="MOC - Claude Code" content="\n- [[<Title>]] — one-line summary"
```

Pick `MOC - Claude Code` or `MOC - Agent Harness` (or both) based on topic.

**Sections**: `## Concepts`, `## Patterns`, `## Pitfalls`, `## Recipes`, `## References` — match folder chosen in step 3.

### 5. Mark Inbox as Processed

After processing each inbox file, append:

```bash
obsidian append path="Claude Knowledge/Inbox/<filename>" content="\n\n---\n*Refactored: <TODAY>*"
```

## Proactive Suggestion

At natural pauses, check for inbox files lacking a `*Refactored:*` marker. If ≥5 exist, suggest:

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
