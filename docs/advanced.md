# Advanced Commands

## Why These Commands?

The core Git commands cover 90% of daily work. These advanced commands exist to solve problems that the basics can't handle elegantly — applying a single fix across multiple branches, working on two things at once without switching branches, embedding dependencies in a project, and pushing content that Git's object store isn't designed for.

---

## cherry-pick — Apply a Specific Commit

### Why cherry-pick?

Normally you integrate work by merging an entire branch. But sometimes you need *one specific commit* from another branch — a critical bug fix — without pulling in everything else that's on that branch.

Cherry-pick solves exactly this. It copies the *diff* of a commit and applies it as a new commit on your current branch. The original commit stays where it is; you get an independent copy.

**When to use it:**
- A bug was fixed on `main` and needs to be **backported** to a `release/1.x` support branch.
- A colleague's branch has one useful commit you need now, but their PR isn't ready to merge yet.
- You accidentally committed something to the wrong branch — cherry-pick it to the right one, then drop it from the wrong one.

**When NOT to use it:**
- If you need most of the work from a branch, merge or rebase instead — cherry-picking many commits creates duplicate history and makes `git log` confusing.

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

### Why worktrees?

Normally Git ties you to one working directory per clone. If you're mid-feature and need to check out a different branch — to review a PR, test a hotfix, or run the old version of the app — you either have to stash and switch, or maintain a second full clone (slow, uses double the disk space).

Worktrees let a single clone check out **multiple branches simultaneously**, each in its own directory on disk. Switching context becomes a `cd` instead of a stash + switch cycle. Each worktree shares the same `.git` database — so no duplication of history — but has its own independent working directory and index.

**When to use it:**
- Reviewing a colleague's PR in a separate directory while your own work continues untouched.
- Running a long test suite on `main` while developing on a feature branch.
- Comparing the behavior of two branches side by side.

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

### Why submodules?

Sometimes a project depends on another Git repository — a shared component library, a vendored dependency, a design system, a compiled tool. Copy-pasting the code in creates a maintenance problem: updates are manual and divergence is invisible. Using a package manager works when one exists, but not all dependencies have package registries.

Submodules allow your repo to **reference another Git repo at a specific commit**. The parent repo records the exact commit hash, not a floating branch, so your project is always reproducible — you always get exactly the dependency version you tested with.

**When to use submodules:**
- Sharing code between multiple repos that can't be packaged (e.g., internal tooling, configuration schemas).
- Vendoring a dependency where you want to track its history and contribute patches upstream.
- Embedding a documentation site, design assets, or generated artifacts from another team.

**When NOT to use submodules:**
- When a language package manager (npm, pip, cargo, go modules) already handles the dependency — it's simpler and more widely understood.
- When the team isn't already familiar with Git submodule workflows — the extra friction (remembering `--recurse-submodules`, pinned commits) causes consistent mistakes.

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

## Large Files — Git LFS

### Why Git LFS?

Git stores every version of every file in its object database forever. This is great for source code (text diffs are small) but disastrous for large binary files — images, videos, ML models, compiled binaries, audio files. Each new version of a 100 MB model adds 100 MB to the repo. After a year of development, `git clone` downloads gigabytes of historical binary blobs that developers never need.

**Git Large File Storage (LFS)** solves this by storing large files on a separate server (GitHub/GitLab both host LFS) and only keeping a tiny text **pointer** file in the Git history. Your working directory still has the real file; the repo history stays lean.

**When to use Git LFS:**
- Binary assets that change frequently: graphics, textures, audio, video.
- ML model weights (`.pt`, `.bin`, `.onnx`).
- Compiled releases, installers, or PDFs that are version-controlled.
- Any file over ~50 MB — GitHub hard-blocks files over 100 MB without LFS.

**Install Git LFS first:**

```bash
# macOS
brew install git-lfs

# Debian / Ubuntu
sudo apt install git-lfs

# Windows (via Scoop)
scoop install git-lfs

# Activate LFS for your user account (one-time)
git lfs install
```

**Track large file types:**

```bash
# Tell LFS to manage all PNG files
git lfs track "*.png"

# Track ML model files
git lfs track "*.pt" "*.bin" "*.onnx"

# Track a specific file
git lfs track "assets/video/demo.mp4"

# .gitattributes records the tracking rules — commit it
git add .gitattributes
git commit -m "chore: track large files with Git LFS"
```

**Normal workflow after tracking:**

```bash
# Add and commit as usual — LFS handles the rest transparently
git add assets/logo.png
git commit -m "chore: update brand logo"
git push origin main
# Git LFS uploads the actual file to LFS storage
# Git stores only the pointer in the repo
```

**Inspect LFS state:**

```bash
# List all files currently tracked by LFS in the repo
git lfs ls-files

# Show LFS status
git lfs status

# See which patterns are tracked
git lfs track

# View the pointer file stored in Git (not the actual content)
git show HEAD:assets/logo.png
# version https://git-lfs.github.com/spec/v1
# oid sha256:4d7a...
# size 204800
```

**Migrate existing large files to LFS (retroactively):**

```bash
# Move all existing PNGs in history into LFS storage
# WARNING: rewrites history — coordinate with the whole team
git lfs migrate import --include="*.png" --everything

# After migration, force-push all branches
git push --force-with-lease origin --all
git push --force-with-lease origin --tags
```

> After a `git lfs migrate`, every team member must re-clone or run `git lfs fetch --all` — their existing clones will have broken pointers.

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
