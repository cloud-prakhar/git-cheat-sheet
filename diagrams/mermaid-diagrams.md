# Mermaid Diagrams — Visual Reference

Every key diagram from the modules, collected in one place. Each renders automatically on GitHub; edit them in the [Mermaid Live Editor](https://mermaid.live/).

---

## Module 02 — Basics

### The Four Areas

The one diagram to remember: every command moves work between these four places.

```mermaid
flowchart LR
    WD["📝 Working Directory<br/>your files"] -- "git add" --> SA["📦 Staging Area<br/>next commit"]
    SA -- "git commit" --> LR["🗄️ Local Repo<br/>your history"]
    LR -- "git push" --> RR["☁️ Remote Repo<br/>shared (GitHub)"]
    RR -- "git fetch / pull" --> LR
    LR -- "git checkout / restore" --> WD
```

### The Three Trees

```mermaid
flowchart TD
    A["Working Tree<br/>(edits on disk)"] -- "git add" --> B["Index / Staging Area<br/>(next commit)"]
    B -- "git commit" --> C["HEAD<br/>(last commit)"]
    C -- "git restore --staged" --> B
    B -- "git restore" --> A
```

---

## Module 03 — Branching & Merging

### A Branch Is Just a Pointer

```mermaid
gitGraph
    commit id: "A"
    commit id: "B"
    branch feature
    checkout feature
    commit id: "C"
    commit id: "D"
    checkout main
    commit id: "E"
```

### Fast-Forward vs `--no-ff`

```mermaid
flowchart TD
    subgraph FF["Fast-forward (linear)"]
        A1[A] --> B1[B] --> C1[C]
    end
    subgraph NOFF["--no-ff (merge commit)"]
        A2[A] --> B2[B]
        A2 --> C2[C]
        B2 --> M2[Merge]
        C2 --> M2
    end
```

### Merge-Conflict Flow

```mermaid
flowchart LR
    A["git merge feature"] --> B{"Conflict?"}
    B -- "no" --> C["✅ Merge completes"]
    B -- "yes" --> D["Edit conflicted files<br/>(<<<< ==== >>>>)"]
    D --> E["git add <file>"]
    E --> F["git commit"]
    F --> C
```

---

## Module 04 — Rebasing

### Before / After a Rebase

```mermaid
flowchart LR
    subgraph Before
        m1[A] --> m2[B] --> m3[C]
        m1 --> f1[X] --> f2[Y]
    end
    subgraph After
        n1[A] --> n2[B] --> n3[C] --> n4["X'"] --> n5["Y'"]
    end
```

### Merge vs Rebase

```mermaid
flowchart TD
    subgraph MERGE["Merge — preserves history"]
        a[main] --> mc[Merge commit]
        b[feature] --> mc
    end
    subgraph REBASE["Rebase — linear history"]
        c[A] --> d[B] --> e["feat'"] --> f["feat'"]
    end
```

---

## Module 05 — Remote Operations

### Remote Data Flow

```mermaid
flowchart LR
    LOCAL["💻 Local Repo"] -- "git push" --> REMOTE["☁️ origin (GitHub)"]
    REMOTE -- "git fetch" --> TRACK["origin/main<br/>(remote-tracking)"]
    TRACK -- "git merge" --> LOCAL
    REMOTE -- "git pull = fetch + merge" --> LOCAL
```

### `origin/main` — A Local Snapshot of the Remote

```mermaid
flowchart LR
    A["origin/main<br/>(last fetched)"] -. "git fetch updates this" .-> B["main<br/>(your work)"]
    B -- "git push" --> C["origin on server"]
```

---

## Module 06 — Stashing

### Stash Flow

```mermaid
flowchart LR
    A["Dirty working dir"] -- "git stash push" --> B["🗄️ Stash stack<br/>(clean working dir)"]
    B -- "git stash pop" --> C["Changes restored"]
    B -- "git stash apply" --> C
    B -- "git stash drop" --> D["Discarded"]
```

---

## Module 07 — History & Diffing

### Walking the Commit Graph

```mermaid
gitGraph
    commit id: "init"
    commit id: "feat-a"
    branch feature
    checkout feature
    commit id: "feat-b"
    checkout main
    commit id: "fix-c"
    merge feature
    commit id: "release"
```

---

## Module 08 — Undoing Changes

### Reset Modes

```mermaid
flowchart TD
    R["git reset --MODE HEAD~1"] --> S{"Which mode?"}
    S -- "--soft" --> A["HEAD moves<br/>index kept · working dir kept"]
    S -- "--mixed (default)" --> B["HEAD moves<br/>index reset · working dir kept"]
    S -- "--hard" --> C["HEAD moves<br/>index reset · working dir RESET ⚠️"]
```

### Revert vs Reset

```mermaid
flowchart LR
    subgraph REVERT["git revert — safe, shared history"]
        a[A] --> b[B] --> c[C] --> d["D = undo of C"]
    end
    subgraph RESET["git reset — rewrites history"]
        e[A] --> f[B]
        f -. "C discarded" .-> g[gone]
    end
```

---

## Module 09 — Tags

### Tag Timeline

```mermaid
gitGraph
    commit
    commit tag: "v1.0.0"
    commit
    commit tag: "v1.1.0"
    commit
    commit tag: "v2.0.0"
```

---

## Module 10 — Debugging

### Bisect — Binary Search for a Bad Commit

```mermaid
flowchart LR
    A["git bisect start"] --> B["mark bad (now)"]
    B --> C["mark good (v1.0)"]
    C --> D["Git checks out the midpoint"]
    D --> E{"Test: good or bad?"}
    E -- "good" --> D
    E -- "bad" --> D
    E --> F["🎯 First bad commit found"]
    F --> G["git bisect reset"]
```

---

## Module 11 — Collaboration Workflows

### Fork & Pull Request

```mermaid
flowchart LR
    LOCAL["💻 Local clone"] -- "git push" --> FORK["🍴 Your fork (origin)"]
    FORK -- "Pull Request" --> UP["⭐ Upstream repo"]
    UP -- "git fetch upstream" --> LOCAL
```

### Feature-Branch Flow

```mermaid
flowchart LR
    A["git switch -c feature/x"] --> B["commit work"]
    B --> C["git push -u origin feature/x"]
    C --> D["Open Pull Request"]
    D --> E{"Review + CI passes?"}
    E -- "changes requested" --> B
    E -- "approved ✅" --> F["Merge into main"]
    F --> G["Delete feature branch"]
```

---

## Module 12 — Advanced

### cherry-pick

```mermaid
gitGraph
    commit id: "A"
    branch hotfix
    checkout hotfix
    commit id: "fix-X"
    commit id: "fix-Y"
    checkout main
    commit id: "B"
    cherry-pick id: "fix-X"
```

### Worktrees — One `.git`, Many Working Dirs

```mermaid
flowchart TD
    GIT[(".git database<br/>shared history")]
    GIT --- W1["📁 repo/ (main)"]
    GIT --- W2["📁 ../hotfix/ (hotfix/bug)"]
    GIT --- W3["📁 ../review/ (pr-42)"]
```

### Git LFS

```mermaid
flowchart LR
    A[Big binary asset] --> B[git lfs track '*.png']
    B --> C[git add + commit]
    C --> D[real file → LFS server]
    C --> E[tiny pointer → git history]
```

---

## Module 13 — Production

### Hotfix from a Tag

```mermaid
flowchart TD
    A["🔥 Bug in prod (v2.1.0)"] --> B["Branch from the TAG<br/>git switch -c hotfix/v2.1.1 v2.1.0"]
    B --> C["Apply minimal fix + commit"]
    C --> D["Tag v2.1.1 & push → deploy"]
    D --> E["Merge fix into main"]
    E --> F["Merge fix into develop/trunk"]
```

### Trunk-Based Strategy

```mermaid
gitGraph
    commit
    branch feature
    checkout feature
    commit
    checkout main
    merge feature tag: "deploy"
    commit
    branch feature2
    checkout feature2
    commit
    checkout main
    merge feature2 tag: "deploy"
```

<!-- NAV-FOOTER -->

---

### 🧭 Navigation

| Previous | Up | Next |
|:---|:---:|---:|
| ⬅️ Prev: [Diagrams](README.md) | ⬆️ Home: [Git Cheat Sheet](../README.md) | ➡️ Next: [References](../references/README.md) |
