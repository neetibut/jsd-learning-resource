# JavaScript Basics

> **Before this:** Complete [01-foundations](../../01-foundations/internet/README.md) first.

---

## The Mental Model

JavaScript is the only programming language that runs natively in browsers. That is its origin story and still its most important fact. Everything you learn here — variables, functions, objects, classes — is the same JavaScript that makes web pages interactive, runs Node.js servers, and powers the React components you will write in Week 9.

The mental model that unlocks JavaScript is understanding that functions are values. You can store a function in a variable, pass it as an argument to another function, and return it from a function. This is what makes callbacks and event listeners work. When you see `button.addEventListener('click', handleClick)`, you are passing the `handleClick` function as a value to `addEventListener`, which will call it later when the click happens. If you understand that, the rest of the language becomes much more readable.

Classes are just a cleaner syntax for creating objects with shared behavior. Under the hood, JavaScript is still using prototypes — but you will rarely need to think about that. Think of a class as a blueprint: you define the shape once, then create as many instances as you need. For the job board, `const job = new Job('Frontend Developer', 'Acme Corp')` is cleaner and less error-prone than manually constructing plain objects everywhere.

---

## The Code

```js
// app.js — Week 3: JavaScript takes over the job list

// --- 1. Data: the job list as an array of objects ---
const jobs = [
  { id: 1, title: 'Frontend Developer',  company: 'Acme Corp',        location: 'Bangkok',    type: 'Full-time' },
  { id: 2, title: 'Backend Engineer',    company: 'StartupTH',        location: 'Remote',     type: 'Full-time' },
  { id: 3, title: 'Junior Web Developer',company: 'Digital Agency BKK',location: 'Bangkok',   type: 'Part-time' },
  { id: 4, title: 'Full Stack Developer',company: 'FinTech Co',        location: 'Chiang Mai', type: 'Full-time' },
  { id: 5, title: 'DevOps Engineer',     company: 'CloudBase TH',      location: 'Remote',     type: 'Contract'  },
];

// --- 2. A function that renders jobs into the DOM ---
// Notice: the function takes jobs as a parameter — it does not
// reach for the global array directly. This makes it reusable.
function renderJobs(jobList) {
  const container = document.querySelector('#jobs-list');

  // .map() transforms each job object into an HTML string.
  // .join('') collapses the array of strings into one string.
  container.innerHTML = jobList
    .map(job => `
      <li class="job-card" data-job-id="${job.id}">
        <h2 class="job-title">${job.title}</h2>
        <p class="job-meta">${job.company} &middot; ${job.location} &middot; ${job.type}</p>
      </li>
    `)
    .join('');
}

// --- 3. A class to model a job (used in Week 8 with Mongoose) ---
// For now, it is just good practice — classes make the shape explicit.
class Job {
  constructor(title, company, location, type) {
    this.title = title;
    this.company = company;
    this.location = location;
    this.type = type;
  }

  // A method — shared behavior attached to every Job instance
  getSummary() {
    return `${this.title} at ${this.company} (${this.location})`;
  }
}

// --- 4. Run it ---
renderJobs(jobs);

// Test the class in the browser console:
// const j = new Job('Developer', 'Acme', 'Bangkok', 'Full-time');
// console.log(j.getSummary());
```

---

## Try It Yourself

Create `app.js` in your `job-board-spine` folder. Move the 5 hardcoded job objects out of your HTML and into a JavaScript array. Write a `renderJobs` function that builds the `<li>` elements using `.map()` and sets `innerHTML` on `#jobs-list`. Remove the hardcoded `<li>` elements from `index.html` and add `<script src="app.js"></script>` before the closing `</body>` tag. Reload the page — the list should look identical to Week 2, but it is now powered by JavaScript. Commit with `Week 3: JavaScript renders job list from array`. (Week 3)

---

## Go Deeper

- [READ] MDN: JavaScript basics — https://developer.mozilla.org/en-US/docs/Learn/Getting_started_with_the_web/JavaScript_basics — read the full page; pay special attention to "Functions" and "Events"
- [READ] javascript.info: The JavaScript language (Part 1) — https://javascript.info — work through "JavaScript Fundamentals" (chapters 2.1–2.18); this is the best free JS reference available; skip the "Objects: the basics" section for now and return to it when you reach classes
- [VIDEO] Fireship: JavaScript in 100 Seconds — search YouTube "Fireship JavaScript 100 seconds" — good 2-minute orientation before reading the longer resources above

---

## The AI Age

**When to use AI for this topic:** Ask AI to explain a JavaScript error message. Paste the full error (including the stack trace) and the code that caused it. AI is accurate on common errors like `TypeError: Cannot read properties of undefined` and `ReferenceError: x is not defined`.

**What AI gets wrong:** AI tends to use `var` in generated code, which has different scoping rules from `let` and `const`. Always replace `var` with `const` (if the value never changes) or `let` (if it does). Also watch for AI using `==` instead of `===` — always use strict equality.

**Try this prompt in Cursor:**
```
Explain this JavaScript error and show me exactly where in my code it
is coming from:

Error: [paste full error message and stack trace]

Code:
[paste your JavaScript]
```

---

← [How the Internet Works](../../01-foundations/internet/README.md) | [Curriculum Map](../../README.md) | [Next: Async JavaScript →](../async/README.md)
