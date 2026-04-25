# Advanced Commands

Less common but powerful commands for specific situations.

---

## cherry-pick — Apply a Specific Commit

Copies a commit from one branch and applies it to another, creating a new commit with the same changes.

```bash
# Apply a single commit to the current branch
git cherry-pick a1b2c3

# Apply a range of commits (oldest to newest, inclusive)
git cherry-pick a1b2c3^..d4e5f6

# Apply without auto-committing — stage the changes first, then commit manually
git cherry-pick -n a1b2c3
git commit -m "cherry-picked: fix null check from hotfix branch"

# If a conflict occurs during cherry-pick
git add resolved-file.txt
git cherry-pick --continue

# Abort the cherry-pick
git cherry-pick --abort
```

**Common use case:** A critical bug was fixed on `main` and needs to be backported to a `release/1.x` maintenance branch.

---

## reflog — Your Safety Net

`reflog` records every position HEAD has been in, even across resets, rebases, and branch deletions. Entries expire after 90 days.

```bash
# View full HEAD history
git reflog

# Output:
# a1b2c3 HEAD@{0}: commit: feat: add signup
# d4e5f6 HEAD@{1}: rebase (finish): returning to refs/heads/feature/login
# g7h8i9 HEAD@{2}: reset: moving to HEAD~2

# Jump back to a previous state
git reset --hard HEAD@{3}

# Recover a deleted branch
git checkout -b recovered-branch HEAD@{5}

# View reflog for a specific branch
git reflog show feature/login
```

---

## Worktrees — Multiple Working Directories

Check out multiple branches simultaneously in separate directories — no stashing required, no extra clones.

```bash
# Create a new worktree for a branch
git worktree add ../hotfix hotfix/critical-bug

# Create a worktree and a new branch at the same time
git worktree add -b experiment ../experiment-dir main

# List all worktrees
git worktree list
# /home/user/repo           a1b2c3 [main]
# /home/user/hotfix         d4e5f6 [hotfix/critical-bug]

# Remove a worktree (does not delete the branch)
git worktree remove ../hotfix

# Remove and force (if there are uncommitted changes)
git worktree remove --force ../hotfix

# Clean up stale worktree references
git worktree prune
```

**Common use case:** Reviewing a PR in `../review-dir` while continuing feature work in the main directory.

---

## Submodules

Embed another Git repo inside your repo as a subdirectory. Useful for shared libraries.

```bash
# Add a submodule
git submodule add https://github.com/user/lib.git libs/lib

# Clone a repo along with all its submodules
git clone --recurse-submodules https://github.com/user/repo.git

# Initialize and populate submodules after a plain clone
git submodule update --init --recursive

# Update all submodules to their latest remote commit
git submodule update --remote --merge

# Run a command in each submodule
git submodule foreach git pull origin main

# Remove a submodule
git submodule deinit libs/lib
git rm libs/lib
rm -rf .git/modules/libs/lib
```

> Submodules pin to a specific commit, not a branch. After updating, commit the parent repo to record the new pointer.

---

## Useful One-Liners

```bash
# See which local branches are fully merged into main (safe to delete)
git branch --merged main

# See which branches are NOT yet merged
git branch --no-merged main

# Remove all local branches already merged into main
git branch --merged main | grep -v "^\* \|main\|develop" | xargs git branch -d

# Clean untracked files — always dry-run first
git clean -nd        # dry run: shows what would be removed
git clean -fd        # remove untracked files and directories
git clean -fdx       # also remove ignored files (e.g. build artifacts)

# Export repo contents as a zip (no .git folder)
git archive --format=zip HEAD > release.zip
git archive --format=tar.gz HEAD > release.tar.gz

# Find all commits in branch-a that aren't in branch-b
git log branch-b..branch-a --oneline

# Count commits per author across the whole history
git shortlog -sn --all

# Show the remote URL
git remote get-url origin

# See the size of your repo's objects
git count-objects -vH

# Find large files in history
git rev-list --objects --all | git cat-file --batch-check='%(objecttype) %(objectname) %(objectsize) %(rest)' | sort -k3 -rn | head -10
```
