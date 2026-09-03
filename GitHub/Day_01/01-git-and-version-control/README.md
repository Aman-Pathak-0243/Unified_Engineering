# CH-01 — Git & Version Control

|  |  |
| - | - |

---

## Before you start

You need: a terminal, and 30 minutes. No installation yet — that is CH-03.

This chapter has no exercises with commands. It exists to make the rest of the course make sense.
Skip it only if you have used another version control system before.

---

## A. Ask yourself

Answer these *before* reading on. Write your answers down.

1. You are writing a report. How do you currently keep old versions? What does your folder look
   like after two weeks?
2. Your code worked on Tuesday and is broken on Friday. You have no old copies. How do you find
   what broke it?
3. Three people edit the same document over the weekend, each on their own laptop. On Monday you
   must produce one combined document. Describe your procedure. How long does it take?
4. You email `project_final_v3.zip` to a teammate. They send back `project_final_v3_edited.zip`.
   What information has been permanently lost?
5. Is Git the same thing as GitHub? If not, what is the difference?

<details>
<summary><strong>Answers</strong></summary>

1. Nearly always `report.docx`, `report_final.docx`, `report_final2.docx`,
   `report_FINAL_actually.docx`. The naming carries no reliable ordering, no reason for each
   change, and no author. Point this out — the students have already *invented a bad VCS*, which
   proves the need is real.
2. Without history, the only tools are memory and re-reading everything. With history, the answer
   is mechanical: check what changed between Tuesday and Friday (`git diff`, or `git bisect` in
   CH-14). This is the single most persuasive argument for version control.
3. Manual merging: open all three, copy-paste, hope. It is slow, error-prone, and silently loses
   work when two people edit the same paragraph. Git does not remove this problem — it *detects*
   it and forces a decision (CH-10). Detection is the real win.
4. The *sequence* of changes and the *reason* for each. You have two end states and no path
   between them. You also cannot tell who wrote which line, or take one change without the others.
5. **Git** is a program that runs on your computer and records versions of a folder. **GitHub** is
   a website that stores copies of Git repositories and adds collaboration tools (pull requests,
   issues, code review). Git works offline and without GitHub; GitHub is meaningless without Git.
   Analogy that holds: Git is the camera, GitHub is the shared photo album.

</details>

---

## B. The real problem

Every developer, writer and researcher independently invents the same broken system:

```text
project/
├── report.docx
├── report_v2.docx
├── report_final.docx
├── report_final_REAL.docx
├── report_final_REAL_sirs_comments.docx
└── report_final_use_this_one.docx
```

This "works" until you need to answer a normal question:

* Which version did I submit?
* Who wrote this paragraph, and why?
* What changed between the version that worked and this one?
* Can I get back the section I deleted last week?
* How do I combine my chapter with my teammate's chapter?

None of these are answerable. The information was never recorded — only the end states were kept.

**Version control is the practice of recording the sequence of changes, with authorship and
reasoning, so that history becomes queryable.**

---

## C. What a version control system gives you

| Capability                  | What it means in practice                                                |
| --------------------------- | ------------------------------------------------------------------------ |
| **History**           | Every recorded version is retrievable, forever, with its date and author |
| **Reason**            | Each version carries a message explaining*why* the change was made     |
| **Comparison**        | You can see exactly what changed between any two points                  |
| **Branching**         | You can try an idea without endangering the working version              |
| **Merging**           | Two independent lines of work can be combined, with conflicts detected   |
| **Blame/attribution** | You can find who last changed a specific line and why                    |
| **Recovery**          | Deleted work is retrievable as long as it was ever committed             |
| **Collaboration**     | Many people can work in parallel without emailing zip files              |

The last one is what people notice. The first six are what make it worth learning.

---

## D. Git vs GitHub — get this right now `BOTH`

The most common confusion in the entire course. Every topic in this course is tagged so you never
have to guess.

|                   | **Git** `GIT`                    | **GitHub** `GITHUB`                                    |
| ----------------- | ---------------------------------------- | -------------------------------------------------------------- |
| What              | A version control program                | A website hosting Git repositories                             |
| Where it runs     | Your computer                            | Someone else's servers                                         |
|                   |                                          |                                                                |
| Needs internet    | No                                       | Yes                                                            |
| Owns your history | You do                                   | You keep a copy; they host one                                 |
| Alternatives      | Mercurial, SVN, Fossil                   | GitLab, Bitbucket, Codeberg, self-hosted Gitea                 |
| Provides          | commits, branches, merges, history, tags | pull requests, issues, reviews, Actions, releases, permissions |

