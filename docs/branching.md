# Branching & Merging

## Why Branches?

In a shared codebase, `main` (or `master`) should always be in a working, deployable state. If everyone committed directly to `main`, any in-progress feature or broken experiment would immediately break the project for the whole team.

Branches solve this by giving each developer (or each feature/fix) a **separate line of development**. Work happens in isolation, and changes are only merged back when they're complete and reviewed. This enables:

- **Parallel development** — multiple features in flight simultaneously without conflict.
- **Safe experimentation** — try a new approach on a branch; if it fails, delete the branch and nothing is lost.
- **Code review** — open a Pull Request from a branch, get feedback, then merge — keeping history clean and intentional.
- **Release management** — maintain stable `release/x.x` branches while `main` continues forward.

## Why Merge?

Once work on a branch is complete, it needs to be brought back into the shared history. Merging creates a point in history where two lines of development come together. The **merge commit** is explicit evidence that the feature was reviewed and integrated, making it easy to audit or revert an entire feature in one step with `git revert <merge-commit>`.

---

Branches let you work on features or fixes in isolation without affecting the main codebase.

---

## Branching

```bash
# List local branches (* marks the current branch)
git branch

# List remote branches
git branch -r

# List all branches (local + remote)
git branch -a

# Create a branch (stays on current branch)
git branch feature/login

# Switch to an existing branch
git switch feature/login
git checkout feature/login    # older syntax

# Create and switch in one step
git switch -c feature/signup
git checkout -b feature/signup  # older syntax

# Rename a branch
git branch -m old-name new-name
git branch -m new-name          # rename the current branch

# Delete a branch (only if fully merged)
git branch -d feature/login

# Force-delete an unmerged branch
git branch -D feature/login

# Delete a remote branch
git push origin --delete feature/login

# Track a remote branch locally
git switch --track origin/feature/login
```

---

## Merging

Merging integrates changes from one branch into another. Always merge into the branch you want to update.

```bash
# Switch to the target branch first
git switch main

# Merge a feature branch into main
git merge feature/login

# Merge with a merge commit even if fast-forward is possible
# Creates an explicit merge commit, preserving branch history
git merge --no-ff feature/login

# Squash all commits from the branch into one staged change (then commit manually)
git merge --squash feature/login
git commit -m "feat: add login feature"

# Abort a merge that's in progress
git merge --abort
```

### Fast-forward vs No-fast-forward

| | Fast-forward | `--no-ff` |
|---|---|---|
| History | Linear — looks like commits were on main all along | Preserves branch topology |
| Merge commit | No | Yes |
| Best for | Short-lived / solo branches | Team branches, features |

---

## Resolving Merge Conflicts

Conflicts happen when two branches changed the same lines differently. Git pauses and asks you to decide.

**Step-by-step:**

```bash
# 1. Attempt the merge
git merge feature/login
# CONFLICT (content): Merge conflict in src/auth.js

# 2. Open the conflicted file — Git marks the conflict zones:
# <<<<<<< HEAD
# your changes on main
# =======
# their changes from feature/login
# >>>>>>> feature/login

# 3. Edit the file — keep what's correct, remove all markers

# 4. Stage the resolved file
git add src/auth.js

# 5. Commit to complete the merge
git commit
# (Git pre-fills the merge commit message)

# --- OR, if you want to give up ---
git merge --abort
```

**Tips:**
- Use `git status` during a conflict to see which files still need resolution.
- VS Code, IntelliJ, and most editors have built-in merge conflict UI.
- `git diff` during a conflict shows the unresolved sections.
