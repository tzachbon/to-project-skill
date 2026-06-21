# to-project

Takes an idea, a notes dump, or a session transcript and turns it into a complete agent-ready local project: a git repo with `TASKS.md`, an `AGENTS.md` steering file, `CONTEXT.md` glossary, `docs/adr/` for architecture decisions, `assets/` for provided source material, `research/` for generated work, and a repo-local memory index. The grill is the engine: the agent interviews you one question at a time until there is shared understanding, writing decisions into the project's docs live as the conversation advances.

This is a portable **Agent Skill**: a folder containing `SKILL.md` (with `name` and `description` frontmatter), reference files (`templates.md`, `CONTEXT-FORMAT.md`, `ADR-FORMAT.md`), an `EXTEND.md` mode for folding new input into a project that already exists, and a `base-skills/` bundle that ships into every project it scaffolds. Any agent harness that supports the Agent Skills format can load and invoke it.

## Install

The universal pattern for every harness: make the `to-project/` folder available in that harness's skills directory. Clone this repo (or download it), then copy or symlink the `to-project/` subfolder into the right location.

```sh
git clone https://github.com/tzachbon/to-project-skill.git
```

### Claude Code

Skills live at `~/.claude/skills/<name>/`.

```sh
# Option A: symlink (edits to the clone stay live)
ln -s "$(pwd)/to-project-skill/to-project" ~/.claude/skills/to-project

# Option B: copy
cp -r to-project-skill/to-project ~/.claude/skills/to-project
```

Source: `claude-code-tools.md` from the superpowers skill reference (v6.0.3).

### Codex

Skills live at `~/.codex/skills/` (overrideable via `$CODEX_HOME`). Codex also reads `~/.agents/skills/`, which is a cross-runtime alias shared with Copilot CLI and Gemini CLI.

```sh
# Via the native Codex path
mkdir -p ~/.codex/skills
cp -r to-project-skill/to-project ~/.codex/skills/to-project

# Or via the shared cross-runtime path
mkdir -p ~/.agents/skills
cp -r to-project-skill/to-project ~/.agents/skills/to-project
```

Source: `codex-tools.md` from the superpowers skill reference (v6.0.3).

### GitHub Copilot CLI

Skills live at `~/.copilot/skills/`. Copilot CLI also reads the cross-runtime alias `~/.agents/skills/`.

```sh
# Via the native Copilot CLI path
mkdir -p ~/.copilot/skills
cp -r to-project-skill/to-project ~/.copilot/skills/to-project

# Or via the shared cross-runtime path
mkdir -p ~/.agents/skills
cp -r to-project-skill/to-project ~/.agents/skills/to-project
```

Source: `copilot-tools.md` from the superpowers skill reference (v6.0.3).

### Gemini CLI

Skills live at `~/.gemini/skills/`. Gemini CLI also reads `~/.agents/skills/`; when both exist, `~/.agents/skills/` takes precedence.

```sh
# Via the native Gemini CLI path
mkdir -p ~/.gemini/skills
cp -r to-project-skill/to-project ~/.gemini/skills/to-project

# Or via the shared cross-runtime path (takes precedence)
mkdir -p ~/.agents/skills
cp -r to-project-skill/to-project ~/.agents/skills/to-project
```

Source: `gemini-tools.md` from the superpowers skill reference (v6.0.3).

### Other harnesses

If your harness supports the Agent Skills format but is not listed above, place the `to-project/` folder in its skills directory and verify the path against your harness's documentation.

## Usage

Ask your agent to turn an artifact into a project:

> "Turn this into a project."
> "Scaffold a project from these notes."
> "Make a project out of this session."

To fold new input into a project that already exists (extend mode):

> "Add this to my project."
> "Extend the project with these notes."

The agent picks up the skill automatically from the trigger phrases in `SKILL.md`.

## What it scaffolds

- `AGENTS.md`: steering file, working norms, multi-agent contract, directory map
- `TASKS.md`: task board seeded from the input (goals to Backlog, open questions extracted)
- `assets/`: provided source material (transcripts, artifacts, images), tracked in git, indexed in `assets/sources.md`
- `research/`: working and generated research the agents produce
- `CONTEXT.md`: glossary of canonical terms (written live during the grill; created on first term)
- `docs/adr/`: architecture decision records for hard-to-reverse trade-offs (created on first ADR)
- `docs/memory/MEMORY.md`: repo-local memory index (header and format, no facts yet)
- Related projects: if sibling directories are relevant, linked in AGENTS.md with a one-line why
- `.agents/skills/`: three bundled base skills copied in and stamped (`installed`, `source`); `.claude/skills` symlinks here for Claude Code, other harnesses symlink their own dir

## Bundled base skills

Every scaffolded project ships with three small skills in `.agents/skills/`. They are documented in the project's `AGENTS.md` and fired by directives there, so the project maintains itself as you work:

- **`capture-to-project`**: when a decision, term, durable fact, or actionable item surfaces, it files the point into the right doc (`CONTEXT.md`, an ADR, `TASKS.md`, `docs/memory/`, or a `research/` note), then commits.
- **`tidy-project`**: a janitor pass. Clears junk, fixes stale tasks and index drift, auto-fixing the safe class and proposing anything destructive, then commits.
- **`recap-project`**: read-only cold-start orientation from `AGENTS.md`, `TASKS.md`, recent commits, and `CONTEXT.md`.

The skill keeps everything tracked: each base skill commits its own changes, and `tidy-project` enforces that nothing important is left uncommitted.

## License

MIT. Copyright (c) 2024 Zach Bonfil.
