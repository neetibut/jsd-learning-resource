# Spine Integration Checklist

Use this checklist to verify that each `spine-week-XX` tag is independently runnable. Run through it before assigning a week to students, and again after any changes to the spine repo.

**Spine repo:** `https://github.com/your-org/job-board-spine`

---

## How to check out a week

```bash
git clone https://github.com/your-org/job-board-spine
cd job-board-spine
git checkout spine-week-06   # replace with the week you want to verify
```

---

## Week-by-week checklist

### spine-week-00 — Empty scaffold

```bash
git checkout spine-week-00
```

- [ ] Repo contains only `README.md` and `.gitignore`
- [ ] No `node_modules/`, no `backend/`, no `frontend/`

---

### spine-week-01 — Static HTML

```bash
git checkout spine-week-01
open index.html          # or drag into browser
```

- [ ] Page loads without errors
- [ ] 5 job listings visible (hardcoded in HTML)
- [ ] No JavaScript, no CSS

---

### spine-week-02 — CSS styling

```bash
git checkout spine-week-02
open index.html
```

- [ ] Jobs display with card styling and spacing
- [ ] Layout is readable on a narrow window (≤ 600px)
- [ ] `style.css` is linked in `<head>`

---

### spine-week-03 — JS renders from array

```bash
git checkout spine-week-03
# Requires a local server (file:// fetch won't work in some browsers)
npx serve .              # then open localhost:3000
```

- [ ] Job list renders dynamically (no hardcoded `<article>` in HTML)
- [ ] Removing one job from the `JOBS` array in `app.js` removes it from the page
- [ ] No console errors

---

### spine-week-04 — Async fetch from mock API

```bash
git checkout spine-week-04
npx serve .              # must use a server; fetch() blocks on file://
```

- [ ] `api/jobs.json` exists
- [ ] Job list loads via `fetch('api/jobs.json')` (check Network tab)
- [ ] No console errors
- [ ] Page still shows jobs if you add a job to `api/jobs.json` and refresh

---

### spine-week-05 — Node.js server

```bash
git checkout spine-week-05
cd backend
npm install
npm start
```

- [ ] Terminal prints `Server running on http://localhost:3001`
- [ ] `curl http://localhost:3001/api/jobs` returns the jobs JSON
- [ ] No errors on startup

---

### spine-week-06 — Express REST API

```bash
git checkout spine-week-06
cd backend
npm install
npm start
```

- [ ] Terminal prints `Express server on port 3001`
- [ ] `GET http://localhost:3001/api/jobs` returns jobs array
- [ ] `GET http://localhost:3001/api/jobs/1` returns a single job
- [ ] `GET http://localhost:3001/api/jobs/999` returns `{ "error": "Job not found" }` with status 404

---

### spine-week-07 — MongoDB

> Requires MongoDB running locally: `mongod` or Docker `mongo`

```bash
git checkout spine-week-07
cd backend
cp .env.example .env     # set MONGO_URI=mongodb://localhost:27017/jobboard
npm install
npm run seed             # seed the database
npm start
```

- [ ] Terminal prints `Connected to MongoDB` then `Server on port 3001`
- [ ] `GET http://localhost:3001/api/jobs` returns 5 seeded jobs with `_id` fields
- [ ] `GET http://localhost:3001/api/jobs/<_id>` returns a single job
- [ ] Jobs persist after restarting the server (not just in-memory)

---

### spine-week-08 — Mongoose models

> Requires MongoDB running locally.

```bash
git checkout spine-week-08
cd backend
cp .env.example .env
npm install
npm run seed
npm start
```

- [ ] Jobs have `tags` array and `createdAt`/`updatedAt` timestamps
- [ ] Posting a job with a missing required field returns a validation error
  ```bash
  curl -X POST http://localhost:3001/api/jobs \
    -H "Content-Type: application/json" \
    -d '{"title":"Test"}'
  # Expected: 400 with error message about missing fields
  ```
- [ ] `POST /api/jobs` with valid data creates and returns the job

---

### spine-week-09 — React frontend

> Requires backend running (spine-week-08 or later).

```bash
# Terminal 1 — backend
cd backend && npm start

# Terminal 2 — frontend
git checkout spine-week-09
cd frontend
npm install
npm run dev
```

- [ ] Vite starts and prints `Local: http://localhost:5173`
- [ ] Open `localhost:5173` — job list renders
- [ ] Jobs come from the API (check Network tab: request to `/api/jobs`)
- [ ] No console errors

---

### spine-week-10 — React Router + filters

> Requires backend running.

```bash
# Terminal 1
cd backend && npm start

# Terminal 2
git checkout spine-week-10
cd frontend && npm install && npm run dev
```

- [ ] Job list loads at `localhost:5173`
- [ ] Filter buttons (All / Full-time / Remote / Contract) narrow the list
- [ ] Clicking a job card navigates to `/jobs/<id>` (job detail page)
- [ ] Browser back button returns to the list
- [ ] Direct URL `localhost:5173/jobs/<valid-id>` loads the detail page

---

### spine-week-11 — JWT auth + post jobs

> Requires backend running and MongoDB.

```bash
# Terminal 1
cd backend
cp .env.example .env     # set MONGO_URI and JWT_SECRET
npm install && npm run seed && npm start

# Terminal 2
git checkout spine-week-11
cd frontend && npm install && npm run dev
```

- [ ] Register at `/register` — redirects to home, jobs visible
- [ ] Log in at `/login` with same credentials — "Post a Job" button appears in navbar
- [ ] Post a job — new job appears in the list
- [ ] Log out — "Post a Job" button disappears
- [ ] `DELETE /api/jobs/<id>` without a token returns 401
- [ ] `render.yaml` exists at repo root

---

### spine-week-12 — AI search

> Requires backend running, MongoDB, and an OpenAI API key.
> **Mock path available:** if no API key, keyword search is the fallback — still verify that.

```bash
# Terminal 1
cd backend
cp .env.example .env     # set MONGO_URI, JWT_SECRET, OPENAI_API_KEY (or leave blank for mock)
npm install && npm run seed && npm start

# Terminal 2
git checkout spine-week-12
cd frontend && npm install && npm run dev
```

**With API key:**
- [ ] Search "remote backend role" — returns semantically relevant jobs (not just keyword matches)
- [ ] Search "something completely unrelated" — returns empty or low-relevance results

**Without API key (keyword fallback):**
- [ ] Search "React" — returns jobs with React in title or tags
- [ ] Search "Remote" — returns remote jobs
- [ ] No 500 errors; fallback is silent

---

## Common failure modes

| Symptom | Likely cause |
|---------|-------------|
| `Cannot find module 'express'` | Ran `npm start` before `npm install` |
| `MongoServerError: connect ECONNREFUSED` | MongoDB not running — start `mongod` or Docker |
| CORS error in browser console | Backend not running, or running on wrong port |
| Blank page, no console errors | Vite proxy not configured — check `vite.config.js` |
| `401 Unauthorized` on POST | Token missing or expired — log out and log back in |
| AI search returns 500 | Invalid or missing `OPENAI_API_KEY` — keyword fallback should catch this |

---

← [Spine Project](README.md) | [Curriculum Map](../README.md)
