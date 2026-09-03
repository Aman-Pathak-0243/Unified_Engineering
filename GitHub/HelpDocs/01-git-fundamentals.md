# Sheet 01 — Git Fundamentals

## "I want to start tracking a project"

```bash
git init                          # new repository here
git clone <url>                   # copy an existing one, with full history
```

## "I want to set up my identity" (once per machine)

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
git config --global init.defaultBranch main
```

## "I want to know what state my files are in"

```bash
git status          # full report
git status -s        # short form: ?? untracked, M modified, A staged
```

## "I want to save a snapshot"

```bash
git add <file>       # stage it
git add -p           # stage part of it, interactively
git commit -m "type(scope): subject"
```

## "I want to see what I'm about to commit"

```bash
git diff --staged
```

## The mental model, in one line

Working directory (your files) → staging area (your draft commit) → repository (permanent
history). `add` moves forward one step, `commit` moves forward another, `restore`/`reset` move
back. A branch is a pointer, not a copy — see [CH-02](../chapters/02-git-mental-model/).

## Referring to commits

| Notation    | Means                |
| ----------- | -------------------- |
| `HEAD`    | where you are now    |
| `HEAD~3`  | 3 commits back       |
| `main`    | tip of`main`       |
| `a3f9c21` | that specific commit |
