# Configuration

## Why Configure Git?

Out of the box, Git doesn't know who you are. Every commit records an author name and email — if you skip this step, your contributions are attributed to nothing or to whatever the OS default is, making `git blame` and `git log --author` useless for finding your own work.

Beyond identity, configuration controls behavior that directly affects your workflow:
- The **default editor** determines what opens when you write a commit message or resolve a rebase — pick one you're comfortable in, or you'll be stuck in Vim with no way out.
- **Line ending handling** prevents Windows CRLF endings from showing up as changes in every file when collaborating cross-platform.
- **Aliases** compress commands you run dozens of times per day (`git lg`, `git sw`) into muscle memory.
- **Credential storage** means you authenticate once instead of on every push.

Git reads config in three layers — system → global → local — and later layers override earlier ones. This lets you set personal defaults globally while overriding them for specific repos (e.g., using a work email in work repos, personal email in personal repos).

---

Git config has three scopes: `--system` (all users), `--global` (your user account), and local (per-repo, default). Most personal settings go in `--global`.

---

## Identity

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

---

## Core Settings

```bash
# Default branch name for new repos
git config --global init.defaultBranch main

# Preferred editor for commit messages, rebase, etc.
git config --global core.editor "code --wait"   # VS Code
git config --global core.editor "vim"
git config --global core.editor "nano"

# Colorize output
git config --global color.ui auto

# Handle line endings (prevents CRLF issues)
git config --global core.autocrlf true    # Windows
git config --global core.autocrlf input   # macOS / Linux
```

---

## Credential Storage

Avoids re-entering your password on every push/pull.

```bash
git config --global credential.helper store        # Linux — saves in plaintext ~/.git-credentials
git config --global credential.helper osxkeychain  # macOS — saves in Keychain
git config --global credential.helper manager      # Windows — uses Git Credential Manager
```

---

## Viewing Config

```bash
# Show all active settings and where they come from
git config --list --show-origin

# Show a single value
git config user.name
git config user.email

# Open the global config file in your editor
git config --global --edit
```

---

## Useful Aliases

Aliases shorten frequently used commands. Define them once, use them everywhere.

```bash
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.sw switch
git config --global alias.br branch
git config --global alias.lg "log --oneline --graph --decorate --all"
git config --global alias.undo "reset HEAD~1 --mixed"
git config --global alias.unstage "restore --staged"
git config --global alias.last "log -1 HEAD --stat"
git config --global alias.aliases "config --get-regexp alias"
```

**Usage examples:**

```bash
git st            # git status
git lg            # pretty graph log
git undo          # undo last commit, keep changes unstaged
git unstage file  # remove file from staging area
git aliases       # list all defined aliases
```