**Things that are Git:** `commit`, `branch`, `merge`, `rebase`, `tag`, `stash`, `log`, `reflog`.

**Things that are GitHub, not Git:** pull requests, issues, code review, forks (as a button),
Actions, releases pages, branch protection, CODEOWNERS, stars, discussions.

A "pull request" does not exist in Git. It is GitHub's user interface for the request *"please
merge my branch into yours, and let's discuss it first."* GitLab calls the same idea a merge
request. This is why you can use Git perfectly well with no GitHub account — and why GitHub
skills are a separate (and also employable) thing.

---

## E. Brainstorm — predict first

Write your answer before checking.

1. Your laptop is stolen. You had pushed to GitHub yesterday, and made 3 commits this morning.
   What survives?
2. GitHub goes down for a day. Which of these still work: `commit`, `branch`, `merge`, `log`,
   `push`, `pull`, opening a pull request?
3. You and a teammate both `git clone` the same repository, then both commit locally without
   talking. Whose history is "correct"?

<details>
<summary><strong>Answers</strong></summary>

1. Everything up to yesterday's push survives on GitHub — and also in *any* teammate's clone.
   This morning's 3 commits are gone; they existed only on that laptop. This is the practical
   reason to push often, and the reason "committed" ≠ "safe."
2. `commit`, `branch`, `merge`, `log` work — all local. `push` and `pull` fail — they need the
   remote. Opening a pull request fails — it is a GitHub feature, and GitHub is down. This split
   *is* the Git/GitHub boundary, felt physically.
3. Both. Neither. Git has no notion of a correct history — that is what "distributed" means. The
   two histories have **diverged**, and someone must reconcile them (merge or rebase, CH-05/CH-13).
   Git's job is to make the divergence visible and the reconciliation safe, not to decide for you.

</details>

I. Common mistakes

| Symptom                                       | Cause                                                          | Fix                                              | Prevention                             |
| --------------------------------------------- | -------------------------------------------------------------- | ------------------------------------------------ | -------------------------------------- |
| "I pushed but my teammate can't see it"       | Believing`commit` shares work                                | `git push` (CH-06)                             | Remember: commit = local, push = share |
| "Git deleted my files"                        | Switching branches with uncommitted work, or a`reset --hard` | Almost always recoverable (CH-12)                | Commit before switching                |
| "I need internet to commit"                   | Confusing Git with GitHub                                      | Nothing to fix — you don't                      | Keep the Git/GitHub table in mind      |
| "I'll learn GitHub, that's the important one" | Backwards                                                      | Learn Git first; GitHub is a thin layer above it | This course's ordering                 |
| Treating GitHub as cloud storage              | Never learned branching                                        | CH-05                                            | Use branches from day one              |

---

## F. Check your understanding

1. Name three questions version control can answer that a folder of dated copies cannot.
2. Give one thing Git can do that GitHub cannot, and one thing GitHub can do that Git cannot.
3. Why can you commit on an aeroplane?
4. Your entire team's laptops are lost, but GitHub is fine. What is the actual state of the
   project?
5. Someone says "we don't need Git, we use Google Drive." Give the strongest *specific* counter —
   and one thing Drive genuinely does better.

<details>
<summary><strong>Answers</strong></summary>

1. Who changed this line and why; what changed between the working and broken versions; what did
   the file look like at any past moment. (Also: which changes are in the shipped version.)
2. Git can merge two divergent histories, offline. GitHub can host a review conversation attached
   to a proposed change (pull request) — a social/process feature Git has no concept of.
3. Git's history lives in the `.git` folder on your machine. Committing is a local write to that
   folder; no server is involved.
4. Whatever was last pushed exists and the project is alive. Anything committed but not pushed is
   gone. This is why "push at end of day" is a real practice, not ceremony.
5. Drive versions whole files with no *reason* attached, cannot merge two people's edits to the
   same code file line-by-line, has no branches, and cannot tell you which version is deployed.
   What Drive genuinely does better: real-time simultaneous editing of prose, and zero learning
   curve. Being fair about this is more convincing than pretending Git wins everywhere.

</details>
