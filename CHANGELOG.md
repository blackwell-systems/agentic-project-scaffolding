# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [v0.1.0] - 2026-03-01

### Added

- Initial release of the agentic-project-scaffolding skill
- Scope explicitly limited to structural scaffold: no build systems, no language tooling, no package manifests, no distribution config
- 12-step pipeline: create directory, git init, LICENSE, .gitignore, README.md stub, CLAUDE.md stub, CI workflow, type-specific directory structure, CODEOWNERS, initial commit, GitHub repo creation, branch protection
- Five project types with distinct directory layouts and README/CLAUDE.md section structures: `cli`, `mcp-server`, `library`, `skill`, `protocol`; plus `other` for generic repos with no type-specific structure
- `.gitignore` tailored by type: build artifact patterns for `cli`/`mcp-server`/`library`, OS-only patterns for `skill`/`protocol`/`other`
- CI workflow skeleton (`yes`/`no`): GitHub Actions workflow with push and pull request triggers on `main`; stub build and test steps for the developer to fill in
- CLAUDE.md stub with four sections: what the project does, structure, build and test commands, and important conventions
- CODEOWNERS file set to `* @<org>` at `.github/CODEOWNERS`
- Branch protection ruleset applied via GitHub API, hardcoded to the blackwell-systems standard policy: non-fast-forward, creation, update, deletion, required linear history, required signatures, and pull request rules requiring one approving review and code owner review
- Gather all inputs at once before proceeding; confirm with one-line summary before executing any steps; no mid-run interruptions after confirmation
- Supports inline arguments (`name=`, `path=`, `type=`, `ci=`) or interactive prompting
- Error handling: stops and asks on non-empty directory, unauthenticated `gh`, or failed repo creation; branch protection failure reported as warning without failing the scaffold
