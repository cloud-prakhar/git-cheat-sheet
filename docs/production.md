# Production Best Practices

## Why Production Needs Special Discipline

In development, a bad commit is an inconvenience — you revert it and move on. In production, a bad commit can mean downtime, data loss, a security breach, or a compliance violation. The practices below exist because production has no undo button that doesn't cost something.

The underlying principles:
- **`main` is always deployable** — if you can't deploy it right now, it shouldn't be on `main`.
- **Every change is reviewed** — no direct commits to protected branches.
- **History is an audit trail** — clean, signed, meaningful commits that explain *why*, not just *what*.
- **Secrets never enter the repo** — not even for a moment, not even in a reverted commit.

---

## Branch Protection

Configure these rules on GitHub/GitLab for `main` (and any `release/*` branches):

| Rule | Why |
|------|-----|
| Require pull request before merging | No one pushes directly to `main` |
| Require N approving reviews | At least one other person has read the code |
| Dismiss stale reviews on new push | A new commit invalidates previous approvals |
| Require status checks to pass | CI must be green before merge is allowed |
| Require branches to be up to date | No merging behind-HEAD branches |
| Restrict force pushes | Prevents history rewriting on shared branches |
| Restrict deletions | Prevents accidental branch deletion |

```bash
# Enforce via GitHub CLI (example)
gh api repos/{owner}/{repo}/branches/main/protection \
  --method PUT \
  --field required_pull_request_reviews='{"required_approving_review_count":1,"dismiss_stale_reviews":true}' \
  --field required_status_checks='{"strict":true,"contexts":["ci/tests"]}' \
  --field enforce_admins=true \
  --field restrictions=null
```

---

## Commit Signing (GPG / SSH)

Unsigned commits let anyone impersonate any author — the name and email in a commit are self-reported. Signed commits are cryptographically verified, creating a tamper-evident audit trail.

```bash
# --- SSH signing (simpler, recommended for new setups) ---

# Tell Git to use SSH for signing
git config --global gpg.format ssh
git config --global user.signingKey ~/.ssh/id_ed25519.pub

# Sign all commits by default
git config --global commit.gpgsign true
git config --global tag.gpgsign true

# --- GPG signing (traditional, required for some compliance frameworks) ---

# List your GPG keys
gpg --list-secret-keys --keyid-format=long

# Set the signing key
git config --global user.signingkey <KEY-ID>
git config --global commit.gpgsign true
git config --global tag.gpgsign true

# Verify a commit's signature
git log --show-signature -1

# Verify a tag
git tag -v v1.0.0
```

> On GitHub, go to Settings → SSH and GPG keys → add your public key, then enable "Vigilant mode" to mark unsigned commits as unverified.

---

## Branching Strategy for Production

### Trunk-Based Development (recommended for CI/CD teams)

```
main  ──●──●──●──●──●──●──  (always deployable)
         └──● feature (short-lived, merged in < 2 days)
```

- One long-lived branch: `main`.
- Feature branches are short-lived (hours to 2 days), merged via PR.
- Releases are cut from `main` using tags, not branches.
- Incompletely-built features are hidden behind **feature flags**, not long-lived branches.

**Why:** Long-lived branches accumulate drift and lead to painful "big bang" merges. Frequent small merges keep integration pain near zero.

### Gitflow (for teams with scheduled releases)

```
main        ──────────────────●────────────────●──
                              ↑ v1.0           ↑ v1.1
release/1.0     ──────────●──●
                          ↑ RC
develop     ──●──●──●──●──●──●──●──●──●──●──●──
               └──● feature/x
```

- `main` — only ever contains tagged releases.
- `develop` — integration branch; all features merge here.
- `feature/*` — one per feature; branch from and merge back into `develop`.
- `release/*` — cut from `develop` when ready; only bug fixes allowed; merged into both `main` and `develop` when shipped.
- `hotfix/*` — branch from `main` for production emergencies; merged into both `main` and `develop`.

**Why:** Provides explicit release staging and a clear path for hotfixes. More overhead than trunk-based; worthwhile for teams that ship versioned software on a schedule.

---

## Never Commit Secrets

Secrets (API keys, tokens, passwords, private keys) committed to a repo are **permanently compromised** — even if reverted, the history still contains them and may be cloned or cached.

**Prevention:**

```bash
# .gitignore — always exclude these patterns
.env
.env.*
!.env.example        # keep the example template
*.pem
*.key
*_rsa
*_ed25519
secrets/
credentials.json
config/secrets.yml
```

```bash
# Use a pre-commit hook to scan for secrets before every commit
# Option 1: detect-secrets (pip install detect-secrets)
detect-secrets scan > .secrets.baseline
detect-secrets audit .secrets.baseline

# Add to .git/hooks/pre-commit (or manage with pre-commit framework)
detect-secrets-hook --baseline .secrets.baseline

# Option 2: gitleaks (go-based, very fast)
gitleaks protect --staged   # scan staged changes
gitleaks detect             # scan full repo history
```

**If a secret is already committed:**

```bash
# 1. Revoke/rotate the secret immediately — assume it's compromised.

# 2. Remove from history using git filter-repo (preferred over BFG)
pip install git-filter-repo
git filter-repo --path secrets.json --invert-paths
git filter-repo --replace-text <(echo 'ghp_actualtoken==>REMOVED')

# 3. Force-push all branches and tags (coordinate with the whole team)
git push --force-with-lease origin --all
git push --force-with-lease origin --tags

# 4. All team members must re-clone — their local copies still have the secret.
```

