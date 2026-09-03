# GitHub — Git & Version Control Course

Lecture material for the Git and GitHub track of the Unified Engineering course.
Each chapter is a self-contained `README.md` built around *predict-first* exercises:
you answer questions before reading the explanation, then check yourself against
collapsible answer blocks.

## Contents

### Day 01 — Git foundations

| Chapter | Topic | Covers |
| ------- | ----- | ------ |
| [01 — Git & Version Control](Day_01/01-git-and-version-control/README.md) | Why version control exists | the "folder of `_final_v2` files" problem, what a VCS gives you, **Git vs GitHub** |
| [02 — The Git Mental Model](Day_01/02-git-mental-model/README.md) | The model you cannot skip | commits, pointers, HEAD, the object graph |
| [03 — Repositories & Commits](Day_01/03-repositories-and-commits/README.md) | Making a repo and recording history | `git init`, `git clone`, `git commit` |
| [04 — Staging, History & .gitignore](Day_01/04-staging-and-history/README.md) | The staging area and reading history | `git add`, `git status`, `git log`, `.gitignore` syntax |
| [05 — Branches & Merging](Day_01/05-branches/README.md) | Parallel lines of work | branch mechanics, fast-forward vs three-way merge, undoing merges |

### Reference sheets

| Sheet | Use |
| ----- | --- |
| [01 — Git Fundamentals](HelpDocs/01-git-fundamentals.md) | Quick command lookup: init/clone, identity setup, status, staging, diffing, commit notation |

## How to use these

1. Read **Before you start** — it names the prerequisite you actually need.
2. Do the **Ask yourself** / **Brainstorm** questions *before* expanding the answers.
3. Run the commands in a scratch repository; verify the graphs yourself with
   `git log --oneline --graph --all --decorate`.
4. Finish with **Check your understanding** and the **Common mistakes** table.

## Conventions

- Topics are tagged `GIT` (works offline, on your machine) or `GITHUB` (a website
  feature) so the boundary is never ambiguous.
- Branch names follow `type/short-description` — e.g. `feat/dark-mode`,
  `fix/login-redirect-loop`.
- Cross-references use `CH-NN` and point to the matching chapter folder.
