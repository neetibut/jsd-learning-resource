# Problem Solving Probing

> **Before this:** Complete [system-design-probing](../system-design-probing/README.md) or finish [02-javascript/basics](../../02-javascript/basics/README.md) before starting this topic.

---

## The Mental Model

Coding challenges in interviews are not primarily about getting the right answer. They are about watching how you think. An interviewer sitting next to you while you solve a problem is observing your process: do you clarify the requirements before writing code? Do you test your solution against edge cases? Do you communicate your reasoning as you go, or do you go silent for five minutes and produce something no one can follow?

This changes your preparation strategy. You are not training to solve 500 LeetCode problems until you recognize every pattern by reflex. You are training to solve problems *out loud*, clearly, in a way that makes the interviewer feel like a collaborator rather than a spectator. Most junior developer roles do not give you algorithmic hard problems. They give you array manipulations, string parsing, object transformations — the same JavaScript you used throughout this curriculum. The gap is not the problem difficulty. The gap is the habit of narrating your thinking while you work.

The think-aloud technique is simple: before you write any code, say out loud what the function should do and what your approach is. While you write, say what each line does and why. After you write, trace through an example by hand before running it. When you find a bug, say "I expected X but got Y — let me check…" instead of going silent. This is a learnable habit, but only through practice. Typing solutions alone will not build it.

---

## The Code

The walkthroughs below show the think-aloud commentary alongside the code. Read the comments as the narration you should be saying out loud.

```js
// PROBLEM: Given an array of job objects, return only the jobs from a specific location.
//
// Think-aloud before coding:
// "The input is an array of objects. I want to keep some and remove others based
//  on a property value. That's a filter. I'll use Array.filter() and compare
//  the location property to the target string. Edge cases: empty array, no matches,
//  case sensitivity."

function getJobsByLocation(jobs, location) {
  // .filter() returns a new array — doesn't mutate the original, which is good.
  // The callback returns true to keep the item, false to drop it.
  return jobs.filter(job => job.location === location);
}

// Trace through an example:
const jobs = [
  { title: 'Frontend Dev', location: 'Bangkok' },
  { title: 'Backend Dev', location: 'Chiang Mai' },
  { title: 'Designer', location: 'Bangkok' },
];

getJobsByLocation(jobs, 'Bangkok');
// Pass 1: { title: 'Frontend Dev', location: 'Bangkok' } — 'Bangkok' === 'Bangkok' → keep
// Pass 2: { title: 'Backend Dev', location: 'Chiang Mai' } — 'Chiang Mai' === 'Bangkok' → drop
// Pass 3: { title: 'Designer', location: 'Bangkok' } — 'Bangkok' === 'Bangkok' → keep
// Result: [{ title: 'Frontend Dev', ... }, { title: 'Designer', ... }]
```

```js
// PROBLEM: Given an array of job titles, return a new array with each title
// formatted as "Title Case" (first letter of each word capitalized).
//
// Think-aloud before coding:
// "I need to transform each string in the array — that's a map.
//  For each string, I need to capitalize the first letter of each word.
//  I'll split on spaces, capitalize each word, then rejoin.
//  Edge cases: empty string, single word, all-caps input."

function toTitleCase(titles) {
  return titles.map(title => {
    return title
      .split(' ')                          // ['frontend', 'developer']
      .map(word => {
        // Capitalize first char, lowercase the rest.
        // word[0] handles the first letter; .slice(1) gets everything after.
        return word[0].toUpperCase() + word.slice(1).toLowerCase();
      })
      .join(' ');                          // 'Frontend Developer'
  });
}

// Trace:
toTitleCase(['frontend developer', 'UI designer']);
// 'frontend developer' → ['frontend', 'developer'] → ['Frontend', 'Developer'] → 'Frontend Developer'
// 'UI designer' → ['UI', 'designer'] → ['Ui', 'Designer'] → 'Ui Designer'
// Note the edge case: 'UI' becomes 'Ui' — worth mentioning to the interviewer.
```

```js
// PROBLEM: Given an array of job objects with a salary field, return the
// average salary. Return 0 if the array is empty.
//
// Think-aloud:
// "I need to sum all the salaries and divide by the count.
//  .reduce() is the right tool for summing. I'll handle the empty array edge
//  case first with an early return."

function averageSalary(jobs) {
  if (jobs.length === 0) return 0; // edge case: no division by zero

  const total = jobs.reduce((sum, job) => sum + job.salary, 0);
  // reduce walks the array, accumulating into 'sum'.
  // Initial value of 0 means the first iteration adds job.salary to 0.

  return total / jobs.length;
}
```

---

## Try It Yourself

Write a function that takes an array of job objects and returns a count of how many jobs there are per location. Example input:

```js
[
  { title: 'Dev', location: 'Bangkok' },
  { title: 'Designer', location: 'Chiang Mai' },
  { title: 'PM', location: 'Bangkok' },
]
// Expected output: { Bangkok: 2, 'Chiang Mai': 1 }
```

Rules for the exercise:
1. Say your approach out loud before writing any code
2. Narrate each line as you write it
3. Trace through the example by hand before testing
4. Time yourself: aim for under 10 minutes start to finish

The solution uses only the JavaScript from [02-javascript/basics](../../02-javascript/basics/README.md) — no tricks, no obscure methods.

---

## Go Deeper

- [READ] JavaScript Array Methods — MDN — https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array — Don't read the whole page. Know: `filter`, `map`, `reduce`, `find`, `some`, `every`. These cover 90% of interview problems at the junior level.
- [VIDEO] Think Like a Programmer — https://www.youtube.com/watch?v=azcrPFhaY9k — Watch the first 20 minutes. The framing of "inputs, outputs, constraints, edge cases" before writing code is the habit to build.
- [READ] How to Think Like a Computer Scientist: Learning with JavaScript — https://openbookproject.net/thinkcs/javascript/ — Read Chapter 6 (Strings) and Chapter 10 (Arrays). The exercises at the end of each chapter are good interview-caliber practice.

---

## The AI Age

**When to use AI for this topic:** Generate custom practice problems tied to the spine project: "Give me 5 array manipulation problems that use data similar to a job board application (jobs with title, company, location, salary fields). Start easy and get harder." This keeps the problems relevant to what you know.

**What AI gets wrong:** AI-generated solutions jump straight to the optimal answer without narrating the reasoning. In an interview, the process matters as much as the answer. Don't study AI solutions — study your own solutions and use AI to ask "what edge cases am I missing?" after you've already solved it.

**Try this prompt in Cursor:**
```
Give me one JavaScript coding problem involving arrays of objects, appropriate
for a junior developer interview. After I submit my solution, evaluate:
(1) correctness, (2) whether I handled edge cases, (3) whether my code is readable.
Don't give me the solution — just guide me to improve mine.
```

---

← [System Design Probing](../system-design-probing/README.md) | [Curriculum Map](../../README.md)