> **Never rely on revert alone.** The secret exists in the git object database and in any clones or forks made before the revert.

---

## Merge Strategy for Pull Requests

Pick one strategy and enforce it consistently via branch protection settings.

| Strategy | History | When to use |
|----------|---------|-------------|
| **Squash merge** | One commit per PR on `main` | Most common for trunk-based. Clean log, easy to revert a feature in one step. |
| **Rebase merge** | All PR commits, linear | When individual commits are meaningful and well-structured. |
| **Merge commit** | Full branch topology preserved | When you need to see exactly when a branch started and ended (Gitflow). |

```bash
# Squash merge locally (same as GitHub "Squash and merge")
git switch main
git merge --squash feature/login
git commit -m "feat: add login feature (#42)"

# Rebase merge locally (same as GitHub "Rebase and merge")
git switch feature/login
git rebase main
git switch main
git merge --ff-only feature/login
```

---

## Atomic Commits

Each commit should do **one thing** and leave the repo in a working state. This makes `git bisect`, `git revert`, and code review dramatically easier.

```bash
# Bad — mixes unrelated changes
git commit -m "fix login bug and refactor user model and update deps"

# Good — separate, focused commits
git commit -m "fix: prevent null pointer in login when session expires"
git commit -m "refactor: extract user model validation into separate module"
git commit -m "chore: bump express to 4.18.3"
```

**Test the atomicity rule:** can you revert this one commit without breaking unrelated things? If not, it's not atomic.

---

## Pre-commit Hooks

Catch problems before they reach the remote. Run hooks via the [pre-commit](https://pre-commit.com) framework so they're version-controlled and reproducible across the team.

```yaml
# .pre-commit-config.yaml — commit this file to the repo
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.5.0
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-merge-conflict
      - id: check-added-large-files   # blocks files > 500KB by default
      - id: no-commit-to-branch
        args: [--branch, main]        # prevents direct commits to main

  - repo: https://github.com/Yelp/detect-secrets
    rev: v1.4.0
    hooks:
      - id: detect-secrets
```

```bash
# Install and activate
pip install pre-commit
pre-commit install          # installs the hook into .git/hooks/pre-commit
pre-commit run --all-files  # run manually against all files
```

---

## Release Tagging

Every production deployment should be tied to an annotated tag. This gives you:
- An exact, immutable snapshot to roll back to.
- A trigger point for CI/CD pipelines.
- A permanent audit record of who released what and when.

```bash
# Cut a release
git switch main
git pull origin main

# Create a signed, annotated tag
git tag -s v2.1.0 -m "Release v2.1.0

- feat: add OAuth2 login
- fix: correct rate-limit header parsing
- chore: upgrade openssl to 3.2.1"

# Push the tag to trigger the release pipeline
git push origin v2.1.0
```

```bash
# Roll back to the previous release (creates a new commit — doesn't rewrite history)
git revert v2.1.0..HEAD
git tag -s v2.1.1 -m "Revert to v2.0.3 state pending investigation"
git push origin v2.1.1
```

---

## Hotfix Procedure

When a critical bug is found in production:

```bash
# 1. Branch from the production tag, not from develop or main-head
git switch -c hotfix/v2.1.1 v2.1.0

# 2. Apply the minimal fix — nothing else
git commit -m "fix: prevent SQL injection in user search endpoint"

# 3. Tag and push — triggers deployment pipeline
git tag -s v2.1.1 -m "Hotfix: SQL injection in user search"
git push origin hotfix/v2.1.1
git push origin v2.1.1

# 4. Merge the fix back into main AND develop (or trunk)
git switch main
git merge --no-ff hotfix/v2.1.1
git push origin main

git switch develop
git merge --no-ff hotfix/v2.1.1
git push origin develop

# 5. Delete the hotfix branch
git branch -d hotfix/v2.1.1
git push origin --delete hotfix/v2.1.1
```

---

## .gitignore Hygiene

A poorly-maintained `.gitignore` is a liability — it's how secrets and large files sneak in.

```bash
# Generate a language-specific .gitignore base from gitignore.io
# (or use GitHub's template on repo creation)

# Audit what's currently being ignored
git check-ignore -v *

# Find accidentally tracked files that should be ignored
git ls-files --others --ignored --exclude-standard

# Stop tracking a file that's now in .gitignore (keeps local copy)
git rm --cached secrets.json
git rm --cached -r build/

# Verify a specific file is ignored before adding it
git check-ignore -v path/to/file
```

**Always review before the first commit:**
```bash
# See everything git would track in a fresh clone
git ls-files
```

---

## Audit & Compliance Commands

Useful when you need to answer "who changed what and when" for a security or compliance review.

```bash
# Full signed commit log with author and date
git log --show-signature --pretty=format:"%H %G? %aN %aI %s" main

# All changes to a sensitive file, with full diffs
git log -p --follow -- config/database.yml

# Who has touched a file in the last 6 months
git log --since="6 months ago" --pretty="%aN" -- src/auth/ | sort | uniq -c | sort -rn

# Every merge into main (release history)
git log --merges --first-parent main --oneline

# Verify all commits on main are signed
git log --show-signature main | grep -E "^(commit|gpg:)"
```
