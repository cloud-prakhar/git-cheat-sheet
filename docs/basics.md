# Basics

Core everyday commands: creating repos, checking status, staging, and committing.

---

## Repository Setup

```bash
# Initialize a new repo in the current directory
git init

# Initialize in a new directory
git init my-project

# Clone a remote repo into a new folder
git clone https://github.com/user/repo.git

# Clone into a custom-named folder
git clone https://github.com/user/repo.git my-folder

# Shallow clone — only latest snapshot, no full history (faster for CI)
git clone --depth 1 https://github.com/user/repo.git

# Clone a specific branch
git clone --branch develop https://github.com/user/repo.git
```

---

## Checking Status

```bash
# Show which files are staged, unstaged, or untracked
git status

# Compact output — one line per file
git status -s
# M  staged modification
#  M unstaged modification
# ?? untracked file
# A  newly staged file
```

---

## Staging

The staging area (index) lets you craft commits precisely — stage only what belongs together.

```bash
# Stage a specific file
git add file.txt

# Stage an entire directory
git add src/

# Stage everything in the current directory
git add .

# Interactively choose which chunks to stage (patch mode)
# Git shows each diff hunk — press y to stage, n to skip
git add -p

# Unstage a file (changes remain in working directory)
git restore --staged file.txt
```

---

## Committing

```bash
# Commit everything that's staged
git commit -m "feat: add login page"

# Stage all tracked files and commit in one step (skips new/untracked files)
git commit -am "fix: correct null check"

# Open editor to write a multi-line commit message
git commit

# Amend the last commit — fix the message
git commit --amend -m "fix: corrected message"

# Amend the last commit — add a forgotten file, keep the message
git add forgotten.txt
git commit --amend --no-edit

# Create an empty commit (useful to re-trigger CI pipelines)
git commit --allow-empty -m "chore: trigger pipeline"
```

> Amending rewrites history. Only amend commits that haven't been pushed to a shared branch.

---

## Commit Message Convention

A widely used format is [Conventional Commits](https://www.conventionalcommits.org/):

```
<type>: <short summary>

[optional body]
[optional footer]
```

Common types: `feat`, `fix`, `chore`, `docs`, `refactor`, `test`, `style`, `ci`

**Examples:**
```
feat: add password reset flow
fix: prevent crash when user list is empty
chore: bump dependency versions
docs: update API authentication guide
```
