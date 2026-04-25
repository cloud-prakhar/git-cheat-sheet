# Git Cheat Sheet

A practical, no-fluff Git reference covering essential commands, workflows, and real-world use cases — from beginners to advanced users.

---

## Topics

| # | Topic | What's inside |
|---|-------|---------------|
| 1 | [Installation](docs/installation.md) | Windows & Linux install methods |
| 2 | [Configuration](docs/configuration.md) | User setup, editor, credentials, aliases |
| 3 | [Basics](docs/basics.md) | init, clone, status, stage, commit |
| 4 | [Branching & Merging](docs/branching.md) | create, switch, merge, conflict resolution |
| 5 | [Rebasing](docs/rebasing.md) | basic rebase, interactive rebase, squashing |
| 6 | [Remote Operations](docs/remote.md) | fetch, pull, push, tracking, force-push |
| 7 | [Stashing](docs/stashing.md) | stash, pop, apply, list, drop |
| 8 | [History & Diffing](docs/history.md) | log filters, diff, blame, show |
| 9 | [Undoing Changes](docs/undoing.md) | restore, reset, revert, reflog |
| 10 | [Tags](docs/tags.md) | lightweight, annotated, push, delete |
| 11 | [Debugging](docs/debugging.md) | bisect, blame, log -S, log -L |
| 12 | [Workflows](docs/workflows.md) | fork/PR flow, feature branch flow |
| 13 | [Advanced](docs/advanced.md) | cherry-pick, worktrees, submodules, one-liners |
| 14 | [Production Best Practices](docs/production.md) | branch protection, signing, secrets, hotfix, release tagging |

---

## Quick Reference

| Task | Command |
|------|---------|
| Init repo | `git init` |
| Clone | `git clone <url>` |
| Stage all | `git add .` |
| Commit | `git commit -m "msg"` |
| Push | `git push origin <branch>` |
| Pull | `git pull origin <branch>` |
| New branch | `git switch -c <name>` |
| Switch branch | `git switch <name>` |
| Merge | `git merge <branch>` |
| Rebase onto main | `git rebase main` |
| Stash changes | `git stash push -m "msg"` |
| Pop stash | `git stash pop` |
| Undo last commit | `git reset --soft HEAD~1` |
| Discard file changes | `git restore <file>` |
| View graph log | `git log --oneline --graph --all` |
| Diff staged changes | `git diff --staged` |
| Create release tag | `git tag -a v1.0.0 -m "msg"` |
| Find bad commit | `git bisect start` |
| Recover lost work | `git reflog` |
