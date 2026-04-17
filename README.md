# JSD Learning Resource — MERN for the AI Age

Build a full-stack job board, week by week.

> **New here? Start at [`00-setup/`](00-setup/)**

A curriculum for Generation Thailand's JSD bootcamp. One project, 12 weeks, from a static HTML page to a deployed MERN app with auth. Every topic connects to the same running application — the [spine project](spine/README.md).

---

## Curriculum Map

| Module | Topics | Weeks | Status |
|--------|--------|-------|--------|
| [00-setup](00-setup/) | VS Code, Node/nvm, npm, terminal | Pre-Week 1 | ✅ Complete |
| [01-foundations](01-foundations/) | HTML, CSS, Git, how the web works | 1–2 | ✅ Complete |
| [02-javascript](02-javascript/) | JS fundamentals, async, DOM | 3–4 | ✅ Complete |
| [03-backend](03-backend/) | Node.js, Express, REST API design | 5–6 | ✅ Complete |
| [04-database](04-database/) | MongoDB, Mongoose, data modeling | 7–8 | ✅ Complete |
| [05-frontend](05-frontend/) | React, state, hooks, routing | 9–10 | ✅ Complete |
| [06-fullstack](06-fullstack/) | Connecting MERN, JWT auth, deployment | 11 | ✅ Complete |
| [07-ai-age](07-ai-age/) | LLM API integration, AI dev workflow | 12 | ✅ Complete |

Status values: `✅ Complete` · `🚧 In progress (N/total)` · `📝 Stub` (Mental Model only) · `🔜 Planned`

---

## What You're Building

The **spine project** is a job board for developers. You start with a static HTML page in Week 1 and end with a full MERN app — MongoDB, Express, React, Node — with JWT auth, deployed to Render, and an AI-assisted job search feature.

The "whoa" moment: your Week 1 HTML file and your Week 12 production app live in the [same repo](https://github.com/your-org/job-board-spine). `git log` is your curriculum timeline. Note: `spine-week-XX` tags are created on the `job-board-spine` repo — not this curriculum repo.

See the full week-by-week progression in [spine/README.md](spine/README.md).

---

## How to Use This Repo

Work through the modules in order (00 → 07). Each topic is self-contained: read the Mental Model, study the code example, do the exercise, go deeper. The exercise always adds a piece to the spine project, so skipping topics will leave your app incomplete.

**Self-paced:** you don't need an instructor. Every topic has enough context to work through on your own.

**AI tools:** each topic has a dedicated "AI Age" section — exact prompts to run in Cursor and what AI commonly gets wrong. Use these as written, then verify the output.

---

## Topics at a Glance

### [00-setup](00-setup/) — Pre-Week 1
- [Environment setup](00-setup/README.md) — VS Code, Node/nvm, npm, terminal basics

### [01-foundations](01-foundations/) — Weeks 1–2
- [HTML](01-foundations/html/README.md) — structure, semantic elements, forms
- [CSS](01-foundations/css/README.md) — layout, flexbox, responsive design
- [Version control](01-foundations/version-control/README.md) — Git, GitHub, branching
- [How the internet works](01-foundations/internet/README.md) — HTTP, DNS, client-server

### [02-javascript](02-javascript/) — Weeks 3–4
- [Basics](02-javascript/basics/README.md) — variables, functions, OOP (classes)
- [Async](02-javascript/async/README.md) — promises, async/await, fetch
- [DOM](02-javascript/dom/README.md) — selecting elements, events, manipulation

### [03-backend](03-backend/) — Weeks 5–6
- [Node.js](03-backend/nodejs/README.md) — runtime, modules, npm scripts
- [Express](03-backend/express/README.md) — server, routing, REST API design

### [04-database](04-database/) — Weeks 7–8
- [MongoDB](04-database/mongodb/README.md) — documents, collections, CRUD
- [Mongoose](04-database/mongoose/README.md) — schemas, models, validation

### [05-frontend](05-frontend/) — Weeks 9–10
- [React basics](05-frontend/react-basics/README.md) — components, props, JSX
- [Hooks](05-frontend/hooks/README.md) — useState, useEffect, custom hooks
- [Routing](05-frontend/routing/README.md) — React Router, dynamic routes

### [06-fullstack](06-fullstack/) — Week 11
- [Connecting MERN](06-fullstack/connecting-mern/README.md) — wiring React to Express
- [JWT auth](06-fullstack/auth-jwt/README.md) — login/register, bcrypt, token verification
- [Deployment](06-fullstack/deployment/README.md) — Render, environment variables

### [07-ai-age](07-ai-age/) — Week 12
- [LLM API](07-ai-age/llm-api/README.md) — AI-assisted job search (USE_MOCK=true available)
- [AI dev workflow](07-ai-age/ai-dev-workflow/README.md) — Cursor/Copilot in a real project
