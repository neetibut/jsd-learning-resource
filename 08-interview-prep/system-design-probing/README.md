# System Design Probing

> **Before this:** Complete [database-probing](../database-probing/README.md) or finish [06-fullstack/connecting-mern](../../06-fullstack/connecting-mern/README.md) before starting this topic.

---

## The Mental Model

Junior developers get system design questions too — but interviewers adjust the scope. They are not expecting you to design Twitter at scale. They want to see whether you can reason about how components of a system fit together, what the failure points are, and what you would do differently if requirements changed. Your spine project is a perfect system to design your way through because you built it — you know exactly where the seams are.

The key skill in system design interviews is structured decomposition. When asked "how would you design a job board?", the instinct is to start listing technologies. Resist it. Start with the requirements: who uses it, what are the core actions, what does the data look like, how much traffic do we expect? Then move to the API contract: what endpoints does the frontend need? Then the data model. Then infrastructure. Interviewers are watching whether you have a process, not just an answer.

Scalability questions at the junior level are mostly about the basics: what happens if the database grows? What if two users save the same job at the same moment? What if the server goes down? You don't need to know distributed systems to answer these well. You need to know the concepts: indexing, stateless servers, environment variables for configuration, and why you put secrets in `.env` instead of hardcoding them. These are patterns you already used in the spine project — the interview is asking you to articulate them.

---

## The Code

```
// THE SPINE PROJECT ARCHITECTURE — a diagram in words.
// Use this as the basis for your system design explanation.

Browser (React SPA)
  │
  │  HTTP requests (fetch, axios)
  │  Authorization: Bearer <jwt>
  ▼
Express Server (Node.js)
  │
  ├── Middleware layer (runs on every request)
  │     ├── express.json()        — parse request body
  │     ├── cors()                — allow cross-origin requests from React dev server
  │     └── requireAuth()         — verify JWT on protected routes
  │
  ├── Route handlers
  │     ├── POST /api/auth/register
  │     ├── POST /api/auth/login
  │     ├── GET  /api/jobs           (public)
  │     ├── POST /api/jobs           (auth required)
  │     └── POST /api/jobs/:id/save  (auth required)
  │
  └── Mongoose ODM
        │
        ▼
      MongoDB (Atlas)
        ├── users collection
        └── jobs collection
```

```js
// PROBING QUESTION: "What would you change if the job board needed to handle
// 100x more traffic?"
//
// Model answer areas to cover:

// 1. Database: add indexes on queried fields (already done).
//    Move to read replicas for heavy read traffic.
//    Add a caching layer (Redis) for the jobs list — it changes infrequently.

// 2. Server: the Express server is stateless (no session data stored in memory,
//    JWT auth doesn't require shared state), so it can scale horizontally —
//    run multiple instances behind a load balancer without coordination.

// 3. File uploads (if added): don't serve uploaded files from the Express server.
//    Use S3 or similar object storage; serve via CDN.

// 4. Rate limiting: add express-rate-limit to the auth routes to prevent
//    brute-force attacks. This is a one-line change that matters at scale.

// The most important thing to communicate: you understand which parts of the
// current design are stateless (and therefore scalable) vs. stateful (and
// therefore harder to scale).
```

---

## Try It Yourself

Grab a whiteboard, paper, or a blank doc. Without looking at code, draw the architecture of the spine project from browser to database. Label every component and every connection. Then annotate two places where the system could fail and describe what would happen to users when it does.

Do this in 10 minutes. Then compare your diagram against the architecture described in "The Code" above. The gaps between your diagram and the actual system are exactly the topics you need to review.

---

## Go Deeper

- [READ] System Design Primer — https://github.com/donnemartin/system-design-primer — Don't read the whole thing. Read "Step-by-step guide to approaching a system design interview question" and "Scalability for Dummies." That's your interview prep scope.
- [VIDEO] MERN Stack Explained — https://www.mongodb.com/mern-stack — Short official overview. Good for the "explain your stack" question that opens most interviews.
- [READ] The Twelve-Factor App — https://12factor.net/ — Read factors III (Config), IV (Backing services), and VI (Processes). These three explain why you put secrets in `.env`, how to think about the database as an attached resource, and why stateless processes are scalable.

---

## The AI Age

**When to use AI for this topic:** Ask AI to play the role of an interviewer running a junior-level system design session: "Walk me through how you'd design a job board application. Ask me questions one at a time, like a real interview." This gives you structured practice with the back-and-forth format.

**What AI gets wrong:** AI system design answers immediately jump to microservices, Kafka, and sharding — appropriate for FAANG, wrong for a junior interview about a MERN app. If you give an AI-generated over-engineered answer in an interview, it signals you don't know the difference between a toy problem and a production problem. Start simple, add complexity only when asked.

**Try this prompt in Cursor:**
```
I'm a junior full-stack developer preparing for a system design interview.
I built a job board application using MongoDB, Express, React, and Node.
Conduct a 15-minute system design interview with me at the appropriate level
for a junior developer role. Ask one question at a time and probe my answers.
Start with: "Tell me about the system you built."
```

---

← [Database Probing](../database-probing/README.md) | [Curriculum Map](../../README.md) | [Next: Problem Solving Probing →](../problem-solving-probing/README.md)
