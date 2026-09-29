# pod-charly-mcp

The `charly-mcp` candy of the OpenCharly candy library, as a standalone repo
(the candy de-submodule cutover, kind-prefixed naming). `pod-` because the candy
declares a `service:` and `port: 18765`.

## What it provides

An MCP server that exposes the **full charly CLI as tools** over Streamable HTTP
on port `18765` (`/mcp`). The candy is a meta-composition
(`candy: [charly, supervisord]`) with no install content of its own: it bakes the
charly binary + supervisord, runs `charly mcp serve --listen 0.0.0.0:18765` under
supervisord, and mounts a world-writable `/workspace` project directory.

| Property | Value |
|---|---|
| Kind | Meta-layer composition (`candy: [charly, supervisord]`) |
| Port | `18765` (Streamable HTTP MCP endpoint at `/mcp`) |
| Service | supervisord program `charly-mcp` (`restart: always`) |
| Volume | `project` → `/workspace` (bind-mount the project root) |
| Working dir | `/workspace` |
| `mcp_provide` | `charly` at `http://{{.ContainerName}}:18765/mcp` |

The `mcp_provide` block advertises the server through the
`ai.opencharly.mcp_provide` OCI label, so consumers — Claude Code, Open WebUI,
OpenClaw, or charly's own declarative `mcp:` check verb — can drive it with no
out-of-band URL configuration. The candy also bakes `plugin-mcp` (via
`bake_plugin:`) so the in-container `charly mcp serve` resolves the external `mcp`
command at runtime.

## Project resolution — three patterns

Build-mode tools (`box.build`, `box.inspect`, `box.list.*`) read `charly.yml`, so
the candy supports three paths:

1. **Bind-mount a local project** — `charly config <image> --bind project=/path/to/opencharly`;
   local edits are immediately visible.
2. **Pin a remote repo** — `charly config <image> -e CHARLY_PROJECT_REPO=opencharly/charly@<sha>`;
   the server clones into the repo cache at startup.
3. **Auto-fallback** (default) — nothing mounted: a placeholder project is seeded
   into `/workspace`, and project tools resolve the default `opencharly/charly`
   cache. Opt out with `--no-default-repo`.

## How to use it

Compose the candy into any box that should be reachable as an MCP gateway:

```yaml
my-box:
  candy:
    base: cachyos.cachyos
    candy:
      - '@github.com/opencharly/pod-charly-mcp:<tag>'
```

```bash
charly box build my-box
charly config my-box --bind project=/path/to/opencharly   # pattern 1
charly start my-box
```

The box must publish port `18765`. It works on both bridge-networked boxes and
host-networked ones (the MCP host-port mapping resolves the container port
verbatim under `network: host`).

## Layout

- `charly.yml` — the `charly-mcp` candy entity (description, `bake_plugin`,
  `candy`, port, `mcp_provide`, volume, service, plan) plus its `skill:` entity.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `CHANGELOG/` — per-CalVer history.
- `README.md` — this user overview.

## Related

- Owning skill: `/charly-coder:charly-mcp` — the candy's properties, the three
  deployment patterns, the host-networking caveat, and the port rationale.
- `/charly-build:charly-mcp-cmd` — the `charly mcp serve` architecture and the
  `mcp:` check verb.
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI and image builder
- [`opencharly/opencharly`](https://github.com/opencharly/opencharly) — the umbrella
