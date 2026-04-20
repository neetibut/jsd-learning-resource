# The Spine Project — Job Board for Developers

One app. 12 weeks. From static HTML to full-stack MERN.

The spine project is a job board where developers can browse and post jobs. You build it incrementally — each module in the curriculum adds a new layer. By Week 12 you have a deployed app with a database, a REST API, a React frontend, authentication, and an AI-assisted search feature.

**Why "spine"?** Your spine holds your whole body together — it's the structure everything else connects to. This project works the same way. Every topic in the curriculum (HTML, CSS, JavaScript, Node, MongoDB, React, auth, AI) slots into the same running app. You don't learn concepts in isolation and forget them. You layer them, week by week, onto something real. By Week 12, `git log` is your learning timeline.

The week mapping below is a starting point, not a contract. Your cohort's pace may vary. The module numbering (00–07) is stable; the week assignments are flexible.

---

## Week-by-Week Progression

| Week | App State | Module | Tag |
|------|-----------|--------|-----|
| 1 | Static HTML page listing 5 hardcoded jobs | [01-foundations/html](../01-foundations/html/README.md) | `spine-week-01` |
| 2 | CSS styling added, responsive layout | [01-foundations/css](../01-foundations/css/README.md) | `spine-week-02` |
| 3 | JS renders job list dynamically from an array | [02-javascript/basics](../02-javascript/basics/README.md) | `spine-week-03` |
| 4 | Async fetch call replacing hardcoded array (mock API) | [02-javascript/async](../02-javascript/async/README.md) | `spine-week-04` |
| 5 | Node.js environment set up, server runs with `npm start` | [03-backend/nodejs](../03-backend/nodejs/README.md) | `spine-week-05` |
| 6 | Express backend serving jobs via REST API | [03-backend/express](../03-backend/express/README.md) | `spine-week-06` |
| 7 | MongoDB storing and serving real job data | [04-database/mongodb](../04-database/mongodb/README.md) | `spine-week-07` |
| 8 | Mongoose models and schema validation added | [04-database/mongoose](../04-database/mongoose/README.md) | `spine-week-08` |
| 9 | React frontend fetching and displaying jobs from the API | [05-frontend/react-basics](../05-frontend/react-basics/README.md) | `spine-week-09` |
| 10 | React Router: job detail page, filters with hooks | [05-frontend/hooks](../05-frontend/hooks/README.md) + [routing](../05-frontend/routing/README.md) | `spine-week-10` |
| 11 | JWT auth + user can post jobs + deployed to Render | [06-fullstack](../06-fullstack/connecting-mern/README.md) | `spine-week-11` |
| 12 | AI-assisted job search via LLM API (mock path available) | [07-ai-age/llm-api](../07-ai-age/llm-api/README.md) | `spine-week-12` |

**Git tags** (`spine-week-01` through `spine-week-12`) are on the [job-board-spine](https://github.com/your-org/job-board-spine) repo — not this curriculum repo. To see the app at a given week: `git checkout spine-week-06`.

---

## Integration Checklist

> 📋 **INTEGRATION-CHECKLIST.md** — coming after the job-board-spine repo is set up. Will cover: commands to run at each tag, what to verify (job list renders at `localhost:5173`), and common failure modes per week.

---

← [Curriculum Map](../README.md)
