# Version Control — Git & GitHub

> **Before this:** Complete [CSS](../css/README.md) first.

---

## The Mental Model

A version control system is a time machine for your code. Every `git commit` takes a snapshot of your project at that moment. You can travel back to any snapshot, compare two snapshots, or branch off from any point to try something without risking your working code. That is the whole idea. Everything else — branches, merges, pull requests, remotes — is built on top of that core concept.

Git specifically tracks changes at the line level, which means it can show you exactly what changed between any two points, and it can merge changes from two people working on different parts of the same file. This is why teams use it. For you as a solo learner, the main benefit is safety: you can experiment freely because you can always undo. The discipline of committing often and writing clear commit messages is worth building now, before you work on a team.

GitHub is not Git — it is a website that hosts Git repositories and adds collaboration features (pull requests, issues, code review). The relationship is: Git is the tool on your computer; GitHub is the cloud backup and collaboration layer. When you `git push`, you are sending your local snapshots to GitHub. This is also how the spine project works — your job board lives on GitHub, and each week's state is marked with a tag (`spine-week-01`, `spine-week-02`, etc.) so anyone can check out exactly how the app looks at any point in the curriculum.

---

## The Code

```bash
# --- Initial setup (run once per machine) ---
git config --global user.name "Your Name"
git config --global user.email "you@example.com"

# --- Starting the spine project ---
git init                          # turn the current folder into a git repo
git add index.html styles.css     # stage specific files (prefer this over "git add .")
git commit -m "Week 1: static HTML job board"

# --- After each week's work ---
git add .                         # stage all changes
git status                        # always check what you are about to commit
git commit -m "Week 2: CSS styling and responsive layout"

# --- Pushing to GitHub ---
git remote add origin https://github.com/YOUR_USERNAME/job-board-spine.git
git push -u origin main           # -u sets the upstream so future pushes just need "git push"

# --- Tagging a week (marks the state of the app at that week) ---
git tag spine-week-01             # create a tag at the current commit
git push origin spine-week-01     # push the tag to GitHub

# --- Checking out a past week to see the app at that state ---
git checkout spine-week-01        # moves HEAD to the tagged commit (read-only view)
git checkout main                 # come back to the latest state

# --- The three commands you will use most often ---
git status    # what has changed?
git diff      # what exactly changed (line by line)?
git log       # history of commits
```

---

## Try It Yourself

Initialize a Git repo in your `job-board-spine` folder if you have not already. Stage and commit your `index.html` and `styles.css` files with a descriptive commit message. Create a repository on GitHub, add it as a remote, and push. Then create a `spine-week-01` tag on your first commit and push the tag to GitHub. Verify you can see both the commit and the tag on GitHub. (Weeks 1–2)

---

## Go Deeper

- [READ] Pro Git Book, Chapters 1–3 — https://git-scm.com/book/en/v2 — read Chapter 1 (Getting Started) and Chapter 2 (Git Basics) fully; Chapter 3 (Branching) is worth skimming now and re-reading before Week 11
- [INTERACTIVE] Learn Git Branching — https://learngitbranching.js.org — complete the "Introduction Sequence" (first 4 levels); the visual tree makes branching intuitive in a way text explanations cannot

---

## The AI Age

**When to use AI for this topic:** Ask AI to explain what a git error message means. Paste the exact error and your last 3 commands. AI is very good at "detached HEAD state" and merge conflict explanations.

**What AI gets wrong:** AI often suggests `git push --force` to resolve push conflicts. This is destructive on shared branches — it overwrites other people's work. Always ask AI for the non-destructive solution first (`git pull --rebase` is usually the right answer).

**Try this prompt in Cursor:**
```
I ran these git commands and got this error:
[paste your commands]
[paste the error message]

Explain what went wrong and give me the safest way to fix it.
Do not suggest --force unless there is no other option.
```

---

← [CSS](../css/README.md) | [Curriculum Map](../../README.md) | [Next: How the Internet Works →](../internet/README.md)
