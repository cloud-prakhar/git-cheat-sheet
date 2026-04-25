# Rebasing

Rebase replays your commits on top of another branch, producing a clean linear history instead of a merge commit. It's commonly used to keep feature branches up to date with `main`.

---

## Basic Rebase

```bash
# Rebase current branch onto main
git rebase main

# Rebase a named branch onto main (without switching to it first)
git rebase main feature/login
```

---

## Step-by-Step: Rebase a Feature Branch onto Main

```bash
# 1. Update main
git switch main
git pull origin main

# 2. Switch to your feature branch
git switch feature/login

# 3. Rebase onto the updated main
git rebase main
```

If there are **no conflicts**, the rebase completes silently and your branch history is now on top of main.

If there are **conflicts**, Git pauses at each conflicting commit:

```bash
# 3a. Resolve conflicts in the marked files (remove <<<, ===, >>> markers)

# 3b. Stage resolved files
git add src/auth.js

# 3c. Continue to the next commit
git rebase --continue

# --- Other rebase controls ---

# Skip the current conflicting commit entirely (rarely needed)
git rebase --skip

# Abort and return to the state before the rebase started
git rebase --abort
```

```bash
# 4. Force-push the rebased branch
#    Required because rebase rewrites commit history
#    --force-with-lease is safer than --force: it fails if the remote
#    has new commits you haven't seen, preventing accidental overwrites
git push --force-with-lease origin feature/login
```

> Never rebase branches that other people are working on — rewriting shared history causes problems for everyone.

---

## Interactive Rebase

Interactive rebase lets you edit, reorder, squash, or drop individual commits before sharing them. It's great for cleaning up a messy local history into a tidy set of commits.

```bash
# Open the last 3 commits for editing
git rebase -i HEAD~3

# Rebase from the point where the branch diverged from main
git rebase -i origin/main
```

Git opens an editor listing your commits oldest-first:

```
pick a1b2c3 feat: add login form
pick d4e5f6 fix: form validation
pick g7h8i9 fix: typo in error message
```

Change `pick` to one of these actions:

| Keyword  | Short | What it does |
|----------|-------|--------------|
| `pick`   | `p`   | Keep the commit unchanged |
| `reword` | `r`   | Keep the commit, edit its message |
| `edit`   | `e`   | Pause here to amend the commit (files + message) |
| `squash` | `s`   | Fold into the previous commit, combine messages |
| `fixup`  | `f`   | Fold into the previous commit, discard this message |
| `drop`   | `d`   | Remove the commit entirely |

Save and close the editor — Git replays the commits according to your instructions.

---

### Common Patterns

**Squash last 3 commits into one clean commit:**

```
pick a1b2c3 feat: add login form
squash d4e5f6 fix: form validation
squash g7h8i9 fix: typo in error message
```

Git prompts you for the final combined commit message.

---

**Fix a typo in an old commit message:**

```
pick a1b2c3 feat: add login form
reword d4e5f6 fix: form validation   ← Git will prompt for new message
pick g7h8i9 fix: typo in error message
```

---

**Drop a commit you don't want:**

```
pick a1b2c3 feat: add login form
drop d4e5f6 fix: debugging junk       ← removed entirely
pick g7h8i9 fix: typo in error message
```

---

## Rebase vs Merge

| | Rebase | Merge |
|---|---|---|
| History | Linear | Preserves branch topology |
| Merge commit | No | Yes (with `--no-ff`) |
| Rewrites history | Yes | No |
| Safe for shared branches | No | Yes |
| Best for | Cleaning up local history | Integrating shared branches |
