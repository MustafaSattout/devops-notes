# Git Concepts: A Step-by-Step Guide

> A structured reference covering core Git concepts with clear explanations, practical examples, and command cheat sheets.

---

## Table of Contents

1. [Git Architecture](#1-git-architecture)
2. [Hash Values (SHA-1)](#2-hash-values-sha-1)
3. [The HEAD Pointer](#3-the-head-pointer)
4. [Adding and Moving Files](#4-adding-and-moving-files)
5. [Renaming and Deleting Files](#5-renaming-and-deleting-files)
6. [Viewing Changes](#6-viewing-changes)
7. [Add and Commit](#7-add-and-commit)
8. [`git add` – Dry Run](#8-git-add--dry-run)

---

## 1. Git Architecture

### What Is It?

Git uses a **three-tree architecture** — three distinct environments that your files move through during version control. Understanding this model is the foundation of everything else in Git.

```
┌─────────────────┐     git add      ┌─────────────────┐    git commit    ┌─────────────────┐
│  Working Tree   │ ───────────────► │  Staging Area   │ ───────────────► │   Repository    │
│  (your files)   │                  │    (Index)      │                  │  (.git folder)  │
└─────────────────┘                  └─────────────────┘                  └─────────────────┘
```

| Layer | Also Called | What It Holds |
|---|---|---|
| **Working Tree** | Working Directory | Your actual project files on disk |
| **Staging Area** | Index / Cache | A snapshot of what will go into the next commit |
| **Repository** | `.git` directory | The full history of all commits |

### How Data Is Stored

Git stores everything as **objects** inside the `.git` folder:

- **Blob** — the content of a single file
- **Tree** — a directory listing (maps filenames to blobs)
- **Commit** — a snapshot pointing to a tree, with author info and a parent commit
- **Tag** — a named reference to a specific commit

```
Commit ──► Tree ──► Blob (file content)
                └──► Blob (another file)
                └──► Tree (subdirectory)
```

### Practical Example

```bash
# Initialize a new repository
git init my-project
cd my-project

# Check the three areas
git status          # shows working tree vs staging area
git log             # shows the repository history
ls .git/            # peek inside the repository store
```

### Cheat Sheet

| Command | Purpose |
|---|---|
| `git init` | Create a new repository |
| `git status` | Show state across working tree and staging area |
| `git log` | Display commit history |
| `ls .git/` | Inspect the internal repository structure |

---

## 2. Hash Values (SHA-1)

### What Is It?

Every object Git stores (blob, tree, commit, tag) is identified by a **SHA-1 hash** — a 40-character hexadecimal fingerprint computed from the object's content.

```
"Hello, Git!" ──► SHA-1 ──► 6a56e9f3b7c8d2e4f1a0b9c8d7e6f5a4b3c2d1e0
```

Key properties:
- **Deterministic** — same content always produces the same hash
- **Unique** — different content produces a different hash (collisions are astronomically rare)
- **Immutable** — changing even one character changes the entire hash
- **Content-addressed** — Git uses the hash as the file's storage key

### Why It Matters

SHA-1 hashes are how Git:
- Detects corruption (if the hash doesn't match, data changed)
- Avoids storing duplicate content (same file = same hash = stored once)
- Identifies every commit, file, and tree uniquely

### Practical Example

```bash
# See the SHA-1 of the latest commit
git log --oneline

# Output example:
# a3f5b2c Add login feature
# 7d9e1f0 Initial commit

# Hash a string manually (same algorithm Git uses)
echo -n "Hello, Git!" | git hash-object --stdin

# Hash an actual file and see where Git stores it
git hash-object README.md

# View a stored object by its hash (first 7+ chars work too)
git cat-file -t a3f5b2c    # type: commit / blob / tree / tag
git cat-file -p a3f5b2c    # pretty-print the content
```

### Cheat Sheet

| Command | Purpose |
|---|---|
| `git log --oneline` | Show commits with short hashes |
| `git hash-object <file>` | Compute a file's SHA-1 hash |
| `git hash-object --stdin` | Hash piped input |
| `git cat-file -t <hash>` | Show the object type |
| `git cat-file -p <hash>` | Pretty-print an object's content |
| `git show <hash>` | Show full details of a commit |

---

## 3. The HEAD Pointer

### What Is It?

`HEAD` is a special pointer that always tells Git **where you currently are** in the repository. It typically points to the tip of the current branch.

```
main branch:    A ──► B ──► C  ◄── HEAD
                                    │
                              (you are here)
```

When you make a new commit, HEAD moves forward automatically.

### HEAD States

**Attached HEAD** (normal state):
```
HEAD ──► main ──► latest commit
```

**Detached HEAD** (when you check out a specific commit directly):
```
HEAD ──► (specific commit hash)   ← not pointing to any branch
```

A detached HEAD is like reading a book with no bookmark — you can look around, but any new commits won't be saved to a branch unless you create one.

### Practical Example

```bash
# See where HEAD currently points
cat .git/HEAD
# Output: ref: refs/heads/main

# HEAD as a shorthand in commands
git log HEAD          # history from current position
git diff HEAD         # changes since last commit
git show HEAD         # details of the latest commit
git show HEAD~1       # one commit before HEAD
git show HEAD~2       # two commits before HEAD

# Switch branches (HEAD moves with you)
git checkout feature-branch
cat .git/HEAD
# Output: ref: refs/heads/feature-branch

# Enter detached HEAD state
git checkout a3f5b2c
cat .git/HEAD
# Output: a3f5b2c...  (a raw hash, not a branch name)

# Get back to a branch (exit detached HEAD)
git checkout main
```

### Cheat Sheet

| Command | Purpose |
|---|---|
| `cat .git/HEAD` | See what HEAD points to |
| `git log HEAD` | Show history from HEAD |
| `git diff HEAD` | Diff working tree against HEAD |
| `git show HEAD` | Show latest commit |
| `git show HEAD~N` | Show N commits before HEAD |
| `git checkout <branch>` | Move HEAD to a branch |
| `git checkout <hash>` | Enter detached HEAD state |

---

## 4. Adding and Moving Files

### What Is It?

Files begin in the **working tree**. You use `git add` to promote them to the **staging area** (index), preparing them for the next commit. Moving files updates Git's tracking so the rename or move is recorded correctly.

### File States

```
Untracked ──► git add ──► Staged ──► git commit ──► Tracked/Committed
                                                          │
                                               (modify file)
                                                          │
                                                       Modified ──► git add ──► Staged
```

### Practical Example — Adding Files

```bash
# Create some files
echo "# My Project" > README.md
echo "print('hello')" > main.py

# Check status (both files are untracked)
git status

# Stage a single file
git add README.md

# Stage multiple files
git add README.md main.py

# Stage everything in the current directory
git add .

# Stage all tracked files that have been modified
git add -u

# Stage interactively (choose hunks)
git add -p main.py
```

### Practical Example — Moving Files

```bash
# Method 1: Use git mv (recommended — Git tracks the move automatically)
git mv main.py src/main.py

# Method 2: Move manually (Git sees it as delete + add — less clean)
mv main.py src/main.py
git rm main.py
git add src/main.py

# Confirm the move is staged
git status
# Output: renamed: main.py -> src/main.py
```

### Cheat Sheet

| Command | Purpose |
|---|---|
| `git add <file>` | Stage a specific file |
| `git add .` | Stage all changes in current directory |
| `git add -u` | Stage only modifications to tracked files |
| `git add -p` | Interactively stage changes (hunk by hunk) |
| `git mv <old> <new>` | Move or rename a tracked file |
| `git status` | Show staged vs unstaged changes |

---

## 5. Renaming and Deleting Files

### What Is It?

Git tracks **content changes**, not filesystem events. When you rename or delete a file, you must tell Git explicitly — otherwise it records a deletion and an untracked new file instead of a clean rename.

### Renaming Files

```bash
# ✅ Recommended: git mv handles staging automatically
git mv old-name.txt new-name.txt
git status
# Output: renamed: old-name.txt -> new-name.txt

# ❌ Manual rename (requires two extra commands)
mv old-name.txt new-name.txt
git rm old-name.txt
git add new-name.txt
```

Git detects renames by comparing content similarity (≥50% match = rename detected).

### Deleting Files

```bash
# ✅ Recommended: git rm removes from disk AND stages the deletion
git rm unwanted.txt
git status
# Output: deleted: unwanted.txt

# Remove from staging area only (keep the file on disk)
git rm --cached secret.env

# Remove an entire directory
git rm -r old-folder/

# ❌ Manual delete (requires a follow-up git rm)
rm unwanted.txt
git rm unwanted.txt   # must still tell Git
# OR:
git add -u            # stages all deletions at once
```

### Practical Example — Full Workflow

```bash
# Rename a file, delete another, then commit
git mv app.py application.py
git rm legacy.py

git status
# renamed: app.py -> application.py
# deleted: legacy.py

git commit -m "Rename app.py; remove legacy.py"
```

### Cheat Sheet

| Command | Purpose |
|---|---|
| `git mv <old> <new>` | Rename (and stage the rename) |
| `git rm <file>` | Delete file from disk and stage deletion |
| `git rm --cached <file>` | Unstage a file but keep it on disk |
| `git rm -r <dir>` | Recursively delete a directory |
| `git add -u` | Stage all modifications and deletions |

---

## 6. Viewing Changes

### What Is It?

`git diff` lets you inspect exactly **what changed** — line by line — across the three areas: working tree, staging area, and the last commit.

### The Three Diff Zones

```
Working Tree  ←──── git diff ────────────────────────────►  Staging Area
Staging Area  ←──── git diff --staged ──────────────────►  Last Commit
Working Tree  ←──── git diff HEAD ──────────────────────►  Last Commit
```

### Reading a Diff

```diff
diff --git a/README.md b/README.md
index 6a56e9f..8b2f3a1 100644
--- a/README.md         ← original (a)
+++ b/README.md         ← modified (b)
@@ -1,3 +1,4 @@        ← line numbers: -old +new
 # My Project           ← unchanged line (leading space)
-Old description        ← removed line (leading -)
+New description        ← added line (leading +)
+Extra line added       ← another added line
```

### Practical Example

```bash
# 1. Diff between working tree and staging area (unstaged changes)
git diff

# 2. Diff between staging area and last commit (staged changes)
git diff --staged
git diff --cached    # same thing, older alias

# 3. Diff between working tree and last commit (all changes)
git diff HEAD

# 4. Diff between two specific commits
git diff a3f5b2c 7d9e1f0

# 5. Diff a specific file only
git diff README.md

# 6. Compact summary (just filenames + stats)
git diff --stat
git diff --name-only

# 7. Word-level diff (highlights changed words, not whole lines)
git diff --word-diff
```

### Cheat Sheet

| Command | Purpose |
|---|---|
| `git diff` | Unstaged changes (working tree vs index) |
| `git diff --staged` | Staged changes (index vs last commit) |
| `git diff HEAD` | All changes since last commit |
| `git diff <hash1> <hash2>` | Compare two commits |
| `git diff <file>` | Diff a specific file |
| `git diff --stat` | Summary of changed files and lines |
| `git diff --word-diff` | Highlight changed words within lines |

---

## 7. Add and Commit

### What Is It?

A **commit** is a permanent snapshot of your staged changes saved into the repository. Every commit records: the author, timestamp, a message, and a pointer to the previous commit — forming the project's history chain.

```
Working Tree ──► git add ──► Staging Area ──► git commit ──► Repository
                                                                    │
                                              A ──► B ──► C ──► [new commit]
```

### Writing Good Commit Messages

```
feat: add user authentication

- Implement JWT token generation
- Add login and logout endpoints
- Write unit tests for auth module

Closes #42
```

**Rules of thumb:**
- Subject line: imperative mood, ≤72 characters
- Blank line between subject and body
- Body explains *what* and *why*, not *how*

### Practical Example

```bash
# Step 1: Make some changes
echo "# Welcome" > README.md
echo "def main(): pass" > app.py

# Step 2: Stage the changes
git add README.md app.py
# or: git add .

# Step 3: Verify what's staged
git status
git diff --staged

# Step 4: Commit
git commit -m "Add README and initial app skeleton"

# Shortcut: stage all tracked files AND commit in one step
git commit -am "Fix typo in README"
# ⚠️  -a only works on already-tracked files; new files still need git add

# Amend the last commit (before pushing!)
git commit --amend -m "Fix typo in README (corrected message)"

# Commit with a multi-line message
git commit    # opens your default editor

# View the commit you just made
git show HEAD
git log --oneline -5
```

### Cheat Sheet

| Command | Purpose |
|---|---|
| `git add .` | Stage all changes |
| `git commit -m "msg"` | Commit with inline message |
| `git commit` | Open editor for multi-line message |
| `git commit -am "msg"` | Stage tracked files and commit together |
| `git commit --amend` | Modify the most recent commit |
| `git show HEAD` | Inspect the latest commit |
| `git log --oneline` | Compact commit history |

---

## 8. `git add` – Dry Run

### What Is It?

A **dry run** simulates a `git add` operation and shows you which files *would* be staged — without actually staging anything. It's a safety check before a large `git add`.

```bash
git add --dry-run .
git add -n .       # -n is the short flag for --dry-run
```

### Why Use It?

- Avoid accidentally staging files you didn't intend to (logs, build artifacts, secrets)
- Verify that `.gitignore` rules are working as expected
- Preview the effect of wildcard patterns before applying them

### Practical Example

```bash
# Scenario: you want to stage all .py files but not .log files
ls
# app.py  utils.py  debug.log  config.env  README.md

# Dry run to preview what "git add ." would stage
git add --dry-run .
# Output:
# add 'app.py'
# add 'utils.py'
# add 'debug.log'   ← oops! you don't want this
# add 'README.md'

# Fix: add debug.log to .gitignore first
echo "*.log" >> .gitignore
echo "config.env" >> .gitignore

# Dry run again to confirm
git add -n .
# Output:
# add 'app.py'
# add 'utils.py'
# add 'README.md'
# add '.gitignore'  ← correct!

# Now actually stage
git add .
```

### Combining with Patterns

```bash
# Preview staging only Python files
git add -n "*.py"

# Preview staging a specific directory
git add -n src/

# Verbose dry run (more detail on ignored files)
git add -nv .
```

### Cheat Sheet

| Command | Purpose |
|---|---|
| `git add --dry-run .` | Preview what would be staged |
| `git add -n .` | Short form of dry run |
| `git add -n "*.py"` | Dry run with a specific pattern |
| `git add -nv .` | Verbose dry run (includes ignored files) |
| `git check-ignore -v <file>` | Explain why a file is ignored |
| `cat .gitignore` | Review ignore rules |

---

## Quick Reference Card

| Concept | Key Command | What It Does |
|---|---|---|
| Architecture | `git status` | Show state across all three trees |
| SHA-1 | `git cat-file -p <hash>` | Inspect any Git object |
| HEAD | `cat .git/HEAD` | See where you currently are |
| Add files | `git add .` | Stage all changes |
| Move files | `git mv <old> <new>` | Rename and auto-stage |
| Delete files | `git rm <file>` | Remove and stage deletion |
| View changes | `git diff --staged` | See what's about to be committed |
| Commit | `git commit -m "msg"` | Save a snapshot to history |
| Dry run | `git add -n .` | Preview staging without doing it |

---

