# Node.js

> **Before this:** Complete [02-javascript](../../02-javascript/dom/README.md) first.

---

## The Mental Model

Node.js is JavaScript running outside the browser. The browser gave JavaScript access to the DOM and user events; Node.js gives JavaScript access to the file system, the network, and the operating system. Same language, different environment, different APIs. This is why you can use JavaScript for both the frontend (browser) and the backend (server) — which is the whole point of the MERN stack.

Node.js uses the same event-driven, non-blocking model as browser JavaScript. When your server receives a database query, it does not sit and wait; it registers what to do when the result arrives and handles other requests in the meantime. This makes Node.js efficient for servers that handle many simultaneous connections — which describes most web applications. The npm ecosystem (hundreds of thousands of packages) exists because of Node.js; when you run `npm install express`, you are pulling in code that runs in this environment.

The shift from browser JavaScript to Node.js is mostly about what is available. There is no `document`, no `window`, no `fetch` (until recently). Instead there is `require`, `process`, `__dirname`, and modules from the Node standard library like `fs` (file system) and `http`. In Week 6 you will stop using the raw `http` module and use Express instead — but understanding what Express is doing underneath makes you a better Express developer.

---

## The Code

```js
// server.js — Week 5: a Node.js HTTP server without any frameworks

const http = require('http');  // Node's built-in http module

// The request handler: called once for every incoming HTTP request.
// req = everything the client sent (URL, method, headers, body)
// res = your tools for sending a response back
const server = http.createServer((req, res) => {

  // Route: respond to GET /api/jobs
  if (req.method === 'GET' && req.url === '/api/jobs') {
    const jobs = [
      { id: 1, title: 'Frontend Developer', company: 'Acme Corp' },
      { id: 2, title: 'Backend Engineer',   company: 'StartupTH'  },
    ];

    // Set the Content-Type header so the browser knows this is JSON
    res.writeHead(200, { 'Content-Type': 'application/json' });
    res.end(JSON.stringify(jobs));

  } else {
    // Everything else: 404
    res.writeHead(404, { 'Content-Type': 'application/json' });
    res.end(JSON.stringify({ error: 'Not found' }));
  }
});

// Start listening. process.env.PORT falls back to 3000 for local dev.
const PORT = process.env.PORT || 3000;
server.listen(PORT, () => {
  console.log(`Server running at http://localhost:${PORT}`);
});
```

```json
// package.json — required before running any Node.js project
{
  "name": "job-board-api",
  "version": "1.0.0",
  "main": "server.js",
  "scripts": {
    "start": "node server.js",
    "dev": "node --watch server.js"
  }
}
```

---

## Try It Yourself

Create a `backend/` folder in your `job-board-spine` repo. Inside it, run `npm init -y` to generate `package.json`, then create `server.js` with the code above. Run `npm start` and verify the server starts. Open `http://localhost:3000/api/jobs` in your browser — you should see JSON. Then open `http://localhost:3000/anything-else` and confirm you get a 404 response. Commit with `Week 5: Node.js server serving hardcoded jobs`. (Week 5)

---

## Go Deeper

- [READ] Node.js official docs: Introduction to Node.js — https://nodejs.org/en/learn/getting-started/introduction-to-nodejs — read "Introduction to Node.js", "Differences between Node.js and the Browser", and "The Node.js Event Loop"; skip the advanced internals sections
- [READ] Node.js official docs: npm package manager — https://nodejs.org/en/learn/getting-started/an-introduction-to-the-npm-package-manager — short page; read fully; pay attention to `devDependencies` vs `dependencies`

---

## The AI Age

**When to use AI for this topic:** Ask AI to explain what a Node.js module does. Paste `require('someModule')` and ask what the module provides. Also good for decoding `package.json` scripts you did not write.

**What AI gets wrong:** AI sometimes generates CommonJS (`require`) and ES Module (`import`) syntax in the same file, which causes Node.js to throw a `SyntaxError`. Stick to CommonJS (`require`/`module.exports`) for Node.js backend code until you have explicitly configured `"type": "module"` in `package.json`.

**Try this prompt in Cursor:**
```
I am building a Node.js HTTP server (no frameworks). Write a request handler
that responds to GET /api/jobs with a JSON array of 3 job objects, and returns
a 404 JSON response for all other routes. Use CommonJS (require, not import).
```

---

← [The DOM](../../02-javascript/dom/README.md) | [Curriculum Map](../../README.md) | [Next: Express →](../express/README.md)
