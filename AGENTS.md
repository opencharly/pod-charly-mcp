# AGENTS.md — pod-charly-mcp

Standalone candy repo for the `charly-mcp` candy — an MCP server exposing the
full charly CLI as Streamable-HTTP tools on port `18765`. The entire candy lives
in `charly.yml` at the repo root: the `charly-mcp:` entity with its
`bake_plugin`, `candy:` composition, port, `mcp_provide`, volume, service, and
`plan:`, plus the `skill:` entity projected into the marketplace corpus. There is
no source tree — the candy is pure service wiring.

Canonical files:

- `charly.yml` — the `charly-mcp` candy entity and its `skill:` entity.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `CHANGELOG/` — per-CalVer history.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-coder:charly-mcp` — the owning skill: candy properties, the three
  project-resolution patterns, the host-networking caveat, and the port
  rationale. Load before editing, building, deploying, or troubleshooting this
  candy.
- `/charly-build:charly-mcp-cmd` — the `charly mcp serve` server architecture,
  destructive-hint annotations, `--read-only` filter, and the `mcp:` check verb.
- `/charly-image:layer` — the candy authoring reference (`charly.yml` schema,
  `plan:` step verbs, service declarations). Load before editing any entity
  field or plan step.
- `/charly-check:check` — the `mcp:` declarative check verb used by the plan.
- `/charly-internals:git-workflow` — before any git/PR action.

## Build / validate / test

- `charly box validate` at the repo root — the structural check: the manifest
  must parse and validate at the installed charly.
- The merge gate is the **org-wide** `charly/pr-validator` (required check
  `validate / validate`, defined in `opencharly/.github`); this repo has **no**
  per-repo candy gate. Its only workflow file is
  `.github/workflows/tag-on-merge.yml`.
- The live R10 witness is a composing box's `check` bed, which exercises the
  baked `mcp:` checks (`mcp-charly-ping`, `mcp-charly-list-tools`,
  `mcp-charly-call-version`, `mcp-charly-call-list-boxes`). There is no
  charly-mcp-only bed.

## Modify this repo

- Edit the `charly-mcp:` candy entity in `charly.yml`; the `skill:` entity in the
  same file is the owning skill's source — a candy change and its skill change
  land together.
- The `mcp_provide` name `charly` is the service contract; a rename breaks every
  consumer's MCP wiring unless it is an explicit hard cutover.
- Keep the `bake_plugin` pin in step with `plugin-mcp`; it contributes to every
  composing image's effective version and forces a rebuild on change.
- The `skill:` entity is the source for `/charly-coder:charly-mcp`; never edit
  the generated `SKILL.md` in the marketplace corpus.

## Landing

- PR-only. Every change lands through a pull request; the org-required
  `charly/pr-validator` validates the diff and body and arms native auto-merge
  on PASS. Direct pushes to `main` are blocked.
- History lives in `CHANGELOG/` (written by `tag-on-merge` at merge time); the
  PR body IS the changelog.
- The authoritative rulebook is the umbrella `AGENTS.md` in
  `opencharly/opencharly` and `charly/AGENTS.md` in the charly repo. Do not
  restate its rules here.
