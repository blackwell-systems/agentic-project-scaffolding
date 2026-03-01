# Agentic Project Scaffolding

[![Blackwell Systems™](https://raw.githubusercontent.com/blackwell-systems/blackwell-docs-theme/main/badge-trademark.svg)](https://github.com/blackwell-systems)
[![Version](https://img.shields.io/github/v/release/blackwell-systems/agentic-project-scaffolding)](https://github.com/blackwell-systems/agentic-project-scaffolding/releases/latest)

A Claude Code skill that scaffolds new projects. It creates the repository structure, initializes git, and produces the files that every project needs before any real work begins — without touching build systems, package manifests, or language tooling.

## Why

Starting a new project involves a predictable set of decisions and a predictable set of files. Most of those files are boilerplate with a few project-specific values filled in. Doing it by hand is slow and inconsistent. This skill handles the structural decisions explicitly and produces exactly what it knows how to produce — stopping before it reaches the things that vary too much to generalize.

## Scope

The skill owns repo structure. It does not own build systems, package managers, language tooling, or distribution config. Those are decisions the developer makes after the repo exists.

**What it produces:**

- Directory at the specified path
- Git repository initialized with `main` as the default branch
- `README.md` stub
- `CLAUDE.md` stub with project-specific context sections
- `.gitignore`
- GitHub Actions CI skeleton
- `LICENSE` (MIT, Blackwell Systems)
- `.github/CODEOWNERS`
- GitHub repo created and configured with branch protection

**What it does not produce:**

- Build system config (`go.mod`, `Cargo.toml`, `package.json`, `pom.xml`, etc.)
- Language-specific source stubs
- Distribution config (goreleaser, cargo-dist, etc.)
- Package registry entries

Distribution wiring is handled separately by [homebrew-formula-updater](https://github.com/blackwell-systems/homebrew-formula-updater) and [github-release-engineer](https://github.com/blackwell-systems/github-release-engineer) when the project is ready to ship.

## Decision Points

The skill asks three questions before producing any output:

**1. Project name and path**

Where the project lives on disk and what it is called.

**2. Project type**

- `cli` — Command-line tool
- `mcp-server` — MCP server
- `library` — Importable package
- `skill` — Claude Code `/command` skill; produces a `prompts/` layout and install instructions
- `protocol` — Coordination or interface specification; produces a `docs/` and `prompts/` layout

The type drives the README structure, CLAUDE.md sections, and CI shape — not the build system.

**3. CI**

- `yes` — GitHub Actions workflow with lint, test, and build jobs (generic; the developer fills in the language-specific commands)
- `no` — No CI generated

## Install

```bash
cp prompts/scaffold.md ~/.claude/commands/scaffold.md
```

## Usage

```
/scaffold
```

The skill asks for project name, path, type, and CI preference, then produces all files and reports exactly what was created.

Non-interactive:

```
/scaffold name=myproject path=~/code/myproject type=cli ci=yes
```

## License

[MIT](LICENSE)
