# Debugging

Tools for finding when a bug was introduced, who changed what, and which commit is responsible.

---

## git bisect — Binary Search for a Bug

`bisect` performs a binary search through commit history to find the exact commit that introduced a bug. For N commits, it takes only log₂(N) steps.

### Step-by-Step

```bash
# 1. Start a bisect session
git bisect start

# 2. Mark the current state as broken
git bisect bad

# 3. Mark a known-good commit (a tag, branch, or hash where the bug didn't exist)
git bisect good v1.2.0
git bisect good a1b2c3    # or a specific commit hash

# Git checks out a commit halfway between good and bad.
# Test your app, then tell Git the result:

git bisect good    # this commit is fine — bug came later
git bisect bad     # this commit has the bug — narrow down earlier

# Repeat until Git prints:
# a1b2c3 is the first bad commit

# 4. End the session and return to your original branch
git bisect reset
```

### Automated Bisect

If you have a test script that exits 0 for good and non-zero for bad, Git can run the entire bisect automatically:

```bash
git bisect start
git bisect bad HEAD
git bisect good v1.2.0
git bisect run npm test          # or: ./test.sh, python test.py, etc.
git bisect reset
```

---

## git blame — Line-by-Line Authorship

Shows who last changed each line of a file and in which commit.

```bash
# Annotate all lines
git blame src/auth.js

# Annotate a specific line range
git blame -L 20,45 src/auth.js

# Ignore whitespace-only changes (avoids blaming reformatting commits)
git blame -w src/auth.js

# Show the commit that moved or copied lines within the file
git blame -M src/auth.js

# Show the commit that moved lines from another file in the same commit
git blame -C src/auth.js
```

**Reading the output:**
```
a1b2c3ef (Prakhar  2024-03-10 09:15 +0530 42) const token = jwt.sign(payload, secret);
^hash     ^author  ^date              ^line ^code
```

Once you have the commit hash, inspect the full change:
```bash
git show a1b2c3ef
```

---

## git log for Debugging

### Find When a String Was Added or Removed

```bash
# Commits that added or removed an exact string (the "pickaxe")
git log -S "getUserById" --oneline

# Show the full patch for those commits
git log -S "getUserById" -p
```

### Find Commits That Changed a Function

```bash
# Trace changes to a specific function across all its commits
git log -L :getUserById:src/user.js

# Works with line ranges too
git log -L 40,80:src/user.js
```

### Find Commits Touching a File

```bash
# All commits that modified a file
git log --follow -- src/auth.js

# Full diff of every change to the file
git log -p --follow -- src/auth.js

# Find commits touching a file in a date range
git log --since="2024-01-01" --until="2024-06-30" -- src/auth.js
```

### Other Useful Debug Queries

```bash
# Commits that match a message keyword
git log --grep="security" --oneline

# Show commits across all branches that touched a file
git log --all --oneline -- src/auth.js

# See what changed between two releases
git log v1.1.0..v1.2.0 --oneline

# Show commits not yet in main (changes unique to this branch)
git log main..HEAD --oneline
```
