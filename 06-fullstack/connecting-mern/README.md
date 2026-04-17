# Connecting MERN

> **Before this:** Complete [05-frontend](../../05-frontend/routing/README.md) first.

---

## The Mental Model

Connecting the MERN stack means getting your React frontend to talk to your Express backend. This is the week the pieces click together. Your React app (running in the browser on port 5173) makes a `fetch` call to your Express server (running on port 3000). The server queries MongoDB, gets the job data, and sends it back as JSON. React renders it. That is the whole loop.

The practical issue that every beginner hits: CORS. When a browser makes a request from one origin (localhost:5173) to another (localhost:3000), the browser's security model blocks it by default. Your Express server needs to explicitly allow requests from your frontend's origin using the `cors` middleware. This trips up almost everyone the first time — the error appears in the browser console, not in the terminal, which makes it easy to miss. Once cors is configured, the connection works and you have a functioning full-stack application for the first time.

Environment variables are the other piece. Your React app needs to know the URL of your Express server — and that URL changes between local development (`http://localhost:3000`) and production (`https://your-app.onrender.com`). Vite uses `.env` files with a `VITE_` prefix: `VITE_API_URL=http://localhost:3000`. At build time, Vite replaces `import.meta.env.VITE_API_URL` with the actual value. Your code never has the URL hardcoded; the environment provides it.

---

## The Code

```js
// backend/server.js — add CORS to allow requests from the React dev server

const express = require('express');
const cors    = require('cors');
const mongoose = require('mongoose');
const Job     = require('./models/Job');
require('dotenv').config();

const app = express();

// CORS: allow requests from the Vite dev server during development.
// In production, update the origin to your Render frontend URL.
app.use(cors({
  origin: process.env.FRONTEND_URL || 'http://localhost:5173',
  methods: ['GET', 'POST', 'PUT', 'DELETE'],
}));

app.use(express.json());

// ... routes unchanged from Week 8 ...
app.get('/api/jobs', async (req, res) => {
  const jobs = await Job.find().sort({ createdAt: -1 });
  res.json(jobs);
});

mongoose.connect(process.env.MONGODB_URI).then(() => {
  app.listen(process.env.PORT || 3000, () =>
    console.log(`API running on port ${process.env.PORT || 3000}`)
  );
});
```

```
# backend/.env
MONGODB_URI=mongodb+srv://...
FRONTEND_URL=http://localhost:5173
PORT=3000

# backend/.env.example (commit this; never commit .env)
MONGODB_URI=
FRONTEND_URL=
PORT=3000
```

```js
// frontend/src/api.js — a single place to configure the API URL

// import.meta.env.VITE_API_URL is replaced at build time by Vite.
// Falls back to localhost:3000 if the variable is not set.
const BASE_URL = import.meta.env.VITE_API_URL || 'http://localhost:3000';

export async function fetchJobs() {
  const res = await fetch(`${BASE_URL}/api/jobs`);
  if (!res.ok) throw new Error(`HTTP ${res.status}`);
  return res.json();
}

export async function fetchJob(id) {
  const res = await fetch(`${BASE_URL}/api/jobs/${id}`);
  if (!res.ok) throw new Error(`HTTP ${res.status}`);
  return res.json();
}
```

```
# frontend/.env.local (local only, gitignored by Vite by default)
VITE_API_URL=http://localhost:3000
```

---

## Try It Yourself

Run both servers simultaneously: `npm run dev` in `frontend/` and `npm start` in `backend/`. Without CORS, the browser console should show a CORS error when your React app tries to fetch jobs. Add `cors` (`npm install cors` in the backend) and configure the allowed origin. Verify the CORS error disappears and your React job list populates from the real MongoDB database. This is the first time all three layers — React, Express, MongoDB — are running together. Commit with `Week 11: full MERN stack connected`. (Week 11)

---

## Go Deeper

- [READ] MDN: Cross-Origin Resource Sharing (CORS) — https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS — read "Simple requests" and "Preflighted requests"; understanding why CORS exists prevents you from disabling it blindly with `cors({ origin: '*' })` in production
- [READ] Vite docs: Env Variables and Modes — https://vitejs.dev/guide/env-and-mode — short page; read "Env Variables" fully; the `VITE_` prefix requirement and how `import.meta.env` works are exactly what you need

---

## The AI Age

**When to use AI for this topic:** Paste a CORS error from your browser console and ask AI to explain exactly why it is happening and what configuration change on the Express server will fix it. AI gives accurate, targeted fixes for CORS errors.

**What AI gets wrong:** AI often suggests `app.use(cors())` with no configuration — this allows requests from any origin, which is a security risk in production. Always configure `origin` explicitly. In development, allow only your local Vite port. In production, allow only your deployed frontend URL.

**Try this prompt in Cursor:**
```
I have a React app at http://localhost:5173 fetching from an Express API
at http://localhost:3000. I'm getting this CORS error in the browser console:

[paste the exact error message]

Show me the Express cors configuration that fixes this, and explain
why cors() with no options is not acceptable for production.
```

---

← [React Router](../../05-frontend/routing/README.md) | [Curriculum Map](../../README.md) | [Next: JWT Auth →](../auth-jwt/README.md)
