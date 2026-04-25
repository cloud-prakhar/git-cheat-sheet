# History & Diffing

Explore what changed, when, by whom, and why.

---

## git log

```bash
# Full log (newest first)
git log

# One commit per line
git log --oneline

# Branch graph — shows how branches diverge and merge
git log --oneline --graph --decorate --all

# Last N commits
git log -5

# Show what changed in each commit (full patch)
git log -p

# Show change statistics per commit
git log --stat

# Compact stats — just filenames and change counts
git log --shortstat
```

### Filtering the Log

```bash
# By author (partial match works)
git log --author="Prakhar"

# By date range
git log --since="2024-01-01" --until="2024-12-31"
git log --since="2 weeks ago"

# By commit message keyword
git log --grep="authentication"

# By content — commits that added or removed a string
git log -S "getUserById"

# By file — only commits that touched a specific file
git log -- src/auth.js

# Follow a renamed file through history
git log --follow -- src/old-name.js

# Commits in branch-a that aren't in branch-b
git log main..feature/login --oneline

# Commits reachable from either branch but not both
git log main...feature/login --oneline --left-right
```

---

## git show

Inspect a specific commit or object.

```bash
# Show the most recent commit
git show HEAD

# Show a specific commit
git show a1b2c3

# Show a commit 2 steps before HEAD
git show HEAD~2

# Show only the changed files, not the full diff
git show --stat HEAD

# Show a specific file as it was at a commit
git show a1b2c3:src/auth.js
```

---

## git diff

```bash
# Unstaged changes (working directory vs index)
git diff

# Staged changes (index vs last commit)
git diff --staged
git diff --cached    # same thing

# Compare two branches
git diff main feature/login

# Compare two commits
git diff a1b2c3 d4e5f6

# Diff a specific file
git diff HEAD -- src/auth.js

# Show only file names that changed (no diff content)
git diff --name-only main feature/login

# Show files with their change summary
git diff --stat main feature/login
```

---

## git blame

Shows who last changed each line of a file and in which commit. Useful for understanding context or tracking down when a bug was introduced.

```bash
# Annotate every line with author and commit
git blame src/auth.js

# Limit to a specific line range
git blame -L 15,40 src/auth.js

# Ignore whitespace-only changes
git blame -w src/auth.js

# Show commit hash in short form
git blame --abbrev=7 src/auth.js

# Detect lines moved or copied within the file
git blame -M src/auth.js

# Detect lines moved or copied from other files in the same commit
git blame -C src/auth.js
```

**Reading the output:**
```
a1b2c3ef (Prakhar Gupta 2024-03-10 14:22:01 +0530 42) const token = jwt.sign(payload, secret);
^commit   ^author        ^date                    ^line ^content
```
