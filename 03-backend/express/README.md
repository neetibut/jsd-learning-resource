# Express

> **Before this:** Complete [Node.js](../nodejs/README.md) first.

---

## The Mental Model

Express is a minimal web framework for Node.js. It does two things: it matches incoming HTTP requests to the right handler, and it provides a clean way to run code before that handler (called middleware). That is almost the entire framework. Its minimalism is a feature — you add only what you need.

A route in Express is a pattern and a handler: 'when a GET request comes in for /jobs, run this function.' The handler receives the request object (everything the client sent: URL, headers, body) and the response object (your way to send something back). Middleware is code that runs between the request arriving and your handler executing: parsing JSON bodies, checking authentication, logging requests. You will write middleware in Week 11 for JWT authentication — the pattern will feel familiar because you have already been using Express's built-in middleware for body parsing since Week 6.

This is the first week where you build something a browser can actually talk to. Your Week 1 HTML page and your Week 9 React app will both ultimately connect to a server that looks like this one. Express's `router` lets you organize routes into separate files as the API grows — in Week 11, auth routes (`/api/auth/register`, `/api/auth/login`) live in their own file, separate from job routes (`/api/jobs`).

---

## The Code

```js
// server.js — Week 6: Express job board API

const express = require('express');
const app = express();

// --- Middleware ---
// express.json() parses incoming request bodies with Content-Type: application/json
// Without this, req.body is undefined on POST/PUT requests.
app.use(express.json());

// --- In-memory data (replaced by MongoDB in Week 7) ---
let jobs = [
  { id: 1, title: 'Frontend Developer',  company: 'Acme Corp',         location: 'Bangkok',    type: 'Full-time' },
  { id: 2, title: 'Backend Engineer',    company: 'StartupTH',         location: 'Remote',     type: 'Full-time' },
  { id: 3, title: 'Junior Web Developer',company: 'Digital Agency BKK',location: 'Bangkok',    type: 'Part-time' },
  { id: 4, title: 'Full Stack Developer',company: 'FinTech Co',         location: 'Chiang Mai', type: 'Full-time' },
  { id: 5, title: 'DevOps Engineer',     company: 'CloudBase TH',       location: 'Remote',     type: 'Contract'  },
];

// --- Routes ---

// GET /api/jobs — return all jobs
app.get('/api/jobs', (req, res) => {
  res.json(jobs);
});

// GET /api/jobs/:id — return a single job by ID
// :id is a route parameter; Express captures it as req.params.id
app.get('/api/jobs/:id', (req, res) => {
  const job = jobs.find(j => j.id === Number(req.params.id));
  if (!job) {
    return res.status(404).json({ error: 'Job not found' });
  }
  res.json(job);
});

// POST /api/jobs — create a new job
app.post('/api/jobs', (req, res) => {
  const { title, company, location, type } = req.body;

  // Basic validation — Week 11 replaces this with express-validator
  if (!title || !company) {
    return res.status(400).json({ error: 'title and company are required' });
  }

  const newJob = {
    id: jobs.length + 1,
    title,
    company,
    location: location ?? 'Remote',
    type: type ?? 'Full-time',
  };

  jobs.push(newJob);
  res.status(201).json(newJob); // 201 Created
});

// --- Start server ---
const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`Job board API running at http://localhost:${PORT}`);
});
```

---

## Try It Yourself

Replace your `backend/server.js` with the Express version above. Run `npm install express` first. Test all three routes using your browser (GET only) and a tool like [Hoppscotch](https://hoppscotch.io) or the VS Code REST Client extension for POST. Verify: `GET /api/jobs` returns 5 jobs, `GET /api/jobs/1` returns the first job, `GET /api/jobs/99` returns a 404, and `POST /api/jobs` with a JSON body creates a new job. Commit with `Week 6: Express REST API for jobs`. (Week 6)

---

## Go Deeper

- [READ] Express official docs: Routing — https://expressjs.com/en/guide/routing.html — read "Route methods", "Route paths", "Route parameters", and "Response methods"; these four sections cover everything you will use in this curriculum
- [READ] Express official docs: Writing middleware — https://expressjs.com/en/guide/writing-middleware.html — short page; read it after you have the routes working; the `next()` function is what makes the middleware chain click

---

## The AI Age

**When to use AI for this topic:** Ask AI to generate a new Express route from a description. Be specific: "Write an Express route that accepts DELETE /api/jobs/:id, finds the job by id, removes it from the array, and returns 204 No Content." AI handles this pattern reliably.

**What AI gets wrong:** AI almost always skips input validation on `req.body`. Any route that accepts user data needs validation — at minimum, check that required fields exist and are the right type. Ask AI explicitly to add validation, or use the `express-validator` package.

**Try this prompt in Cursor:**
```
Add a PUT /api/jobs/:id route to this Express server that:
1. Finds the job by id (return 404 if not found)
2. Updates only the fields provided in req.body (partial update)
3. Returns the updated job
4. Validates that at least one field is provided in the body

Here is the current server.js:
[paste your server.js]
```

---

← [Node.js](../nodejs/README.md) | [Curriculum Map](../../README.md) | [Next: MongoDB →](../../04-database/mongodb/README.md)
