# Git Commands Cheat Sheet

---

## 1. Git Architecture

| Command | Purpose |
|---|---|
| `git init` | Create a new repository |
| `git status` | Show state across working tree and staging area |
| `git log` | Display commit history |
| `ls .git/` | Inspect the internal repository structure |

---

## 2. Hash Values (SHA-1)

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

| Command | Purpose |
|---|---|
| `git mv <old> <new>` | Rename (and stage the rename) |
| `git rm <file>` | Delete file from disk and stage deletion |
| `git rm --cached <file>` | Unstage a file but keep it on disk |
| `git rm -r <dir>` | Recursively delete a directory |
| `git add -u` | Stage all modifications and deletions |

---

## 6. Viewing Changes

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

| Command | Purpose |
|---|---|
| `git add --dry-run .` | Preview what would be staged |
| `git add -n .` | Short form of dry run |
| `git add -n "*.py"` | Dry run with a specific pattern |
| `git add -nv .` | Verbose dry run (includes ignored files) |
| `git check-ignore -v <file>` | Explain why a file is ignored |
| `cat .gitignore` | Review ignore rules |

---

## Quick Reference

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