# Diagrams

A central collection of the **Mermaid diagrams** used across this repository, gathered for quick visual reference and reuse.

## Why Diagrams?

A good picture cements a concept faster than paragraphs. Git is a graph of commits, branches, and pointers — and the fastest way to *see* what a command does is to look at the shapes it makes. These diagrams visualize how Git's pieces fit together.

## Contents

- [mermaid-diagrams.md](./mermaid-diagrams.md) — all key diagrams in one place.

## How to View

Mermaid renders automatically on GitHub. To edit/preview elsewhere, use the [Mermaid Live Editor](https://mermaid.live/).

## Diagrams Included

| Diagram | Concept | Source Module |
|---------|---------|---------------|
| The four areas | Working dir → staging → local → remote | 02 |
| The three trees | Working tree / index / HEAD | 02 |
| Branch as a pointer | Branches are movable labels on commits | 03 |
| Fast-forward vs `--no-ff` | Two ways a merge lands | 03 |
| Merge-conflict flow | Conflict → resolve → commit | 03 |
| Rebase before/after | Replaying commits onto a new base | 04 |
| Merge vs rebase | Preserved vs linear history | 04 |
| Remote data flow | fetch / pull / push | 05 |
| `origin/main` snapshot | Remote-tracking branches | 05 |
| Stash flow | Shelve → restore work | 06 |
| `git log` history view | Walking the commit graph | 07 |
| Reset modes | `--soft` / `--mixed` / `--hard` | 08 |
| Revert vs reset | Safe undo vs history rewrite | 08 |
| Tag timeline | Marking release points | 09 |
| Bisect search | Binary search for a bad commit | 10 |
| Fork & PR flow | upstream / origin / PR | 11 |
| Feature-branch flow | branch → PR → merge | 11 |
| cherry-pick | Copy one commit to another branch | 12 |
| Worktrees | One `.git`, many working dirs | 12 |
| Git LFS | Pointer in history, blob on server | 12 |
| Hotfix from a tag | Branch from release, fix, merge back | 13 |
| Trunk-based vs Gitflow | Two branching strategies | 13 |

<!-- NAV-FOOTER -->

---

### 🧭 Navigation

| Previous | Up | Next |
|:---|:---:|---:|
| ⬅️ Prev: [Production Best Practices](../13-production/production.md) | ⬆️ Home: [Git Cheat Sheet](../README.md) | ➡️ Next: [Mermaid Diagrams — Visual Reference](mermaid-diagrams.md) |
