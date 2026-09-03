# CH-04 — Staging, History & .gitignore

Canonical home of: **`.gitignore` syntax and behaviour**, **reading history**.

---

## Before you start

From CH-02 you need the three trees. From CH-03 you need `add`, `commit`, `status`, and the commit
message convention. Have a repository with at least three commits.

---

## A. Ask yourself

1. You fixed a bug **and** started an unfinished feature in the same file. How do you commit only
   the fix?
2. What is the difference between `git diff` and `git diff --staged`? Guess before reading.
3. Your project has a 400 MB dataset and a `venv/` folder. Should they be in the repository? What
   happens to your teammates if they are?
4. You added `secrets.env` to `.gitignore` — but Git keeps tracking it. Why might that be?
5. What question are you actually asking when you run `git log`?

<details>
<summary><strong>Answers</strong></summary>

1. Stage selectively — by file if they are in different files, or by **hunk** (`git add -p`) if
   they are in the same file. This is the single most persuasive demonstration of why staging
   exists; run it live.
2. `git diff` = working directory vs staging (what you have *not yet* staged). `git diff --staged`
   = staging vs last commit (what you *are about to* commit). Most students assume `git diff`
   shows "all my changes" and are confused after `git add`, when it goes empty.
3. No. Every clone downloads them forever, in every version they ever had. A 400 MB dataset
   committed 10 times is not 400 MB in `.git` — it is up to 4 GB, permanently, for everyone.
4. `.gitignore` only affects **untracked** files. Once a file is tracked, Git keeps tracking it
   regardless. You must `git rm --cached` it. This is the #1 `.gitignore` misconception.
5. "What happened, in what order, by whom, and why?" `log` is a query tool. The default output is
   just its least useful form.

</details>

---

## B. The problem

Two problems, one chapter.

**Problem 1 — commits are not file-saves.** Your working directory at any moment contains a mess:
a finished fix, an experiment, a debug print, a personal note. If commits mirror that mess, your
history becomes unreadable and unrevertable. You need to *compose* a commit.

**Problem 2 — you cannot read your own history.** A history you cannot query is a history that
does not help you. `log`, `diff` and `show` turn it into an answer machine.

---

## C. Staging with precision `MOST USED`

### The state machine, in commands

```text
untracked ──add──▶ staged ──commit──▶ tracked & unmodified
                     ▲                        │
                     │                    (you edit)
                     └────── add ◀──── modified
```

```bash
git status              # where is everything?
git add <path>          # working dir  -> staging
git restore --staged <path>   # staging -> working dir   (unstage; CH-12)
git restore <path>            # ⚠ discard working-dir changes  (CH-12)
```

### `git add -p` — the command that makes staging click `IMPORTANT`

Stage *parts* of a file, hunk by hunk.

```bash
git add -p src/complaints.py
```

Git shows one chunk of changes at a time and asks:

```text
Stage this hunk [y,n,q,a,d,s,e,?]?
```

| Key   | Meaning                                     |
| ----- | ------------------------------------------- |
| `y` | stage this hunk                             |
| `n` | skip it                                     |
| `s` | split into smaller hunks                    |
| `e` | edit the hunk manually (line-level control) |
| `q` | quit                                        |
| `?` | help                                        |

This is how you commit the bug fix and leave the half-finished feature in your working directory,
even when both live in the same file. Run it once and staging stops being abstract.

> **When *not* to bother:** trivial single-purpose changes. `git add -p` is for untangling, not
> ceremony.

### Verify before you commit — always

```bash
git diff --staged      # exactly what this commit will contain
```

Make this reflexive. It catches debug statements, stray keys, and accidental whole-file
reformatting before they become permanent.

---

## D. `.gitignore` — keeping junk out `MUST KNOW` `MOST USED`

### What belongs in a repository

| Commit it                                  | Never commit it                                                               |
| ------------------------------------------ | ----------------------------------------------------------------------------- |
| Source code                                | Build output (`dist/`, `build/`, `*.pyc`, `target/`)                  |
| Configuration templates (`.env.example`) | Real secrets (`.env`, keys, tokens) — see [CH-18](../18-security/)          |
| Documentation                              | Dependencies (`node_modules/`, `venv/`) — declared in a manifest instead |
| Small fixtures / sample data               | Large datasets, media, model weights                                          |
| Lock files (`package-lock.json`)         | Editor/OS cruft (`.vscode/`, `.DS_Store`, `Thumbs.db`)                  |
| Schema, migrations                         | Logs, caches,`*.tmp`                                                        |

Test: *if it can be regenerated, downloaded, or is personal to your machine — it does not belong.*

### Syntax

Create `.gitignore` in the repository root:

```gitignore
# comment

venv/              # a directory anywhere (trailing slash = directories only)
*.log              # any file with this extension
build/             # build output
.env               # a specific file
secrets/*.key      # pattern inside a directory

/config.local.json # ONLY at the repository root (leading slash anchors)

!important.log     # negation: re-include something an earlier rule excluded

data/**/*.csv      # ** matches across directory levels
temp?.txt          # ? matches exactly one character
```

