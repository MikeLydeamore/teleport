# Teleport Action

Copy a folder from one repository (the caller) into another repository path and push it.

This is useful for syncing docs, examples, or other shared resources across repos.

---

## Usage

In your workflow, call this action:

```yaml
name: Teleport

on:
  workflow_dispatch: # or schedule, push, etc.

jobs:
  sync:
    runs-on: ubuntu-latest
    steps:
      - uses: MikeLydeamore/teleport-action@v1
        with:
          target_repo: "OWNER/TARGET_REPO"
          target_path: "path/in/target"
          source_path: "path/in/source"
          token: ${{ secrets.CROSS_REPO_PAT }}
```

---

## Inputs

| Name          |  Description                                          |
|---------------|-------------------------------------------------------|
| `target_repo` |  Full repo name to push into, e.g. `owner/my-repo`    |
| `source_path` |  Path in the **caller repo** to copy from             |
| `target_path` |  Path in the **target repo** to copy into             |
| `token`       |  PAT with repo access (see below)                     |

---

## Token requirements

You must create a **Personal Access Token** and store it as a secret in the **caller repo**.

- **Fine-grained PAT (recommended)**  
  - Target repo → `Contents: Read & Write`  
  - Source repo (if private) → `Contents: Read`  
- **Classic PAT**  
  - Scope: `repo`

Then reference it in your workflow like:

```yaml
token: ${{ secrets.CROSS_REPO_PAT }}
```

If you are copying between organisational repos, then a classic token will also need the `admin:org` scope.

---

## Copy semantics

- Trailing slash in `source_path` copies the **contents** of the folder.  
  - `source_path: docs/` → copies *files inside `docs/`*  
  - `source_path: docs` → copies the *entire `docs` folder*  

- `--delete` is enabled in `rsync`: files removed in source are also removed in target path.

---

## Commit details

- Commits are made with the identity:  
  - Name: `github-actions[bot]`  
  - Email: `41898282+github-actions[bot]@users.noreply.github.com`  
- Commit message includes source and target repo + paths.

---

## Example: Nightly sync

Run every night at 01:15 UTC:

```yaml
on:
  schedule:
    - cron: "15 1 * * *"

jobs:
  sync:
    runs-on: ubuntu-latest
    steps:
      - uses: MikeLydeamore/teleport-action@v1
        with:
          target_repo: "MikeLydeamore/sandbox"
          target_path: "test_target"
          source_path: "test"
          token: ${{ secrets.CROSS_REPO_PAT }}
```

---

## Notes

- If your target repo has **branch protection**, direct pushes may fail.  
  You’ll need to fork this action to open PRs instead of pushing.  
- The action runs entirely in the caller’s workflow runner — no external services.

---

## 📦 Versioning

- Use `@v1` for the latest stable v1.x release.  
- Pin to a full tag (`@v1.0.0`) or commit SHA for maximum supply-chain safety.

---

## 📄 License

MIT © Mike Lydeamore
