# Deployment

> **Before this:** Complete [JWT Auth](../auth-jwt/README.md) first.

---

## The Mental Model

Deployment means your code runs on someone else's computer, permanently, accessible from any browser in the world. Until now, your app has only run on localhost — your own machine, not visible to anyone else. Deployment changes that. Render is the platform you will use: it pulls your code from GitHub, installs dependencies, and runs your server. When you push a new commit, Render automatically deploys the update.

The main thing deployment forces you to handle is environment variables. On your machine, secrets (database URLs, JWT signing keys, API keys) live in a `.env` file that is never committed to GitHub. On Render, you set these in a dashboard — the platform injects them as environment variables when your server starts. This is why `.env.example` exists in every backend project: it documents which variables are needed without exposing the actual values. After deployment, your backend has a public URL; your React frontend will be configured to call that URL instead of localhost.

You will deploy two separate services: the Express API as a Render Web Service (Node.js), and the React frontend as a Render Static Site (Vite builds to static HTML/CSS/JS that any CDN can serve). The two services are independent — the backend can be redeployed without touching the frontend, and vice versa. The only connection between them is the `VITE_API_URL` environment variable set on the frontend, which points to the backend's public URL.

---

## The Code

```
# backend/.env.example — commit this file; it documents required variables
# Never commit the actual .env file (it is in .gitignore)

MONGODB_URI=
JWT_SECRET=
FRONTEND_URL=
PORT=3000
```

```
# backend/.gitignore — ensure secrets never reach GitHub
node_modules/
.env
```

```json
// backend/package.json — Render uses the "start" script to run the server
{
  "name": "job-board-api",
  "version": "1.0.0",
  "scripts": {
    "start": "node server.js",
    "dev":   "node --watch server.js"
  },
  "engines": {
    "node": ">=20.0.0"
  }
}
```

```
# frontend/.env.production — Vite uses this for production builds
# Set VITE_API_URL to your Render backend URL after deployment
VITE_API_URL=https://job-board-api.onrender.com
```

```js
// backend/server.js — production-ready updates

// 1. Trust the proxy (required on Render and most cloud platforms)
app.set('trust proxy', 1);

// 2. CORS: in production, only allow requests from your Render frontend URL
app.use(cors({
  origin: process.env.FRONTEND_URL,
  methods: ['GET', 'POST', 'PUT', 'DELETE'],
}));

// 3. Helmet adds secure HTTP headers (install with: npm install helmet)
const helmet = require('helmet');
app.use(helmet());

// 4. Graceful shutdown — Render sends SIGTERM before stopping the container
process.on('SIGTERM', () => {
  console.log('SIGTERM received, shutting down gracefully');
  process.exit(0);
});
```

---

## Try It Yourself

1. **Deploy the backend:** Create a new Web Service on [render.com](https://render.com), connect your GitHub repo, set the Root Directory to `backend/`, Build Command to `npm install`, and Start Command to `npm start`. Add your environment variables in the Render dashboard (`MONGODB_URI`, `JWT_SECRET`, `FRONTEND_URL`). Wait for the first deploy to succeed.

2. **Deploy the frontend:** Create a Static Site on Render, set Root Directory to `frontend/`, Build Command to `npm run build`, Publish Directory to `dist/`. Add `VITE_API_URL=https://<your-backend-url>.onrender.com` as an environment variable.

3. **Verify:** Open your deployed frontend URL. The job list should load from your production MongoDB database. Post a job using the auth flow. Commit any fixes with `Week 11: deployed to Render`. (Week 11)

---

## Go Deeper

- [READ] Render docs: Deploy a Node.js app — https://render.com/docs/node-express — follow the "Deploy an Express App" guide; pay attention to the environment variables section and the `PORT` handling
- [READ] Render docs: Static Sites — https://render.com/docs/static-sites — short page; the "Build & Deploy" section explains exactly how Vite's `dist/` folder becomes a deployed site
- [READ] The Twelve-Factor App: Config — https://12factor.net/config — one page; explains why environment variables are the right place for configuration and why hardcoding URLs in code is wrong

---

## The AI Age

**When to use AI for this topic:** Ask AI to help debug a Render deployment failure. Copy the build or runtime logs from the Render dashboard and paste them. AI diagnoses `MODULE_NOT_FOUND` errors, port binding issues, and missing environment variable errors accurately.

**What AI gets wrong:** AI sometimes suggests setting `NODE_ENV=production` manually in your start script. This is already handled by most platforms. More importantly, AI often generates code that listens on a hardcoded port like `3000` instead of `process.env.PORT || 3000` — Render assigns the port dynamically, so hardcoding causes the deployment to fail.

**Try this prompt in Cursor:**
```
My Node.js app deploys to Render but fails to start. Here are the logs:

[paste Render deployment logs]

What is causing the failure and how do I fix it?
```

---

← [JWT Auth](../auth-jwt/README.md) | [Curriculum Map](../../README.md) | [Next: LLM API →](../../07-ai-age/llm-api/README.md)