Order matters: a later rule can re-include, but **a negation cannot resurrect a file whose parent
directory is ignored.** `logs/` + `!logs/keep.log` does not work — Git never descends into an
ignored directory. Use `logs/*` + `!logs/keep.log` instead.

`.gitignore` itself **must be committed** — it is a shared project decision, not a personal one.

### The trap everyone hits `COMMONLY MISUNDERSTOOD`

> **`.gitignore` only affects files Git is not already tracking.**

If you committed `secrets.env` and *then* ignored it, Git keeps tracking it and keeps committing
its changes. Fix:

```bash
git rm --cached secrets.env     # stop tracking; keep the file on disk
git commit -m "chore: stop tracking secrets.env"
```

⚠ `git rm --cached` removes it from **future** commits only. Every past commit still contains it,
and anyone who cloned already has it. For a leaked credential that is not enough —
[CH-18](../18-security/) covers what to actually do (short version: rewrite history *and* rotate
the secret, in that order of urgency reversed).

### Personal ignores

Things only *you* need ignored (your editor, your scratch files) do not belong in the shared
`.gitignore`:

```bash
# repo-local, not committed:
.git/info/exclude

# machine-wide:
git config --global core.excludesfile ~/.gitignore_global
```

### Useful commands

```bash
git status --ignored              # show what is being ignored
git check-ignore -v path/to/file  # WHICH rule ignores this file? (invaluable when debugging)
```

