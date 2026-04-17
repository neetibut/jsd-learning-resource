# JWT Authentication

> **Before this:** Complete [Connecting MERN](../connecting-mern/README.md) first.

---

## The Mental Model

Authentication answers the question: who are you? Authorization answers: what are you allowed to do? JWT (JSON Web Token) is a mechanism for both. When a user logs in with the correct password, your server creates a token — a signed string that encodes the user's identity. The client stores this token (usually in localStorage) and sends it with every subsequent request. The server verifies the token's signature, reads the identity, and knows who is making the request without touching the database again.

The security pieces to understand: passwords are never stored in plain text. You hash them with bcrypt before storing, and you compare hashes on login. Bcrypt is deliberately slow — this makes brute-force attacks expensive. JWT tokens are signed but not encrypted — anyone can decode them and read the payload, so never put sensitive information in a token. The signature just proves the token was issued by your server. In Week 11, authentication gates the 'post a job' feature: only logged-in users can create job listings.

The flow for the job board is: register with email + password → server hashes the password, saves the user, returns a JWT → client stores the JWT in localStorage → subsequent requests include the JWT in the `Authorization: Bearer <token>` header → Express middleware verifies the token before allowing access to protected routes like `POST /api/jobs`.

---

## The Code

```js
// backend/models/User.js

const mongoose = require('mongoose');

const userSchema = new mongoose.Schema({
  email:    { type: String, required: true, unique: true, lowercase: true, trim: true },
  password: { type: String, required: true }, // stored as bcrypt hash
}, { timestamps: true });

module.exports = mongoose.model('User', userSchema);
```

```js
// backend/routes/auth.js — register and login routes

const express  = require('express');
const bcrypt   = require('bcrypt');
const jwt      = require('jsonwebtoken');
const User     = require('../models/User');

const router = express.Router();
const SALT_ROUNDS = 10; // bcrypt cost factor

// POST /api/auth/register
router.post('/register', async (req, res) => {
  try {
    const { email, password } = req.body;
    if (!email || !password) {
      return res.status(400).json({ error: 'email and password are required' });
    }

    // Hash the password — never store plain text
    const hash = await bcrypt.hash(password, SALT_ROUNDS);
    const user = await User.create({ email, password: hash });

    // Sign a token valid for 7 days
    const token = jwt.sign(
      { userId: user._id, email: user.email },
      process.env.JWT_SECRET,
      { expiresIn: '7d' }
    );

    res.status(201).json({ token });
  } catch (error) {
    if (error.code === 11000) { // MongoDB duplicate key (email already exists)
      return res.status(409).json({ error: 'Email already registered' });
    }
    res.status(500).json({ error: 'Registration failed' });
  }
});

// POST /api/auth/login
router.post('/login', async (req, res) => {
  const { email, password } = req.body;

  const user = await User.findOne({ email });
  if (!user) {
    // Return the same error for "user not found" and "wrong password"
    // to avoid leaking which emails are registered
    return res.status(401).json({ error: 'Invalid email or password' });
  }

  const passwordMatch = await bcrypt.compare(password, user.password);
  if (!passwordMatch) {
    return res.status(401).json({ error: 'Invalid email or password' });
  }

  const token = jwt.sign(
    { userId: user._id, email: user.email },
    process.env.JWT_SECRET,
    { expiresIn: '7d' }
  );

  res.json({ token });
});

module.exports = router;
```

```js
// backend/middleware/requireAuth.js — protect routes with this middleware

const jwt = require('jsonwebtoken');

function requireAuth(req, res, next) {
  const authHeader = req.headers.authorization;

  // Expect: Authorization: Bearer <token>
  if (!authHeader || !authHeader.startsWith('Bearer ')) {
    return res.status(401).json({ error: 'No token provided' });
  }

  const token = authHeader.split(' ')[1];

  try {
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    req.user = decoded; // attach user info for downstream handlers
    next();
  } catch (error) {
    return res.status(401).json({ error: 'Invalid or expired token' });
  }
}

module.exports = requireAuth;
```

```js
// backend/server.js — wire up auth routes and protect POST /api/jobs

const authRoutes  = require('./routes/auth');
const requireAuth = require('./middleware/requireAuth');

app.use('/api/auth', authRoutes);

// POST /api/jobs is now protected — only logged-in users can post
app.post('/api/jobs', requireAuth, async (req, res) => {
  try {
    // req.user.userId was set by the requireAuth middleware
    const job = await Job.create({ ...req.body, postedBy: req.user.userId });
    res.status(201).json(job);
  } catch (error) {
    if (error.name === 'ValidationError') {
      return res.status(400).json({ error: error.message });
    }
    res.status(500).json({ error: 'Server error' });
  }
});
```

```
# backend/.env — add JWT_SECRET
JWT_SECRET=replace-with-a-long-random-string-in-production
```

---

## Try It Yourself

Install the packages: `npm install bcrypt jsonwebtoken`. Create the `User` model, the auth router, and the `requireAuth` middleware as shown above. Add `JWT_SECRET` to your `.env` file. Test the full flow with Hoppscotch or the VS Code REST Client: register a user, log in to get a token, then try `POST /api/jobs` without the token (expect 401), and with the token in the `Authorization: Bearer` header (expect 201). Commit with `Week 11: JWT authentication and protected job posting`. (Week 11)

---

## Go Deeper

- [READ] jwt.io: Introduction to JSON Web Tokens — https://jwt.io/introduction — read the full page; the "How do JSON Web Tokens work?" section with the diagram is the clearest explanation of the three-step flow
- [READ] OWASP: Password Storage Cheat Sheet — https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html — skim the "Password Hashing" section; understanding why bcrypt uses a cost factor prevents you from changing it to 1 for performance reasons

---

## The AI Age

**When to use AI for this topic:** Ask AI to review your auth implementation for security issues. Paste your register and login routes and ask for a security review. AI catches common mistakes: same error for "user not found" vs "wrong password" (already handled above), missing rate limiting, storing raw tokens in cookies without httpOnly flag.

**What AI gets wrong:** AI often generates JWT implementations that put sensitive data in the token payload (email, role, permissions) — anything in the payload is visible to anyone who decodes the token. The payload should contain only the user ID and minimal data needed to identify the user. Sensitive lookups should hit the database.

**Try this prompt in Cursor:**
```
Review this Express JWT authentication implementation for security issues.
Focus on: password hashing, error message information leakage, token payload
contents, and any other vulnerabilities. Explain each issue and how to fix it.

[paste your register/login routes and middleware]
```

---

← [Connecting MERN](../connecting-mern/README.md) | [Curriculum Map](../../README.md) | [Next: Deployment →](../deployment/README.md)
