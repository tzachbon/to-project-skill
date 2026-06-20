# Scaffold templates

File bodies the skill writes in step 6. Replace `{{placeholders}}`. Keep each
file lean: `CLAUDE.md` is a map, not a manual.

---

## CLAUDE.md

```markdown
# CLAUDE.md

Guidance for any agent working in this repo.

## What this is

{{one-paragraph: what the project is, who it's for, what "done" looks like.}}

**Read `TASKS.md` first.** It is the live task board and the source of truth for
what is in flight.

## Working norms

{{project-specific norms inferred during the grill. Examples: build/test commands,
where output goes, review gates.}}

Global conventions (from the user's `~/.claude/CLAUDE.md`) apply automatically and
are not repeated here. Reminders that bite often in this project:
- No AI attribution in commits, PRs, or file content.
- No em dashes in prose. Self-edit for slop before sending.

## You are not alone (multi-agent contract)

Assume other agents and the user work this repo at the same time.

- One **main/orchestrator session** runs the show. **Named side-agents** each take
  a focused sub-task in their own context, often on their own branch or git
  worktree.
- **`TASKS.md` is the coordination surface.** Claim a task before working it; mark
  status as you go.
- **Shared files change under you.** Re-read right before editing, keep edits
  narrow, never revert another writer's work.
- If a write is blocked or risks collision, work in a **worktree and merge back**
  (rebase onto moved main, then `git merge --ff-only`).
- Converging a side session back into the main thread closes the loop; the main
  session stays the source of truth.

## Index: where things live

- **`TASKS.md`**: task board. Read first, update as you work.
- **`CONTEXT.md`**: glossary. The project's canonical terms. Challenge any usage
  that conflicts with it. _(Created on the first term resolved during the grill.
  May not exist yet in a fresh scaffold.)_
- **`docs/`**: durable docs. `docs/adr/` holds architecture decision records
  (why a hard-to-reverse choice was made). _(`docs/adr/` is created on the first
  hard-to-reverse trade-off. May not exist yet in a fresh scaffold.)_
- **`research/`**: working material. `research/inbox/` is intake;
  `research/inbox/sources.md` indexes every source. Keep exploration here.
- **`resources/`**: assets and external references the project depends on.
- **`docs/memory/MEMORY.md`**: repo-local memory index. One line per durable fact;
  facts live in sibling files.

{{type-specific entries, e.g. for a presentation: "slides/: the deck".}}
```

---

## TASKS.md

```markdown
# {{Project}}: Task Board

`TASKS.md` is the source of truth for what is in flight. Update it as you work.

## Now
_(empty: set the current focus when work starts)_

## Next
_(empty: queue the next items when priorities are clear)_

## Backlog
{{extracted goals as discrete, actionable items}}

## Open questions
{{questions surfaced during the grill that are not yet resolved}}

## Reference
- Origin and sources: `research/inbox/sources.md`
- Glossary: `CONTEXT.md` _(if present; may not exist yet in a fresh scaffold)_
- Decisions: `docs/adr/` _(if present; may not exist yet in a fresh scaffold)_
```

---

## research/inbox/sources.md

```markdown
# Sources: intake index

One row per source fed into this project. Newest first.

| Date | Source | Path | Notes |
|---|---|---|---|
| {{YYYY-MM-DD}} | {{origin: session / notes / artifact}} | {{research/inbox/<file>}} | {{one line}} |
```

---

## research/inbox/origin.md (typed-only idea, no artifact)

```markdown
# Origin

Captured {{YYYY-MM-DD}}. The idea this project was scaffolded from, verbatim.

{{the user's original idea text}}
```

---

## docs/memory/MEMORY.md

```markdown
# Memory Index

Repo-local durable facts. One line per fact; the fact itself lives in a sibling
file in this directory. Format for each file:

    ---
    name: <kebab-slug>
    description: <one-line summary used to judge relevance>
    ---
    <the fact. Link related facts with [[their-name]].>

Add a pointer here when you write a fact: `- [Title](file.md): hook`.

<!-- no facts yet -->
```

---

## .gitignore by project type

Always include the common block. Append the type-specific block.

### common
```
.DS_Store
*.log
.env
.env.local
.scratch/
tmp/
```

### presentation (Slidev / node)
```
node_modules/
dist/
.slidev/
*.local
```

### software (node)
```
node_modules/
dist/
build/
coverage/
```

### software (python)
```
__pycache__/
*.pyc
.venv/
venv/
.pytest_cache/
dist/
build/
*.egg-info/
```

### research / docs / ops-runbook
```
# (common block is usually enough; add tool-specific dirs as they appear)
```
