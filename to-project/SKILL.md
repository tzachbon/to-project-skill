---
name: to-project
description: Use when the user wants to turn an idea, a notes dump, or a session transcript into a complete agent-ready local project. Triggers include "turn this into a project", "scaffold a project from this", "make a project out of this session/these notes", "set up a new repo for this".
---

# to-project

Turn raw input (an idea, notes, or a whole session) into a complete, agent-ready
local project: git repo, `CLAUDE.md`, `TASKS.md`, docs, research intake, a
glossary, and a memory index. The grill is the engine. You interview the user to
shared understanding, and the project's docs crystallise live during that
interview.

**Core principle:** a folder is not a project. A project is a *contract* other
agents can pick up cold: where to look first (`TASKS.md`), how not to collide
(the multi-agent norms), and why it exists (provenance + `CONTEXT.md`).

## Workflow

Run in order. The skill stays in the main session because the grill is an
interview, it cannot be delegated.

### 1. Ingest
Accept whatever the user has: a one-line idea, a pasted artifact, a path to
notes, or this session's transcript. Read it fully before asking anything.

### 2. Infer and confirm the frame
From the input, infer:
- **Project type**: one of `presentation`, `software`, `research`,
  `ops-runbook`, `docs`, or `other (describe)`. State your guess with confidence.
- **Target path**: default `~/projects/<slug>`, slug derived from the input.

Prompt once: "Type X, path Y. Go, or correct?" One line, then proceed.

### 3. Create the repo
Before creating: if the target path already exists and is non-empty, stop and
ask the user to confirm before proceeding. Do not scaffold over an existing project.

At the confirmed path: `mkdir`, `git init`, write a `.gitignore` for the detected
type (see templates.md), picking the variant that matches the detected stack (e.g.
node vs python for software projects) and defaulting to the closest match. Nothing
is committed yet. The repo exists early so the grill can write docs live.

### 4. Grill to shared understanding (always, in full)
Interview the user relentlessly, one question at a time, walking the design tree
and resolving dependencies one by one. For each question, give your recommended
answer. If a question is answerable from the input, answer it from the input
instead of asking.

Write decisions live, do not batch:
- **Terminology** -> `CONTEXT.md` glossary (format: CONTEXT-FORMAT.md). Devoid of
  implementation detail. A glossary, nothing else.
- **Hard-to-reverse trade-offs** -> `docs/adr/NNNN-slug.md`, sparingly (format:
  ADR-FORMAT.md). Only when the decision is hard to reverse AND surprising without
  context AND a real trade-off. If any of the three is missing, skip it.

Grilling moves:
- Challenge terms that conflict with what is already in `CONTEXT.md`.
- Sharpen fuzzy or overloaded words into one canonical term.
- Stress-test relationships with concrete edge-case scenarios.
- The input seeds questions, it never shortens the grill.

### 5. Preview and confirm
Before writing the remaining scaffold, show:
- detected type + final path
- the full directory tree to be created
- extracted goals, first tasks, and open questions

Wait for one explicit "go".

### 6. Scaffold (on go)
Write from templates.md:
- **`CLAUDE.md`**: two layers: (a) core instructions (what the project is; "read
  TASKS.md first, it is the source of truth"; global conventions noted as
  reminders) and (b) an index pointing to where docs/research/resources live and
  how to handle each. Include the multi-agent contract (below). The index entries
  for `CONTEXT.md` and `docs/adr/` are annotated as conditional: both are created
  lazily during the grill (first term and first ADR respectively) and may not
  exist in a fresh scaffold.
- **`TASKS.md`**: goals to Backlog, extracted Open Questions filled in; Now and
  Next left empty.
- **Dirs**: `docs/`, `docs/memory/`, `research/inbox/`, `resources/`; `.gitkeep` in empties. The
  `CLAUDE.md` index is the single source of truth for what-goes-where. No
  per-directory READMEs.
- **Memory index**: seed the repo-local index (header + format, no facts).
- **Provenance**: copy the source artifact into `research/inbox/` with a dated
  name, log it in `research/inbox/sources.md`; ADRs and CONTEXT terms that came
  from it cite it. A typed-only idea goes to `research/inbox/origin.md`.

### 7. Commit
One commit, "Initial project scaffold". No AI attribution (per the user's global
rule). Do NOT create a remote. Show the final tree.

## Multi-agent contract (write into CLAUDE.md)
The default working model this skill assumes and documents:
- One **main/orchestrator session**; **named side-agents** each run their own
  loop, often on their own branch or git worktree.
- `TASKS.md` is the coordination surface. Claim before working.
- Shared files: re-read right before editing, keep edits narrow, never revert
  another writer.
- If a write is blocked or risks collision, work in a worktree and merge back
  (rebase onto moved main, then ff-only).
- Converging a side session back into the main thread closes the loop; the main session stays the source of truth.

## Files
- **templates.md**: all scaffold file bodies (CLAUDE.md, TASKS.md, sources.md,
  memory index, `.gitignore` by type).
- **CONTEXT-FORMAT.md**: glossary format for step 4.
- **ADR-FORMAT.md**: ADR format for step 4.
