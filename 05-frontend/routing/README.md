# React Router

> **Before this:** Complete [React Hooks](../hooks/README.md) first.

---

## The Mental Model

React Router is what makes a single-page application feel like a multi-page website. Without it, your React app lives at one URL and has no way to navigate between views without a full page reload. React Router maps URL patterns to components: when the URL is `/jobs`, render the `<JobList>` component; when it is `/jobs/42`, render the `<JobDetail>` component for job 42. No server round-trip; the browser stays on the same page, and React swaps out which components are displayed.

The key concept is that the URL is part of your application state. React Router reads the URL, finds the matching route, and renders the right component. When the user clicks a `<Link>` component, React Router updates the URL and re-renders without a page reload. This is what 'client-side routing' means. The browser's back and forward buttons still work; bookmarks and shared links still work. It feels like navigation, but it is just React re-rendering based on the URL.

Route parameters let URLs carry data. `/jobs/:id` is a pattern where `:id` matches any string. Inside the `<JobDetail>` component, `useParams()` gives you the value of `id` from the URL — `{ id: '64a1f2c3...' }`. That ID is what you pass to your API to fetch the right job. This is the same idea as Express route parameters (`req.params.id` in Week 6), just on the frontend.

---

## The Code

```jsx
// frontend/src/App.jsx — Week 10: React Router adds navigation

import { BrowserRouter, Routes, Route, Link, useParams, useNavigate } from 'react-router-dom';
import { useState, useEffect } from 'react';

const API_URL = import.meta.env.VITE_API_URL || 'http://localhost:3000';

// --- Job list page ---
function JobListPage() {
  const [jobs, setJobs] = useState([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    async function load() {
      const res = await fetch(`${API_URL}/api/jobs`);
      setJobs(await res.json());
      setLoading(false);
    }
    load();
  }, []);

  if (loading) return <p>Loading...</p>;

  return (
    <ul className="jobs-list">
      {jobs.map(job => (
        <li key={job._id} className="job-card">
          <h2>
            {/* Link navigates to the detail page without a full reload */}
            <Link to={`/jobs/${job._id}`}>{job.title}</Link>
          </h2>
          <p>{job.company} &middot; {job.location}</p>
        </li>
      ))}
    </ul>
  );
}

// --- Job detail page ---
function JobDetailPage() {
  const { id } = useParams(); // reads :id from the URL
  const navigate = useNavigate();
  const [job, setJob] = useState(null);

  useEffect(() => {
    async function load() {
      const res = await fetch(`${API_URL}/api/jobs/${id}`);
      if (!res.ok) {
        navigate('/'); // redirect to list if job not found
        return;
      }
      setJob(await res.json());
    }
    load();
  }, [id]); // re-run if the id in the URL changes

  if (!job) return <p>Loading...</p>;

  return (
    <div className="job-detail">
      <button onClick={() => navigate(-1)}>← Back</button>
      <h1>{job.title}</h1>
      <p>{job.company} &middot; {job.location} &middot; {job.type}</p>
      <p>{job.description ?? 'No description.'}</p>
    </div>
  );
}

// --- Layout with shared header ---
function Layout() {
  return (
    <div className="app">
      <header>
        <Link to="/" style={{ textDecoration: 'none', color: 'inherit' }}>
          <h1>Dev Jobs</h1>
        </Link>
      </header>
      <main>
        {/* Outlet renders whichever route is active */}
        <Routes>
          <Route path="/"         element={<JobListPage />} />
          <Route path="/jobs/:id" element={<JobDetailPage />} />
        </Routes>
      </main>
    </div>
  );
}

// --- Root App ---
function App() {
  return (
    <BrowserRouter>
      <Layout />
    </BrowserRouter>
  );
}

export default App;
```

---

## Try It Yourself

Install React Router: `npm install react-router-dom`. Wrap your app in `<BrowserRouter>`, add a `<Routes>` block with two routes (`/` for the list, `/jobs/:id` for the detail), and create a `JobDetailPage` component that reads the `id` from `useParams()` and fetches that job from your Express API. Add `<Link>` elements to each job card so clicking navigates to the detail page. Test the browser back button — it should work without a page reload. Commit with `Week 10: React Router with job detail page`. (Week 10)

---

## Go Deeper

- [READ] React Router docs: Tutorial — https://reactrouter.com/en/main/start/tutorial — follow the tutorial up to and including "URL Params"; stop before "Index Routes" (you can return to it later)
- [READ] React Router docs: useParams — https://reactrouter.com/en/main/hooks/use-params — short reference page; read alongside the code above to see exactly how `:id` becomes `{ id }` in the component

---

## The AI Age

**When to use AI for this topic:** Ask AI to help set up a protected route (one that redirects to `/login` if the user is not authenticated). Describe your auth state shape and ask for the pattern. This is the Week 11 setup, but the routing piece is straightforward and AI generates it correctly.

**What AI gets wrong:** AI often generates code for React Router v5 (using `<Switch>` and `component={}` props) when you are using v6 (which uses `<Routes>` and `element={}`). These APIs are incompatible. Always specify "React Router v6" in your prompt, and verify the package version in your `package.json`.

**Try this prompt in Cursor:**
```
Using React Router v6, create a ProtectedRoute component that:
- Accepts an element prop (the component to render if authenticated)
- Reads authentication state from a boolean prop called isAuthenticated
- Redirects to /login using <Navigate> if not authenticated
- Otherwise renders the element prop
```

---

← [React Hooks](../hooks/README.md) | [Curriculum Map](../../README.md) | [Next: Connecting MERN →](../../06-fullstack/connecting-mern/README.md)
