# Agentic Project Scaffolding

[![Blackwell Systems™](https://raw.githubusercontent.com/blackwell-systems/blackwell-docs-theme/main/badge-trademark.svg)](https://github.com/blackwell-systems)

A Claude Code skill that scaffolds new projects interactively. Given a small set of inputs — language, project type, and distribution target — it produces a complete, opinionated initial structure: directory layout, git repository with a `main` branch, README, CLAUDE.md stub, CI workflow, and optionally a goreleaser config and homebrew tap registration.

## Why

Starting a new project involves a predictable set of decisions and a predictable set of files. Most of those files are boilerplate with a few project-specific values filled in. Doing it by hand is slow and inconsistent. Doing it with a general-purpose scaffolding tool means fighting its opinions. This skill handles the decisions explicitly, asks only what it needs to know, and produces exactly the right structure for the combination of choices made.

## What It Produces

For every project:

- **Directory** at the specified path, created if it does not exist
- **Git repository** initialized with `main` as the default branch
- **README.md** covering what the project is, how to install or use it, and any relevant decision context
- **CLAUDE.md stub** with project-specific context sections for Claude Code to load on each session
- **CI workflow** (GitHub Actions) appropriate to the language and project type

For Go and Rust projects with homebrew distribution:

- **goreleaser config** (`.goreleaser.yaml`) with platform matrix, archive settings, and checksum configuration
- **Homebrew tap entry** — a formula stub in the correct tap repository location, ready for wiring to the release pipeline

## Decision Points

The skill asks three questions before producing any output:

**1. Language**

- `go` — Go CLI tool or MCP server; produces `go.mod`, `main.go` stub, standard package layout
- `rust` — Rust CLI tool or MCP server; produces `Cargo.toml`, `src/main.rs` stub, standard crate layout
- `markdown` / `prompt` — Protocol, skill, or documentation repository; no compiled source, produces prompt files and a `docs/` layout

**2. Project type**

- `cli` — Command-line tool; produces a `main` entry point, flag parsing stub, and release-oriented CI
- `mcp-server` — MCP server binary; produces MCP transport scaffolding and appropriate CI
- `skill` — Claude Code `/command` skill; produces a `prompts/` layout and install instructions
- `library` — Importable package; produces library-oriented CI without a release pipeline
- `protocol` — Coordination or interface specification; produces a `docs/` and `prompts/` layout with no source

**3. Distribution**

- `homebrew` — Generates goreleaser config targeting macOS (arm64, amd64) and Linux (amd64, arm64), plus a homebrew formula stub; only available for `go` and `rust` CLI or MCP server projects
- `none` — CI publishes artifacts or runs tests but does not wire a tap

## Usage with Claude Code

This skill ships as a `/scaffold` command for [Claude Code](https://docs.anthropic.com/en/docs/claude-code).

### Install

Copy the skill to your global commands directory:

```bash
cp prompts/scaffold.md ~/.claude/commands/scaffold.md
```

### Invoke

```
/scaffold
```

The skill will ask for:

1. Project name and destination path
2. Language (`go`, `rust`, or `markdown`)
3. Project type (`cli`, `mcp-server`, `skill`, `library`, or `protocol`)
4. Distribution (`homebrew` or `none`)

It will then produce all files, initialize the git repository, and report exactly what was created.

### Non-interactive (one-liner)

```
/scaffold name=myproject path=~/code/myproject lang=go type=cli dist=homebrew
```

All four parameters can be passed inline to skip the interactive prompts.

## Output Example

For `lang=go type=cli dist=homebrew`, the skill produces:

```
~/code/myproject/
  .github/
    workflows/
      release.yml
  cmd/
    myproject/
      main.go
  go.mod
  .goreleaser.yaml
  README.md
  CLAUDE.md
```

And registers a formula stub in the configured homebrew tap repository.

## License

[MIT](LICENSE)
