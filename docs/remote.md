# Remote Operations

Remotes are named references to repositories hosted elsewhere (GitHub, GitLab, Bitbucket, self-hosted). `origin` is the conventional name for the repo you cloned from.

---

## Managing Remotes

```bash
# List remotes with their URLs
git remote -v

# Add a remote
git remote add origin https://github.com/user/repo.git

# Add a second remote (common in fork workflows)
git remote add upstream https://github.com/original/repo.git

# Change a remote's URL (e.g. switching from HTTPS to SSH)
git remote set-url origin git@github.com:user/repo.git

# Rename a remote
git remote rename origin old-origin

# Remove a remote
git remote remove upstream
```

---

## Fetching

Fetch downloads remote changes into your local repo but does **not** modify your working directory or branches. Safe to run at any time.

```bash
# Fetch updates from origin
git fetch origin

# Fetch a specific branch
git fetch origin main

# Fetch from all remotes
git fetch --all

# Fetch and remove local references to deleted remote branches
git fetch --prune
```

After fetching, you can inspect what changed before merging:

```bash
git log HEAD..origin/main --oneline   # commits on remote not yet in local main
git diff HEAD origin/main             # diff between local and remote main
```

---

## Pulling

Pull = fetch + merge (or fetch + rebase with `--rebase`).

```bash
# Fetch and merge remote main into current branch
git pull origin main

# Fetch and rebase instead of merge (keeps history linear)
git pull --rebase origin main

# Pull with fast-forward only — fails if a merge commit would be needed
git pull --ff-only origin main
```

---

## Pushing

```bash
# Push current branch to origin
git push origin feature/login

# Push and set upstream tracking (only needed the first time)
git push -u origin feature/login
# After this, plain `git push` works for this branch

# Push all local branches
git push --all origin

# Push all tags
git push --tags

# Delete a remote branch
git push origin --delete feature/login
```

### Force Pushing

Needed after a rebase or history rewrite. Always prefer `--force-with-lease`.

```bash
# Safe force push — fails if someone else pushed new commits you haven't fetched
git push --force-with-lease origin feature/login

# Unsafe force push — overwrites remote regardless (use only when you're certain)
git push --force origin feature/login
```

---

## Tracking Branches

A tracking relationship lets Git know which remote branch your local branch corresponds to, enabling plain `git push` / `git pull`.

```bash
# Set upstream for the current branch
git branch --set-upstream-to=origin/main main

# Check tracking relationships
git branch -vv
# main   a1b2c3 [origin/main] latest commit message
# feat   d4e5f6 [origin/feat: ahead 2] local work

# ahead N  — you have N commits not yet pushed
# behind N — remote has N commits not yet pulled
```
