# Stashing

## Why Stash?

Git won't let you switch branches when you have uncommitted changes that would conflict with the target branch. Your options without stash are to either commit half-done work (polluting history) or discard it (losing work). Neither is acceptable.

`git stash` gives you a **third option**: save the current state of your working directory and staging area to a temporary stack, revert to a clean state, and restore everything exactly as it was when you're ready to continue. It's the equivalent of putting your work-in-progress into a drawer so you can deal with something urgent on a clean desk.

**Common scenarios:**
- A critical production bug comes in while you're mid-feature — stash your work, fix the bug on `main`, then pop the stash.
- You started work on the wrong branch — stash it, switch to the right branch, pop the stash.
- You want to quickly test the current state of `main` without committing incomplete work.

---

Stash temporarily shelves your uncommitted changes so you can switch context — e.g., jump to a hotfix branch — and come back to your work later.

---

## Saving a Stash

```bash
# Stash tracked modified files (default)
git stash

# Stash with a descriptive message (strongly recommended)
git stash push -m "WIP: half-done login form"

# Include untracked (new) files in the stash
git stash push -u -m "WIP: new config file"

# Include untracked files AND ignored files
git stash push -a -m "WIP: everything"

# Stash only specific files
git stash push -m "WIP: auth changes" src/auth.js src/token.js

# Interactively choose which chunks to stash (patch mode)
git stash push -p
```

---

## Viewing Stashes

```bash
# List all stashes (newest first)
git stash list
# stash@{0}: WIP: half-done login form
# stash@{1}: On main: quick experiment

# Show a summary of what's in a stash
git stash show stash@{0}

# Show the full diff of a stash
git stash show -p stash@{0}
```

---

## Restoring a Stash

```bash
# Apply the most recent stash (leaves it in the stash list)
git stash apply

# Apply a specific stash
git stash apply stash@{1}

# Apply the most recent stash AND remove it from the list
git stash pop

# Apply a specific stash and remove it
git stash pop stash@{1}
```

> `apply` is safer than `pop` — it keeps the stash in case the apply causes conflicts you need to sort out.

---

## Cleaning Up Stashes

```bash
# Remove a specific stash
git stash drop stash@{1}

# Remove all stashes
git stash clear
```

---

## Creating a Branch from a Stash

Useful when the stash no longer cleanly applies to the current branch.

```bash
# Creates a new branch at the commit where the stash was made, applies the stash, then drops it
git stash branch feature/login stash@{0}
```

---

## Typical Workflow

```bash
# You're mid-feature and need to fix a bug on main
git stash push -m "WIP: new nav bar"

git switch main
git switch -c hotfix/crash-on-login
# ... fix and commit the bug ...
git switch main
git merge hotfix/crash-on-login

# Return to feature work
git switch feature/nav
git stash pop
```
