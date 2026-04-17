# Async JavaScript

> **Before this:** Complete [JavaScript Basics](../basics/README.md) first.

---

## The Mental Model

JavaScript runs on a single thread — it can only do one thing at a time. But fetching data from a server takes time, and you cannot freeze the entire browser while waiting. The solution is asynchronous programming: start an operation, register what to do when it finishes, and continue doing other things in the meantime. This is why callbacks, promises, and async/await exist. They are all different syntaxes for the same underlying idea: 'do this, and when it is done, do that.'

Async/await is the modern syntax and the one you should use. It makes asynchronous code look synchronous: `const data = await fetch(url)` reads like a normal assignment even though it pauses execution until the fetch completes. The catch: any function that uses `await` must be marked `async`, and anything that calls that function must also handle the asynchronous nature of it. This propagation is the part that trips people up — but it is consistent once you see it.

Error handling in async code belongs in a `try/catch` block. Unlike synchronous code where a thrown error bubbles up visibly, unhandled errors in async functions disappear silently in older environments. The discipline of wrapping `await` calls in `try/catch` is what separates robust code from code that fails mysteriously in production. For the job board, a failed fetch should show the user an error message — not just silently render an empty list.

---

## The Code

```js
// app.js — Week 4: replace the hardcoded array with a real fetch call

// We will mock the API response locally for now.
// In Week 6, this URL will point to your Express server.
const API_URL = 'https://jsonplaceholder.typicode.com/posts?_limit=5';

// --- Async function to fetch jobs ---
// The 'async' keyword means this function always returns a Promise.
// Inside it, 'await' pauses execution until the Promise resolves.
async function fetchJobs() {
  const response = await fetch(API_URL);

  // fetch() only rejects on network failure — not on 404 or 500.
  // Check response.ok to catch HTTP error status codes.
  if (!response.ok) {
    throw new Error(`Failed to load jobs: ${response.status}`);
  }

  // .json() is also async — it reads and parses the response body.
  const data = await response.json();
  return data;
}

// --- Render function (same as Week 3) ---
function renderJobs(jobs) {
  const container = document.querySelector('#jobs-list');
  container.innerHTML = jobs
    .map(job => `
      <li class="job-card">
        <h2 class="job-title">${job.title}</h2>
        <p class="job-meta">Post #${job.id}</p>
      </li>
    `)
    .join('');
}

// --- Error state ---
function renderError(message) {
  const container = document.querySelector('#jobs-list');
  container.innerHTML = `<li class="error-message">${message}</li>`;
}

// --- Entry point: load and render on page load ---
// try/catch ensures errors surface visibly instead of silently failing.
async function init() {
  try {
    const jobs = await fetchJobs();
    renderJobs(jobs);
  } catch (error) {
    console.error(error);
    renderError('Could not load jobs. Please try again later.');
  }
}

init();
```

---

## Try It Yourself

Replace the hardcoded `jobs` array in your `app.js` with an async `fetchJobs` function that calls `https://jsonplaceholder.typicode.com/posts?_limit=5`. Add a `try/catch` block and render an error message if the fetch fails. Test the error state by changing the URL to something invalid. Once it works, commit with `Week 4: async fetch replaces hardcoded data`. In Week 6 you will update the URL to point to your own Express server. (Week 4)

---

## Go Deeper

- [READ] javascript.info: Promises, async/await — https://javascript.info/async — work through "Callbacks", "Promises", and "Async/await" in order; the callback section explains why async/await exists, which makes it click faster
- [READ] MDN: Using the Fetch API — https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch — focus on "Checking that the fetch was successful" — the `response.ok` pattern is exactly what beginners miss

---

## The AI Age

**When to use AI for this topic:** Ask AI to help you trace the execution order of async code. Paste a snippet and ask "In what order do these lines execute, and why?" AI gives clear, step-by-step explanations of Promise resolution order.

**What AI gets wrong:** AI often forgets `response.ok` checks and writes `const data = await response.json()` directly after `fetch()`. This means a 404 or 500 response will throw a JSON parse error instead of a meaningful error message. Always add the `if (!response.ok)` check manually.

**Try this prompt in Cursor:**
```
Write an async function called fetchJobs that:
1. Fetches from [URL]
2. Checks response.ok and throws a descriptive error if the request failed
3. Returns the parsed JSON
4. Can be called with await inside a try/catch block

Use modern async/await syntax. No .then() chains.
```

---

← [JavaScript Basics](../basics/README.md) | [Curriculum Map](../../README.md) | [Next: The DOM →](../dom/README.md)
