# The DOM

> **Before this:** Complete [Async JavaScript](../async/README.md) first.

---

## The Mental Model

The DOM (Document Object Model) is the browser's in-memory representation of your HTML page as a tree of objects. When the browser loads an HTML file, it parses the markup and builds this tree. JavaScript can then read and modify that tree, which is what makes pages interactive. When you `document.querySelector('#jobs-list')` you are reaching into that tree to grab a specific node. When you set `element.textContent = 'Loading...'` you are modifying the tree, and the browser immediately updates what's displayed.

The event system is layered on top of the DOM: browsers emit events (click, submit, keydown) on specific nodes, and you attach listeners to respond to them. Understanding that events 'bubble' up from the target element through its parents explains why you can sometimes attach a single listener to a container and catch events from all its children — a pattern called event delegation that you will use on the job board's job list. Instead of attaching a click listener to each job card, you attach one listener to the `<ul>` and check `event.target` to find which card was clicked.

Starting in Week 9, React will take over DOM manipulation entirely — you stop calling `querySelector` and `innerHTML` and instead describe what the UI should look like, and React handles the DOM updates. But the DOM knowledge does not become useless: when a React component behaves unexpectedly, understanding the underlying DOM is what lets you debug it. The tools (browser devtools, event listeners, element inspection) are the same.

---

## The Code

```js
// app.js — DOM manipulation patterns used in the job board

// --- 1. Selecting elements ---
const jobsList = document.querySelector('#jobs-list');     // single element
const cards    = document.querySelectorAll('.job-card');   // NodeList (like an array)

// --- 2. Showing a loading state while fetch is in progress ---
function showLoading() {
  jobsList.innerHTML = '<li class="loading">Loading jobs...</li>';
}

// --- 3. Rendering jobs (from Week 3) ---
function renderJobs(jobs) {
  jobsList.innerHTML = jobs
    .map(job => `
      <li class="job-card" data-job-id="${job.id}">
        <h2 class="job-title">${job.title}</h2>
        <p class="job-meta">${job.company} &middot; ${job.location}</p>
      </li>
    `)
    .join('');
}

// --- 4. Event delegation: one listener for all job cards ---
// Rather than adding a click listener to each <li>, we add one to the
// parent <ul>. The event bubbles up from whichever card was clicked.
jobsList.addEventListener('click', (event) => {
  // .closest() walks up the DOM tree to find the nearest matching ancestor.
  // This works even if the user clicked on the <h2> inside the card.
  const card = event.target.closest('[data-job-id]');
  if (!card) return; // click was outside any card

  const jobId = card.dataset.jobId; // reads the data-job-id attribute
  showJobDetail(jobId);
});

// --- 5. Showing a job detail (simple version — Week 9 uses React Router) ---
function showJobDetail(jobId) {
  const job = jobs.find(j => j.id === Number(jobId));
  if (!job) return;

  // For now: update a detail panel. In Week 9 React Router navigates to /jobs/:id
  const detail = document.querySelector('#job-detail');
  if (detail) {
    detail.innerHTML = `
      <h2>${job.title}</h2>
      <p>${job.company} &middot; ${job.location}</p>
      <p>${job.description ?? 'No description available.'}</p>
    `;
  }
}

// --- 6. Entry point ---
async function init() {
  showLoading();
  try {
    const jobs = await fetchJobs();
    renderJobs(jobs);
  } catch (error) {
    jobsList.innerHTML = '<li class="error">Could not load jobs.</li>';
  }
}

init();
```

---

## Try It Yourself

Add event delegation to your job board's `#jobs-list`. When a user clicks a job card, log the job's `data-job-id` attribute to the console to confirm the click is being caught. Then add a `<div id="job-detail">` below the list and update it with the selected job's title and company when a card is clicked. This is the manual DOM version of what React Router will do in Week 10 — building it by hand first makes the React version easier to understand. (Week 4)

---

## Go Deeper

- [READ] javascript.info: Document — https://javascript.info/document — read "DOM tree", "Walking the DOM", "Searching: getElement* and querySelector*", and "Event delegation"; skip "Modifying the document" (you already know innerHTML)
- [READ] MDN: Introduction to events — https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Building_blocks/Events — focus on "Event delegation"; the `closest()` section is exactly the pattern used in the code above

---

## The AI Age

**When to use AI for this topic:** Ask AI to generate event delegation code for a specific pattern. Paste your HTML structure and describe what should happen on click. AI handles the `closest()` pattern correctly most of the time.

**What AI gets wrong:** AI often attaches event listeners inside loops — one listener per element — instead of using event delegation. This leaks memory as the list grows and breaks when elements are re-rendered. If AI writes `cards.forEach(card => card.addEventListener(...))`, ask it to rewrite using a single listener on the parent container.

**Try this prompt in Cursor:**
```
I have a <ul id="jobs-list"> that gets re-rendered dynamically.
Each <li> has a data-job-id attribute.
Write event delegation so that clicking any <li> (or any element inside it)
logs the job ID. Use event.target.closest() and do not attach listeners
to individual <li> elements.
```

---

← [Async JavaScript](../async/README.md) | [Curriculum Map](../../README.md) | [Next: Node.js →](../../03-backend/nodejs/README.md)
