# Environment Setup

> ⚠️ **Stub** — This topic has a Mental Model draft but is not yet complete.
> Full content (code examples, exercises, links) coming soon.
> → [Back to Curriculum Map](../README.md)

---

## The Mental Model

Before you write a single line of JavaScript, your computer needs to speak the same language as every other developer's computer. That's what environment setup is: agreeing on the same tools, the same versions, and the same way of running things.

The most important thing to get right is Node version management. Node.js updates frequently, and different projects need different versions. `nvm` (Node Version Manager) lets you install multiple Node versions and switch between them in seconds. Install nvm first — everything else flows from it.

VS Code is the editor. Not because it's the best editor in the abstract, but because it has the best ecosystem for JavaScript: extensions, debugging, terminal integration, and AI tooling (Copilot, Cursor) all work best here. The terminal inside VS Code is the same terminal as outside — use it.

---

## The Code

*Code example coming — this topic's exercise is setup verification rather than a runnable code file.*

---

## Try It Yourself

Verify your environment is ready:

```bash
node --version    # should print v20.x.x or higher
npm --version     # should print 10.x.x or higher
git --version     # should print git version 2.x.x
code --version    # should print VS Code version
```

If any of these fail, follow the installation steps in the links below before continuing.

---

## Go Deeper

- [READ] nvm README (install instructions) — https://github.com/nvm-sh/nvm#installing-and-updating — follow the "Installing and Updating" section only
- [VIDEO] Traversy Media: VS Code Setup for Web Dev — search YouTube — watch for VS Code extensions; skip the Git section (covered in 01-foundations/version-control)

<!-- Add verified URLs with last-verified dates to LINK-MAINTENANCE.md -->

---

## The AI Age

**When to use AI for this topic:** Ask AI to diagnose setup errors. Paste the exact error message and your OS version. AI is surprisingly good at "nvm: command not found" style errors.

**What AI gets wrong:** AI assumes your shell config file is correct. If you're on macOS with zsh, nvm needs to be in `~/.zshrc` — not `~/.bashrc`. AI often gives the wrong file for your shell.

**Try this prompt in Cursor:**
```
I'm setting up Node.js with nvm on [your OS]. Here's the error I'm seeing:
[paste error message]
My shell is [zsh/bash]. What's wrong and how do I fix it?
```

---

← [Curriculum Map](../README.md) | [Next: HTML →](../01-foundations/html/README.md)
