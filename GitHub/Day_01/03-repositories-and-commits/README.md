# CH-03 — Repositories & Commits

|  |  |
| - | - |

---

## Before you start

Install Git and confirm it runs. You should be able to answer, from CH-02: what is a commit, what
is a branch, and what are the three trees.

| OS      | Install                                                                                                               |
| ------- | --------------------------------------------------------------------------------------------------------------------- |
| Windows | [git-scm.com/download/win](https://git-scm.com/download/win) — includes Git Bash, which this course's examples assume |
| macOS   | `brew install git`, or run `git --version` and accept the Xcode tools prompt                                      |
| Linux   | `sudo apt install git` / `sudo dnf install git`                                                                   |

```bash
git --version
# git version 2.43.0   (any 2.30+ is fine; git switch/restore need 2.23+)
```

---

## A. Ask yourself

1. What makes a folder on your computer "a Git repository"? What physically changes?
2. You have a two-year-old project folder with no version control. Can you start using Git on it
   now, or is it too late?
3. What is the difference between `git init` and `git clone`?
4. What makes a commit message *good*? Who is the audience?
5. Should one commit contain everything you did today, or something else?

<details>
<summary><strong>Answers</strong></summary>

1. A hidden `.git/` subfolder appears. That folder *is* the repository — the object database, the
   refs, the index, the config. Delete it and you have an ordinary folder again with your latest
   files intact and no history. Copy it and you copy the whole history.
2. Never too late. `git init` in the existing folder, then one commit captures the current state
   as the starting point. You do not get the past two years of history (that information was never
   recorded), but everything from now on is tracked.
3. `init` creates a new empty repository in place. `clone` copies an existing repository —
   including its full history and its remote configuration — from somewhere else. Students who
   confuse these end up with a repository inside a repository.
4. Audience: **a teammate, or you in six months**, trying to answer "why was this changed?" A good
   message states intent and reason, not the diff — the diff is already in the commit.
5. Something else: one *logical change*. A commit should be revertable and reviewable on its own.
   "Everything I did today" is neither.

</details>

---

## B. The problem

You have CH-02's model: snapshots, pointers, three trees. But right now you have no repository to
apply it to, and no way to create a snapshot.

Two situations cover almost everything:

* You are **starting** something — a new assignment, a club website, an ML experiment. → `init`
* You are **joining** something that already exists on GitHub or a teammate's machine. → `clone`

And then, forever after, the loop that is 80% of daily Git: *change something → check → record it
with a reason.*

---

## C. Configuring Git (do this once) `GIT`

Git stamps every commit with your name and email. Set them before your first commit or you will
have commits attributed to `unknown` — and rewriting authorship later means rewriting history.

```bash
git config --global user.name "Aman Pathak"
git config --global user.email "amanpathak8926@gmail.com"
```

Use the email you will register on GitHub, otherwise your commits will not link to your GitHub
profile or appear on your contribution graph.

Three more settings worth having on day one:

```bash
git config --global init.defaultBranch main    # new repos start on 'main', not 'master'
git config --global core.editor "code --wait"  # or "nano", "vim"
git config --global pull.rebase false          # be explicit; see CH-13
```

Windows/macOS line endings — set this once and avoid a whole category of fake diffs:

```bash
git config --global core.autocrlf true    # Windows
git config --global core.autocrlf input   # macOS / Linux
```

Inspect anything:

```bash
git config --list --show-origin   # every setting and which file it came from
git config user.email             # one value, as it applies right here
```

| Level        | Flag         | File               | Use for                                                  |
| ------------ | ------------ | ------------------ | -------------------------------------------------------- |
| System       | `--system` | `/etc/gitconfig` | Rare                                                     |
| Global (you) | `--global` | `~/.gitconfig`   | Your normal identity and preferences                     |
| Local (repo) | *(none)*   | `.git/config`    | Per-project overrides — e.g. a work email on work repos |

Local overrides global. To use a different identity on one project, run `git config user.email "asha@company.com"` **inside** that repository.

More configuration and aliases: [CH-14](../14-advanced-git/).

---

## D. Creating a repository `GIT`

### `git init` — start a new repository

```bash
mkdir hostel-tracker && cd hostel-tracker
git init
# Initialized empty Git repository in /home/asha/hostel-tracker/.git/
```

What changed: a `.git/` folder now exists. Nothing else. No commit yet — the repository is empty
and `main` does not exist until the first commit creates something for it to point at.

You can also run `git init` inside an existing folder with files in it; the files become
untracked, and your first commit captures them.

```bash
ls -a          # .git  is the repository. Everything else is your working directory.
git status     # "No commits yet" + your untracked files
```

⚠ **Never `git init` inside another repository.** If `git status` works in the parent folder, you
are already in a repository. Nested repositories confuse Git and every tool around it. Check with
`git rev-parse --show-toplevel` — it prints the repository root you are currently inside.

### `git clone` — copy an existing repository

```bash
git clone https://github.com/Aman-Pathak-0243/Unified_Engineering.git
cd Unified_Engineering
```

`clone` does four things at once: creates the folder, copies the entire history into `.git/`,
records the source as a remote named `origin`, and checks out the default branch into your working
directory. (Remotes are CH-06.)

```bash
git clone <url> my-folder-name    # clone into a differently named folder
git clone --depth 1 <url>         # shallow: latest commit only. Fast; limited history. Rarely what you want.
```

---

## E. The commit loop `MOST USED`

### `git status` — read this constantly

```bash
git status
```

```text
On branch main
No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        README.md
        complaints.py

nothing added to commit but untracked files present
```

`git status` answers all three tree questions at once: what is modified (working directory), what
is staged (index), and where you are (branch, ahead/behind). Git's status output literally tells
you the command for the next step — read it rather than guessing.

Short form once you are fluent:

```bash
git status -s
# ?? README.md      untracked
#  M app.py         modified, not staged
# M  complaints.py  staged
# MM notes.md       staged, then modified again
```

The two columns are **staging area** and **working directory** — the three trees, made visible.

### `git add` — choose what goes in the next commit

```bash
git add README.md              # one file
git add README.md app.py       # several
git add docs/                  # a directory
git add .                      # everything under the current directory  (see the warning below)
```

⚠ `git add .` is the most over-used command in Git. It is fine when you genuinely intend to add
everything and you have just read `git status`. It is how debug code, 200 MB datasets and API
keys get committed. Selective staging is the whole subject of [CH-04](../04-staging-and-history/).

### `git commit` — record the snapshot

```bash
git commit -m "Add complaint submission form"
```

```text
[main (root-commit) a3f9c21] Add complaint submission form
 2 files changed, 34 insertions(+)
```

What changed internally (CH-02's model in action):

1. Git writes blob objects for the staged content, a tree object for the directory structure, and
   a commit object pointing at that tree plus the current HEAD as parent.
2. The branch pointer (`main`) is rewritten to the new commit ID.
3. The staging area now matches HEAD, so `git status` reports a clean tree.

Useful forms:

```bash
git commit                     # opens your editor — use this for real messages (see §F)
git commit -m "subject" -m "body paragraph"
git commit -am "message"       # stage all *tracked* modified files, then commit.
                               # Does NOT include untracked files. Skips your chance to review.
```

⚠ `git commit --amend` replaces the previous commit with a new one. Safe on commits you have not
pushed; dangerous afterwards — see [CH-13](../13-rebase/).

---

## F. Commit messages — the canonical convention `MUST KNOW`

This is the reference used by the rest of the course, the templates, and every homework rubric.

```text
<type>(<scope>): <subject, imperative, ≤ 50 chars, no full stop>

<blank line>
<body: WHY. What problem, what approach, what you rejected. Wrap at 72.>

<blank line>
Closes #42
```

Real example:

```text
fix(complaints): reject submissions with empty description

Empty complaints were reaching the warden's dashboard and could not be
actioned. Validation now happens server-side as well as in the form,
because the form can be bypassed by posting directly to the endpoint.

Closes #42
```

| Type         | Use for                                   |
| ------------ | ----------------------------------------- |
| `feat`     | A new capability for the user             |
| `fix`      | A bug fix                                 |
| `docs`     | Documentation only                        |
| `refactor` | Behaviour unchanged, structure improved   |
| `test`     | Adding or fixing tests                    |
| `chore`    | Build, dependencies, config, housekeeping |
| `style`    | Formatting only, no logic change          |

### Rules that actually matter

1. **Imperative mood**: "Add validation", not "Added" or "Adds". Read it as *"Applying this commit
   will… add validation."* Git's own generated messages ("Merge branch…", "Revert…") use it.
2. **The subject line is a headline.** It appears alone in `git log --oneline`, in PR lists, in
   `git blame`. If it needs the body to make sense, rewrite it.
3. **The body explains WHY.** The *what* is in the diff and always will be. The *why* exists only
   in your head until you write it down.
4. **One logical change per commit.** If your subject needs "and", you have two commits.

### Bad → good

| Bad                                                      | Why it fails                       | Good                                                |
| -------------------------------------------------------- | ---------------------------------- | --------------------------------------------------- |
| `update`                                               | Says nothing                       | `fix(auth): expire session tokens after 24h`      |
| `fixed bug`                                            | Which bug?                         | `fix(upload): handle filenames containing spaces` |
| `asdfgh`                                               |                                    | anything                                            |
| `final commit`                                         | Meaningless a day later            | `docs: add setup steps to README`                 |
| `Added login page and fixed navbar and updated readme` | Three commits wearing a trenchcoat | three commits                                       |

> **Instructor note:** commit message quality is the cheapest, fastest signal of whether a student
> is thinking in changes or in file-saves. It is worth grading from the very first assignment,
> because habits set here persist for years.

---

## G. Worked example — the first hour of a real project

```bash
mkdir mess-feedback && cd mess-feedback
git init

# 1. A repository with no README is a repository nobody can use.
printf '# Mess Feedback\n\nCollects daily mess feedback from hostel residents.\n' > README.md
git add README.md
git commit -m "docs: add project README with purpose"

# 2. Application skeleton — one logical change.
mkdir -p src
printf 'def main():\n    print("mess feedback")\n' > src/app.py
git add src/app.py
git commit -m "feat: add application entry point"

# 3. Feedback storage — a separate logical change, so a separate commit.
printf 'FEEDBACK = []\n\ndef add(rating, comment):\n    FEEDBACK.append((rating, comment))\n' > src/store.py
git add src/store.py
git commit -m "feat(store): add in-memory feedback storage"

git log --oneline
# 7c1e4a9 feat(store): add in-memory feedback storage
# 4b8d2f1 feat: add application entry point
# a3f9c21 docs: add project README with purpose
```

Three commits, three intentions, each revertable alone. Compare with the same work as one commit
called `initial stuff` — same files, but the history now carries no information.

---

## H. Brainstorm — predict first

1. You run `git init` in a folder with 12 files, then immediately `git commit -m "start"`. What
   happens?
2. You run `git init` twice in the same folder. What happens to your history?
3. You commit, then delete `.git/`. What have you lost? What do you still have?
4. `git commit -am "fix"` when you have one modified tracked file and one brand-new untracked
   file. What lands in the commit?
5. You clone a repository into a folder that is already inside another Git repository. What goes
   wrong, and when will you notice?

<details>
<summary><strong>Answers</strong></summary>

1. It fails: `nothing added to commit`. `init` does not stage anything — all 12 files are
   untracked. You need `git add` first. This trips up nearly every beginner once.
2. Nothing. `git init` on an existing repository is safe and non-destructive — it reinitialises
   config and prints "Reinitialized existing Git repository." Your commits are untouched.
3. You lose all history, branches and configuration. You keep the current files exactly as they
   are on disk, because the working directory is not stored inside `.git`.
4. Only the modified **tracked** file. `-a` means "all tracked files that changed" — untracked
   files are never included automatically. This is a genuinely common surprise.
5. Git will operate on the *outer* repository from the parent folder and the inner one from
   inside; the inner repo's files appear to the outer one as an opaque, unstaged entry. You often
   notice only when a teammate clones and finds an empty folder. Fix: don't nest — check with
   `git rev-parse --show-toplevel` first.

</details>

## I. Common mistakes

| Symptom                                          | Cause                                            | Fix                                                                    | Prevention                                                                   |
| ------------------------------------------------ | ------------------------------------------------ | ---------------------------------------------------------------------- | ---------------------------------------------------------------------------- |
| `Author: unknown <unknown@…>` in log          | Never configured identity                        | Set config; rewriting past authorship needs history rewriting (CH-13)  | Configure before first commit                                                |
| `nothing added to commit`                      | Forgot`git add`                                | Stage, then commit                                                     | Read`git status` — it says exactly this                                   |
| Repository inside a repository                   | Ran`init`/`clone` in the wrong directory     | Delete the inner`.git`, or move the folder out                       | `git rev-parse --show-toplevel` before `init`                            |
| Commits not showing on GitHub profile            | Commit email ≠ any email on your GitHub account | Add the email to GitHub, or fix config for future commits              | Use the same email everywhere                                                |
| History of`update`, `update2`, `final`     | Treating commits as file-saves                   | Nothing to fix retroactively; change habit now                         | Write the subject line*before* staging — it forces you to name the change |
| Huge first commit ("initial commit" = 300 files) | `git add .` on an existing project             | Acceptable once for a genuine import; not a habit                      | Split by concern where the project is new                                    |
| Committed`node_modules/` or a dataset          | `git add .` with no ignore file                | Remove and ignore (CH-04); it stays in history until rewritten (CH-13) | Write`.gitignore` **before** the first `add`                       |

---

## J. Check your understanding

1. What exactly does `git init` create, and what does it *not* do?
2. Give two differences between `init` and `clone`.
3. Why is `git commit -am` unable to include a new file?
4. Your commit message is `fixed stuff`. Write it properly, inventing a plausible context.
5. Why should identity be configured before the first commit rather than after?
6. What is in the staging area immediately after a successful commit?

<details>
<summary><strong>Answers</strong></summary>

1. Creates `.git/` (object database, refs, config, index). It does not stage, commit, or create
   `main` — the branch exists only once a commit gives it something to point to.
2. `clone` copies existing history and configures `origin`; `init` starts empty with no remote.
   `clone` creates the directory for you; `init` uses the one you are in.
3. `-a` is defined as "all *tracked* files that have been modified or deleted." An untracked file
   has never been added, so Git has no reason to consider it part of the project.
4. e.g. `fix(planner): prevent duplicate tasks with identical titles` + a body explaining that
   duplicates were being created by double form submission.
5. Every commit permanently records author name/email as part of the commit object, which
   determines its hash. Fixing it later means creating replacement commits (rewriting history) —
   trivial alone, disruptive once shared.
6. Exactly the same content as HEAD's snapshot — which is why `git status` reports a clean tree.

</details>

---
