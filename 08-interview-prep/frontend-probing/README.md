# Frontend Probing

> **Before this:** Complete [05-frontend](../../05-frontend/routing/README.md) and review your React code in the spine project before working through this topic.

---

## The Mental Model

When a frontend interviewer probes you, they are not testing your ability to recite the React docs. They are listening for whether you understand *why* the framework makes the choices it does. The difference between a candidate who "knows React" and one who impresses an interviewer is that the second person can explain what problem each feature solves.

Take `useState`. A weak answer describes what it does: "it stores component state and re-renders the component when it changes." A strong answer explains the constraint that made it necessary: React's rendering model is a pure function of state — the UI is a snapshot, not a live mutation. `useState` is the escape hatch that lets the component say "this value matters enough to trigger a fresh snapshot." That one sentence of reasoning tells the interviewer you understand the model, not just the API.

The same pattern applies to every frontend topic. Interviewers probe `useEffect` not because they want you to list the dependency array rules, but because those rules reveal whether you understand the separation between rendering (synchronous, pure) and side effects (asynchronous, impure). They ask about browser rendering — paint, layout, compositing — not to test trivia but to see whether you intuitively know *why* CSS transforms are cheaper than changing `top/left`. Train yourself to answer every frontend question with "the reason this works this way is…" and you will stand out.

---

## The Code

The questions below are common probing questions in frontend interviews. Read each one, then read the model answer. Notice that every answer leads with the *why* before the *what*.

```js
// PROBING QUESTION: "Why does React re-render a component?"
//
// Model answer pattern:
// React re-renders when state or props change. But the important part
// is what "change" means: React uses Object.is() comparison. For primitives
// (strings, numbers, booleans), that's a value check. For objects and arrays,
// it's a reference check — so a new array with the same items will trigger
// a re-render, but mutating an existing array won't. This is why you always
// return new objects/arrays from state updates.

// In the spine project's job list, this matters here:
const [jobs, setJobs] = useState([]);

// WRONG — mutates existing array, React sees the same reference, no re-render:
// jobs.push(newJob);
// setJobs(jobs);

// RIGHT — new array reference, React detects change, re-renders:
setJobs([...jobs, newJob]);
```

```js
// PROBING QUESTION: "What is the purpose of the useEffect dependency array?"
//
// Model answer pattern:
// useEffect runs a side effect after render. The dependency array tells React
// *when* to re-run it — specifically, React re-runs the effect whenever any
// dependency value changes between renders. An empty array means "run once
// after the first render." No array means "run after every render."
// The underlying reason: React needs to know which values the effect reads
// so it can synchronize the effect with state. If you lie about dependencies,
// the effect runs with stale values.

useEffect(() => {
  // This runs once on mount — fetches initial job list from the spine API.
  // dependency array is empty because we only want this on first render.
  fetch('/api/jobs')
    .then(res => res.json())
    .then(data => setJobs(data));
}, []); // empty = mount only
```

---

## Try It Yourself

Open the spine project's React frontend. Pick any one `useEffect` hook you wrote. Set a timer for 5 minutes and explain it out loud, covering:

1. What the effect does
2. Why it's a side effect (not pure rendering logic)
3. What each dependency in the array does and why it's there
4. What would break if the dependency array were empty, or missing

Do this without looking at notes. If you get stuck, that's the gap to study — not a failure.

---

## Go Deeper

- [VIDEO] What is React? — https://react.dev/learn/thinking-in-react — Read this, not watch. The "Thinking in React" walkthrough is the mental model interviewers expect junior devs to have internalized.
- [READ] How browsers work: behind the scenes of modern web browsers — https://web.dev/articles/howbrowserswork — Read the "Rendering engine" section only; the rest is useful but lower priority for interviews at this level.
- [READ] A Complete Guide to useEffect — https://overreacted.io/a-complete-guide-to-useeffect/ — Long. Read the first half; the "Each Render Has Its Own…" section is the part that comes up in interviews.

---

## The AI Age

**When to use AI for this topic:** Generate a list of 10 probing questions for a specific React topic (e.g., "give me 10 interview questions about useEffect that a senior engineer would ask a junior candidate"). Use AI as a sparring partner — ask it to evaluate your answers.

**What AI gets wrong:** AI-generated "model answers" are often accurate but generic. They describe what a feature does without connecting it to the underlying model. If you copy AI answers verbatim, an interviewer who probes one level deeper will expose that you don't understand the reasoning behind the answer.

**Try this prompt in Cursor:**
```
I'm preparing for a frontend interview. I'm going to describe how useEffect works
in my own words. Tell me where my explanation is weak or incomplete, and what a
senior React developer would add. Here's my explanation: [your explanation here]
```

---

← [Interview Prep](../README.md) | [Curriculum Map](../../README.md) | [Next: Backend Probing →](../backend-probing/README.md)