Starter files for most languages: [github.com/github/gitignore](https://github.com/github/gitignore).
A ready-made one is in [templates/repository-template/](../../../templates/repository-template/).

---

## E. Reading history `MOST USED`

### `git log` — the query tool

```bash
git log                       # full, verbose, rarely what you want
git log --oneline             # one line per commit — the daily default
git log --oneline --graph --all --decorate   # THE command. Shows the actual branch structure.
```

Learn that last one as a single unit. Alias it (CH-14) — most professionals do:

```bash
git config --global alias.lg "log --oneline --graph --all --decorate"
```

Filtering — this is where `log` earns its place:

```bash
git log -5                          # last 5
git log --author="Asha"             # by author
git log --since="2 weeks ago"       # by time
git log --grep="complaint"          # by message text
git log -S "def add_task"           # commits that ADDED or REMOVED this code  ("pickaxe")
git log -- src/store.py             # commits touching this path
git log --stat                      # which files changed, how much
git log -p                          # full diff of every commit
git log main..feat/x                # commits on feat/x that are NOT on main
```

`git log -S` is the one people don't know and should: *"when did this function appear/disappear?"*
It searches content changes, not messages.

### `git show` — inspect one commit

```bash
git show                 # HEAD
git show a3f9c21         # a specific commit
git show HEAD~2          # two commits back
git show HEAD:src/app.py # that file AS IT WAS in that commit
```

### `git diff` — compare anything `MOST USED`

| Command                      | Compares               | Answers                                            |
| ---------------------------- | ---------------------- | -------------------------------------------------- |
| `git diff`                 | working dir ↔ staging | "What have I changed but not staged?"              |
| `git diff --staged`        | staging ↔ HEAD        | "What am I about to commit?"                       |
| `git diff HEAD`            | working dir ↔ HEAD    | "What have I changed since my last commit, total?" |
| `git diff main feat/x`     | two branches           | "What does this branch do?"                        |
| `git diff a3f9c21 7c1e4a9` | two commits            | "What changed between these points?"               |
| `git diff --stat`          | *(any of the above)* | summary instead of full text                       |

Reading a diff:

```diff
--- a/src/complaints.py     ← old version
+++ b/src/complaints.py     ← new version
@@ -12,7 +12,8 @@ def submit(text):        ← old lines 12-18, new lines 12-19
     if not text:
-        return None                       ← removed
+        raise ValueError("empty complaint")  ← added
```

### Referring to commits

| Notation               | Means                                                           |
| ---------------------- | --------------------------------------------------------------- |
| `a3f9c21`            | that commit (short hash; 7 chars is normally unambiguous)       |
| `HEAD`               | where you are now                                               |
| `HEAD~1`, `HEAD~3` | 1 / 3 commits back, following first parents                     |
| `HEAD^`              | the first parent (same as`HEAD~1`)                            |
| `HEAD^2`             | the**second** parent — only meaningful on a merge commit |
| `main`               | the tip of`main`                                              |
| `main@{2.days.ago}`  | where`main` pointed then (uses reflog, CH-12)                 |

`~` walks *back generations*; `^` picks *which parent*. On a linear history they are the same, and
you can safely use `~` until CH-13.

---

## F. Worked example — untangling a messy working directory

You are on the mess-feedback project. Your working directory holds three unrelated things.

```bash
git status -s
#  M src/store.py     <- a real fix AND a debug print
#  M src/app.py       <- half-finished analytics feature
# ?? notes-personal.md <- yours, not the project's
```

Goal: commit only the fix.

```bash
# 1. Never commit blind.
git diff src/store.py

# 2. Stage only the fix hunk; skip the debug print.
git add -p src/store.py
#   -> y  on the validation hunk
#   -> n  on the print() hunk

# 3. Prove what you are about to commit.
git diff --staged
#   shows ONLY the validation change

# 4. Commit it.
git commit -m "fix(store): reject feedback with rating outside 1-5"

# 5. Keep personal files out permanently.
echo "notes-personal.md" >> .gitignore
git add .gitignore
git commit -m "chore: ignore personal notes"

# 6. What's left?
git status -s
#  M src/store.py   <- the debug print, still yours to delete
#  M src/app.py     <- unfinished feature, still in progress
```

One clean, revertable commit. The unfinished work stayed exactly where it belongs: in the working
directory.

---

## G. Brainstorm — predict first

1. You edit `app.py`, run `git add app.py`, then edit `app.py` again. You run `git commit`. Which
   version is committed?
2. Following (1): what does `git status -s` show for `app.py` immediately before the commit?
3. You add `*.log` to `.gitignore`, but `debug.log` was committed last week. Does it disappear
   from the repository?
4. `git diff` prints nothing, but `git status` says you have changes. What is going on?
5. You run `git log -- src/store.py` and see 4 commits, but `git log` shows 30. Why?
6. `git add .` in a folder containing a 2 GB video. You commit, notice, delete the video, and
   commit again. How large is `.git`?

<details>
<summary><strong>Answers</strong></summary>

1. The version **as it was when you ran `git add`**. The staging area is a real snapshot, not a
   list of filenames. The later edit is not included.
2. `MM app.py` — staged changes *and* further unstaged changes to the same file.
3. No. `.gitignore` does not affect tracked files. `debug.log` stays tracked and stays in every
   past commit. Use `git rm --cached debug.log`, and note the history still contains it.
4. Your changes are all **staged**. `git diff` compares working directory to staging, and those
   now match. Use `git diff --staged` or `git diff HEAD`.
5. `-- <path>` filters to commits that touched that path. The other 26 changed other files. Note
   the `--` separator: it tells Git the argument is a path, not a branch name.
6. Still roughly 2 GB. The blob remains in the first commit forever. Deleting a file only changes
   future snapshots. Removing it requires history rewriting (CH-13) — and everyone must re-clone.

</details>

---

## H. Common mistakes

| Symptom                                        | Cause                                      | Fix                                                              | Prevention                                            |
| ---------------------------------------------- | ------------------------------------------ | ---------------------------------------------------------------- | ----------------------------------------------------- |
| Committed the debug print / commented-out code | `git add .` without reviewing            | Amend or fix-forward commit                                      | `git diff --staged` before every commit             |
| `.gitignore` "not working"                   | File is already tracked                    | `git rm --cached <file>`                                       | Write`.gitignore` before the first `add`          |
| Repository is 800 MB                           | Committed`node_modules/`, venv, datasets | Ignore +`rm --cached`; shrinking needs history rewrite (CH-13) | Use a language-appropriate ignore template on day one |
| `git diff` shows nothing after `add`       | Comparing the wrong pair of trees          | `git diff --staged`                                            | Learn the diff table in §E                           |
| Staged the wrong file                          | Sloppy`add`                              | `git restore --staged <file>`                                  | `git status` before `commit`                      |
| Whole file shows as changed                    | Line-ending or formatter mismatch          | Set`core.autocrlf` (CH-03); commit formatting separately       | Never mix reformatting with logic changes             |
| Committed a secret                             | No ignore rule                             | **Rotate the secret first**, then CH-18                    | `.env` in `.gitignore` from commit #1             |
| Can't find when a bug appeared                 | Only ever used plain`git log`            | `git log -S`, `git log -p`, `git bisect` (CH-14)           | Learn`log` as a query language                      |

---

## I. Check your understanding

1. Explain, in one sentence each, what `git diff` and `git diff --staged` compare.
2. You staged a file then edited it again. Describe the contents of all three trees.
3. Why must `.gitignore` be committed?
4. Give the command that tells you which ignore rule is hiding a file.
5. What does `git log -S "connect_db"` find that `git log --grep "connect_db"` does not?
6. When is `git add -p` worth the extra time?

<details>
<summary><strong>Answers</strong></summary>

1. `git diff`: working directory vs staging area. `git diff --staged`: staging area vs HEAD.
2. Working directory has the newest edit; staging has the version from when you ran `add`; HEAD
   has the last committed version. Three genuinely different contents — `git status -s` shows `MM`.
3. Because it is a project-wide decision. If it were personal, every collaborator would
   re-commit build output and junk. Personal rules go in `.git/info/exclude` instead.
4. `git check-ignore -v <path>`.
5. `-S` searches the *content of changes* — it finds commits where that string was added or
   removed, even if no message mentions it. `--grep` searches only commit messages.
6. When one file contains changes belonging to more than one logical commit. Not for routine
   single-purpose edits.

</details>
