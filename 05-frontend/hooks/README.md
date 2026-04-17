# React Hooks

> **Before this:** Complete [React Basics](../react-basics/README.md) first.

---

## The Mental Model

React hooks are functions that let components remember things and respond to changes. `useState` gives a component memory: it returns a value and a function to update that value, and whenever you call the update function, React re-renders the component with the new value. `useEffect` lets a component do something in response to a render — like fetching data when the component first appears, or subscribing to an event and cleaning up when the component disappears.

The rule that confuses beginners: state updates do not take effect immediately. When you call `setJobs(newJobs)`, React schedules a re-render — it does not update `jobs` right now. If you try to read `jobs` on the next line, you still get the old value. This is intentional; React batches updates for performance. The mental model: state updates are asynchronous triggers for re-renders, not synchronous mutations. Once you internalize this, React's behavior becomes predictable.

The dependency array in `useEffect` is how you control when the effect runs. An empty array `[]` means 'run once after the first render' — the right choice for initial data fetching. An array with values like `[filter]` means 'run again whenever `filter` changes' — perfect for re-fetching when search criteria change. No array at all means 'run after every render' — almost never what you want, and a common source of infinite loops.

---

## The Code

```jsx
// frontend/src/App.jsx — Week 10: hooks replace hardcoded data

import { useState, useEffect } from 'react';

const API_URL = import.meta.env.VITE_API_URL || 'http://localhost:3000';

// --- Custom hook: encapsulates the fetch logic ---
// Moving data fetching into a custom hook keeps components clean.
function useJobs() {
  const [jobs, setJobs]       = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError]     = useState(null);

  useEffect(() => {
    // Defined inside useEffect to avoid stale closure issues
    async function loadJobs() {
      try {
        const response = await fetch(`${API_URL}/api/jobs`);
        if (!response.ok) throw new Error(`HTTP ${response.status}`);
        const data = await response.json();
        setJobs(data);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    }

    loadJobs();
  }, []); // [] = run once on mount

  return { jobs, loading, error };
}

// --- JobCard component (same as Week 9) ---
function JobCard({ job }) {
  return (
    <li className="job-card">
      <h2 className="job-title">{job.title}</h2>
      <p className="job-meta">{job.company} &middot; {job.location} &middot; {job.type}</p>
    </li>
  );
}

// --- Root App with search filter ---
function App() {
  const { jobs, loading, error } = useJobs();
  const [filter, setFilter] = useState('');

  // Derived state: filter is computed from jobs + filter string.
  // Do NOT store this in useState — it would go out of sync.
  const filteredJobs = jobs.filter(job =>
    job.title.toLowerCase().includes(filter.toLowerCase()) ||
    job.company.toLowerCase().includes(filter.toLowerCase())
  );

  return (
    <div className="app">
      <header>
        <h1>Dev Jobs</h1>
        <input
          type="search"
          placeholder="Search jobs..."
          value={filter}
          onChange={e => setFilter(e.target.value)}
        />
      </header>

      <main>
        {loading && <p>Loading jobs...</p>}
        {error   && <p className="error">Error: {error}</p>}
        {!loading && !error && (
          <ul className="jobs-list">
            {filteredJobs.map(job => (
              <JobCard key={job._id} job={job} />
            ))}
          </ul>
        )}
      </main>
    </div>
  );
}

export default App;
```

---

## Try It Yourself

Replace the hardcoded `jobs` array in your `App.jsx` with a `useJobs` custom hook that fetches from `http://localhost:3000/api/jobs` using `useEffect`. Add a loading state that shows "Loading jobs..." while the fetch is in progress, and an error state that shows the error message if the fetch fails. Then add a search input that filters the job list by title or company as the user types — use `useState` for the filter string and derive the filtered list rather than storing it in state. Commit with `Week 10: hooks fetch jobs from API`. (Week 10)

---

## Go Deeper

- [READ] React docs: Adding Interactivity — https://react.dev/learn/adding-interactivity — read "State: A Component's Memory" and "Queueing a Series of State Updates"; the queueing page explains exactly why state updates feel delayed
- [READ] React docs: Synchronizing with Effects — https://react.dev/learn/synchronizing-with-effects — read the full page; the dependency array section with the visual examples is the clearest explanation of `useEffect` available

---

## The AI Age

**When to use AI for this topic:** Ask AI to help you extract a custom hook. Paste a component with `useState` and `useEffect` logic mixed into the JSX, and ask it to move the data-fetching logic into a `useJobSearch` custom hook. AI handles this refactor well.

**What AI gets wrong:** AI often puts `async` directly on the `useEffect` callback — `useEffect(async () => {...}, [])` — which causes a warning because `useEffect` is not supposed to return a Promise. Always define the async function inside the effect and call it, as shown in the code above.

**Try this prompt in Cursor:**
```
I have a useEffect that fetches data. Refactor it to a custom hook called
useJobs that returns { jobs, loading, error }. Keep the async function
defined inside useEffect (not on the useEffect callback itself).
Here is the current component:
[paste your component]
```

---

← [React Basics](../react-basics/README.md) | [Curriculum Map](../../README.md) | [Next: React Router →](../routing/README.md)
