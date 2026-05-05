# Backend Probing

> **Before this:** Complete [frontend-probing](../frontend-probing/README.md) or have finished [06-fullstack/auth-jwt](../../06-fullstack/auth-jwt/README.md) before starting this topic.

---

## The Mental Model

Backend interviews probe whether you understand *what your server is actually doing* — not whether you can name every Express method. The two things that separate junior candidates who impress from those who don't are: (1) they can reason about the request lifecycle from HTTP verb to database call, and (2) they understand why their auth implementation is secure, not just that it works.

The request lifecycle is the mental model that ties everything together. An HTTP request arrives at your server. Express matches it to a route. Middleware runs in order — authentication, parsing, validation — each one passing the request to the next with `next()`, or short-circuiting with a response. Then the route handler runs, talks to the database, and sends a response. Every backend interview question is really a question about some part of this chain: where does JWT verification happen in that chain? What happens if a middleware doesn't call `next()`? What does `express.json()` actually do and when do you need it?

On auth: interviewers don't ask "how does JWT work?" to see if you know the acronym. They ask because JWT is a common place junior developers make security mistakes — signing with a weak secret, not verifying the token on protected routes, storing the token in `localStorage` where XSS can reach it. If you built it yourself in the spine project and can explain each decision, you are already ahead of candidates who copied it from a tutorial without understanding it.

---

## The Code

```js
// PROBING QUESTION: "Walk me through what happens when a user logs in."
//
// Model answer in code — trace the spine project's login route:

// 1. POST /api/auth/login receives { email, password } in req.body
app.post('/api/auth/login', async (req, res) => {
  const { email, password } = req.body;

  // 2. Find the user by email in MongoDB
  const user = await User.findOne({ email });
  if (!user) return res.status(401).json({ error: 'Invalid credentials' });

  // 3. Compare the submitted password against the stored hash.
  //    bcrypt.compare() rehashes the input and compares — the plain password
  //    is never stored, so even if the database leaks, passwords are safe.
  const valid = await bcrypt.compare(password, user.password);
  if (!valid) return res.status(401).json({ error: 'Invalid credentials' });

  // 4. Sign a JWT with the user's ID. The secret must stay in .env —
  //    anyone who knows the secret can forge tokens.
  const token = jwt.sign({ userId: user._id }, process.env.JWT_SECRET, {
    expiresIn: '7d',
  });

  // 5. Send the token. The client stores it and sends it in the
  //    Authorization header on subsequent requests.
  res.json({ token });
});
```

```js
// PROBING QUESTION: "How do you protect a route so only logged-in users can access it?"
//
// Model answer — the auth middleware used in the spine project:

function requireAuth(req, res, next) {
  // Extract token from the Authorization header: "Bearer <token>"
  const authHeader = req.headers.authorization;
  if (!authHeader) return res.status(401).json({ error: 'No token' });

  const token = authHeader.split(' ')[1];

  try {
    // jwt.verify() checks the signature AND expiry.
    // If either is wrong, it throws — caught below.
    const payload = jwt.verify(token, process.env.JWT_SECRET);
    req.userId = payload.userId; // attach to req for downstream handlers
    next(); // pass control to the actual route handler
  } catch {
    res.status(401).json({ error: 'Invalid token' });
  }
}

// Applied to any route that requires authentication:
app.get('/api/jobs/saved', requireAuth, async (req, res) => {
  const savedJobs = await Job.find({ savedBy: req.userId });
  res.json(savedJobs);
});
```

---

## Try It Yourself

Without looking at your code, describe out loud the full request lifecycle for the spine project's "save a job" feature — from the moment the user clicks "Save" in the React frontend to the moment the database is updated. Cover:

1. What HTTP method and URL the frontend sends
2. What middleware runs before the route handler and why
3. What the route handler does with the database
4. What the frontend does with the response

Time yourself. This should take 3–4 minutes for a complete answer. Under 1 minute means you're skipping important steps.

---

## Go Deeper

- [READ] JWT.io Introduction — https://jwt.io/introduction — Read the full page. It's short. Understand the three parts of a JWT (header, payload, signature) because interviewers ask about them.
- [VIDEO] Middleware in Express.js explained — https://expressjs.com/en/guide/using-middleware.html — Official docs. Read the "Application-level middleware" and "Router-level middleware" sections.
- [READ] OWASP Authentication Cheat Sheet — https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html — Skim the first half. Knowing what common auth mistakes look like makes you sound senior.

---

## The AI Age

**When to use AI for this topic:** Ask AI to generate adversarial interview questions — "what are 5 follow-up questions a senior backend engineer would ask after I explain JWT authentication?" Use it to stress-test your understanding.

**What AI gets wrong:** AI tends to give "best practice" answers that omit the reasoning. It will tell you to store JWT_SECRET in `.env` but not explain *what an attacker can do if they get the secret*. Interviewers who probe past the surface answer expect that reasoning. Fill in the "why" yourself.

**Try this prompt in Cursor:**
```
I'm going to explain how JWT authentication works in the spine project I built.
Ask me one probing follow-up question after each thing I say, like a senior
engineer would in a technical interview. Start by asking me to explain the login flow.
```

---

← [Frontend Probing](../frontend-probing/README.md) | [Curriculum Map](../../README.md) | [Next: Database Probing →](../database-probing/README.md)
