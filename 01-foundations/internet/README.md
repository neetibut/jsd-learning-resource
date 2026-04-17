# How the Internet Works

> **Before this:** Complete [Version Control](../version-control/README.md) first.

---

## The Mental Model

When you type a URL into a browser, a conversation happens. Your browser sends a request to a server; the server sends back a response. That request-response cycle, governed by a protocol called HTTP, is the foundation of everything you will build in this curriculum. Your Express server (Week 6) will receive HTTP requests and send HTTP responses. Your React app (Week 9) will make HTTP requests to fetch data. Understanding the protocol before you build on top of it means you will understand what is happening when things go wrong.

The key concepts are: URLs (which identify resources), HTTP methods (GET means 'give me this', POST means 'here is something new', PUT means 'replace this', DELETE means 'remove this'), status codes (200 means OK, 201 means created, 400 means the client sent bad data, 401 means not authenticated, 404 means not found, 500 means the server broke), and headers (metadata attached to every request and response — things like content type and authentication tokens). DNS is the system that translates a domain name like `example.com` into an IP address so your request can find the right server.

The browser's developer tools Network tab is your window into this conversation. Every request your page makes, every response it receives, the status codes, the headers, the timing — it is all there. Get in the habit of opening it. When your React app cannot fetch jobs from your Express server (Week 9), the answer will almost always be visible in the Network tab before it is visible anywhere else.

---

## The Code

```js
// This is what an HTTP conversation looks like at the code level.
// You are not writing this yet — but you will in Week 6.
// Read it to understand what Express is doing under the hood.

// THE REQUEST (what the browser sends)
// GET /api/jobs HTTP/1.1
// Host: localhost:3000
// Accept: application/json

// THE RESPONSE (what your server sends back)
// HTTP/1.1 200 OK
// Content-Type: application/json
//
// [{ "title": "Frontend Developer", "company": "Acme Corp" }, ...]

// ---------------------------------------------------------------
// You can observe real HTTP conversations right now, without any code.
// Open your browser's DevTools (F12), go to the Network tab,
// then navigate to any website. Each row is one HTTP request.
// Click a row to see the full request headers, response headers,
// and response body.
// ---------------------------------------------------------------

// In Week 4 you will write this:
async function fetchJobs() {
  // This sends a GET request to /api/jobs
  const response = await fetch('/api/jobs');

  // response.status is the HTTP status code (200, 404, 500, etc.)
  if (!response.ok) {
    throw new Error(`Request failed with status ${response.status}`);
  }

  // The server sent JSON; .json() parses the response body
  const jobs = await response.json();
  return jobs;
}
```

---

## Try It Yourself

Open your browser's DevTools (F12 → Network tab) and visit any website. Find a request that returns JSON (look for `fetch` or `xhr` type requests, or try visiting a URL like `https://jsonplaceholder.typicode.com/posts/1` directly). Identify: the URL, the HTTP method, the status code, and one response header. Then open the Network tab while your `index.html` job board is open — notice how even a static page makes requests (for `styles.css`, favicon, etc.). (Pre-Week 5)

---

## Go Deeper

- [VIDEO] Fireship: HTTP Crash Course — search YouTube "Fireship HTTP explained in 100 seconds" — watch the full 5-minute version, not just the 100-second one; it covers status codes and methods clearly
- [READ] MDN: An overview of HTTP — https://developer.mozilla.org/en-US/docs/Web/HTTP/Overview — read the "HTTP Messages" section carefully; the request/response structure diagrams are the most useful part
- [READ] MDN: HTTP response status codes — https://developer.mozilla.org/en-US/docs/Web/HTTP/Status — bookmark this reference; you will check it every time you build an API endpoint

---

## The AI Age

**When to use AI for this topic:** Ask AI to explain a specific HTTP status code you are seeing in your Network tab. Paste the request URL, method, and status code. AI gives accurate, context-specific explanations for HTTP errors.

**What AI gets wrong:** AI sometimes conflates HTTP status codes with application-level error handling. A server that always returns 200 with `{ "error": "not found" }` in the body is using HTTP incorrectly — but AI-generated server code often does this. Always return the semantically correct status code (404 for not found, 401 for unauthenticated, etc.).

**Try this prompt in Cursor:**
```
I'm seeing this in my browser's Network tab:
URL: [paste URL]
Method: [GET/POST/etc]
Status: [paste status code]
Response body: [paste response]

What does this status code mean in this context, and what is likely
causing it on the server side?
```

---

← [Version Control](../version-control/README.md) | [Curriculum Map](../../README.md) | [Next: JavaScript Basics →](../../02-javascript/basics/README.md)
