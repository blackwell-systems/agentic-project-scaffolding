<!-- agentic-project-scaffolding v0.1.0 -->
Project Scaffolding

You are setting up a new project repository. Your goal is to eliminate setup friction: one command, repo is ready to work in. Ask only what you need. Produce everything in one pass.

## Gather inputs

Ask for the following. Accept them inline if provided (e.g. `/scaffold name=myproject type=cli`), otherwise ask interactively — all at once, not one at a time:

1. **Project name** — used for the directory name, repo name, and README title
2. **Path** — where to create it (default: `~/code/<name>`)
3. **Type** — one of:
   - `cli` — command-line tool
   - `mcp-server` — MCP server
   - `library` — importable package
   - `skill` — Claude Code skill (prompt-based, lives in `prompts/`)
   - `protocol` — specification or coordination artifact (lives in `docs/` and `prompts/`)
   - `other` — generic repo, no type-specific structure
4. **GitHub org or username** — who owns the repo (default: infer from `gh api user --jq .login`)
5. **CI** — `yes` or `no` (default: `yes`)

Confirm the inputs with the user before proceeding. Show a one-line summary:

```
Creating: <org>/<name> at <path> [type: <type>] [CI: yes/no]
```

Wait for confirmation. If the user wants to change anything, ask again. Once confirmed, proceed without further interruption.

## Create the repository

Execute the following in order. Do not stop between steps unless a step fails.

### 1. Directory

```bash
mkdir -p <path>
cd <path>
```

### 2. Git

```bash
git init
git checkout -b main
```

### 3. LICENSE

Create `LICENSE` with the MIT license. Copyright line:

```
Copyright (c) <current year> Blackwell Systems / Dayna Blackwell
```

### 4. .gitignore

Create a minimal `.gitignore` appropriate to the project type:

- `cli` / `mcp-server` / `library`: ignore common build artifacts (`/bin/`, `/dist/`, `target/`, `*.o`, `*.out`, `node_modules/`, `__pycache__/`, `.env`)
- `skill` / `protocol` / `other`: ignore OS files only (`.DS_Store`, `Thumbs.db`)

### 5. README.md

Create a `README.md` with:

```markdown
# <name>

<one sentence description — ask the user if not obvious from the name and type>

## <appropriate section for type>

...

## License

[MIT](LICENSE)
```

Section structure by type:
- `cli`: What It Is, Install, Usage, License
- `mcp-server`: What It Is, Setup, Tools, License
- `library`: What It Is, Install, Usage, License
- `skill`: What It Is, Install, Usage, License
- `protocol`: What It Is, When To Use It, License
- `other`: What It Is, License

Stubs are fine. The README does not need to be complete — it needs to exist with the right structure.

### 6. CLAUDE.md

Create a `CLAUDE.md` with stubs for the sections Claude Code needs most:

```markdown
# <name>

## What this project does

<one sentence — same as README opener>

## Structure

<brief description of where things live — fill in after scaffold>

## Build and test

<commands to build and run tests — fill in after scaffold>

## Important conventions

<project-specific rules — fill in after scaffold>
```

### 7. CI workflow

If CI is `yes`, create `.github/workflows/ci.yml`:

```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Build
        run: echo "Add build command here"
      - name: Test
        run: echo "Add test command here"
```

The developer fills in the actual commands. The workflow structure and triggers are correct for any project.

### 8. Type-specific structure

Create the minimal directory structure for the project type:

- `cli`: `cmd/<name>/` directory with a `.gitkeep`
- `mcp-server`: `internal/` directory with a `.gitkeep`
- `library`: `<name>/` or `src/` directory with a `.gitkeep`
- `skill`: `prompts/` directory with a `.gitkeep`
- `protocol`: `docs/` and `prompts/` directories with `.gitkeep` files
- `other`: no additional structure

### 9. CODEOWNERS

Create `.github/CODEOWNERS`:

```
* @<org>
```

### 10. Initial commit

```bash
git add -A
git commit -m "init: <name>"
```

### 11. GitHub repo

```bash
gh repo create <org>/<name> --public --description "<one sentence from README>"
git remote add origin git@github.com:<org>/<name>.git
git push -u origin main
```

### 12. Branch protection

Apply a ruleset matching the standard blackwell-systems policy via the GitHub API:

```bash
gh api repos/<org>/<name>/rulesets --method POST --input - <<'EOF'
{
  "name": "main",
  "target": "branch",
  "enforcement": "active",
  "conditions": {
    "ref_name": {
      "exclude": [],
      "include": ["~DEFAULT_BRANCH", "~ALL"]
    }
  },
  "rules": [
    {"type": "non_fast_forward"},
    {"type": "creation"},
    {"type": "update"},
    {"type": "deletion"},
    {"type": "required_linear_history"},
    {"type": "required_signatures"},
    {
      "type": "pull_request",
      "parameters": {
        "required_approving_review_count": 1,
        "dismiss_stale_reviews_on_push": false,
        "required_reviewers": [],
        "require_code_owner_review": true,
        "require_last_push_approval": false,
        "required_review_thread_resolution": false,
        "allowed_merge_methods": ["merge", "squash", "rebase"]
      }
    }
  ],
  "bypass_actors": [
    {
      "actor_id": 5,
      "actor_type": "RepositoryRole",
      "bypass_mode": "always"
    }
  ]
}
EOF
```

## Report

When all steps are complete, emit a summary:

```
Created: <org>/<name>
Path:    <path>
Repo:    https://github.com/<org>/<name>

Files:
  LICENSE
  .gitignore
  README.md
  CLAUDE.md
  .github/CODEOWNERS
  .github/workflows/ci.yml   (if CI: yes)
  <type-specific dirs>

Branch protection: applied
Next: add build and test commands to .github/workflows/ci.yml and fill in CLAUDE.md
```

## Error handling

- If the directory already exists and is non-empty: stop and ask the user whether to proceed
- If `gh` is not authenticated: stop and report
- If `gh repo create` fails (name taken, permissions): stop and report
- If branch protection fails: report as a warning, do not fail the whole scaffold — the repo exists and the developer can apply it manually
- Do not attempt to fix errors automatically. Report and ask.
