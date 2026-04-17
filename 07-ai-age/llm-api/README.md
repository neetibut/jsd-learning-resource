# LLM API Integration

> **Before this:** Complete [06-fullstack](../../06-fullstack/deployment/README.md) first.

---

## The Mental Model

An LLM API is an HTTP endpoint that accepts text (your prompt) and returns text (the model's response). Calling one from your Node.js server looks almost identical to calling any other REST API: you send a POST request with a JSON body, you get a JSON response. The practical skill here is not machine learning — it is reading API documentation, structuring prompts, and handling the response. The AI is a tool; you are the engineer deciding how to use it.

For the job board, the feature is AI-assisted job search: a user describes their skills and goals, your server sends that description to the LLM API with a structured prompt, and the response suggests which job listings match best. The `USE_MOCK=true` environment variable bypasses the real API call and returns a pre-written mock response — this means you can build and test the feature without an API key, and students without billing set up can still complete the exercise. Streaming and multi-provider support are stretch goals; a single synchronous API call is the complete version for this week.

Prompt engineering is the skill of constructing inputs that reliably produce useful outputs. Two principles matter most here: be specific (give the model the job listings as structured data, not just a vague description), and constrain the output format (ask for JSON so you can parse the response programmatically instead of trying to extract data from free-form prose).

---

## The Code

```js
// backend/routes/search.js — AI-assisted job search endpoint

const express = require('express');
const Job     = require('../models/Job');

const router = express.Router();

// Mock response for development without an API key
const MOCK_RESPONSE = [
  { jobId: null, reason: 'This is a mock response. Set USE_MOCK=false and add ANTHROPIC_API_KEY to use the real AI.' }
];

// Build a prompt that gives the model the job data in a structured way
function buildPrompt(userQuery, jobs) {
  const jobList = jobs
    .map(j => `- ID: ${j._id} | Title: ${j.title} | Company: ${j.company} | Location: ${j.location} | Type: ${j.type}`)
    .join('\n');

  return `You are a job matching assistant. A developer is looking for work.

Their background and goals:
"${userQuery}"

Available job listings:
${jobList}

Return a JSON array of the top 3 most relevant matches. Each item should have:
- jobId: the job's ID string
- reason: one sentence explaining why this job matches the candidate

Return ONLY the JSON array, no other text.`;
}

// POST /api/search
router.post('/', async (req, res) => {
  const { query } = req.body;
  if (!query) return res.status(400).json({ error: 'query is required' });

  // Mock path — no API key needed
  if (process.env.USE_MOCK === 'true') {
    return res.json({ results: MOCK_RESPONSE });
  }

  try {
    // Fetch current jobs to include in the prompt
    const jobs = await Job.find().limit(20).lean();

    // Call the Anthropic API
    const response = await fetch('https://api.anthropic.com/v1/messages', {
      method: 'POST',
      headers: {
        'x-api-key':          process.env.ANTHROPIC_API_KEY,
        'anthropic-version':  '2023-06-01',
        'content-type':       'application/json',
      },
      body: JSON.stringify({
        model:      'claude-haiku-4-5-20251001', // fast and cost-effective for this use case
        max_tokens: 1024,
        messages: [
          { role: 'user', content: buildPrompt(query, jobs) }
        ],
      }),
    });

    if (!response.ok) {
      throw new Error(`Anthropic API error: ${response.status}`);
    }

    const data    = await response.json();
    const text    = data.content[0].text;
    const results = JSON.parse(text); // the model returns JSON as instructed

    res.json({ results });

  } catch (error) {
    console.error('AI search failed:', error.message);
    res.status(500).json({ error: 'AI search is unavailable. Try again later.' });
  }
});

module.exports = router;
```

```js
// backend/server.js — mount the search router
const searchRoutes = require('./routes/search');
app.use('/api/search', searchRoutes);
```

```
# backend/.env — add these for Week 12
ANTHROPIC_API_KEY=sk-ant-...   # get from console.anthropic.com
USE_MOCK=true                  # set to false to use the real API
```

```jsx
// frontend/src/SearchPage.jsx — UI for the AI search feature

import { useState } from 'react';

const API_URL = import.meta.env.VITE_API_URL || 'http://localhost:3000';

export function SearchPage() {
  const [query,   setQuery]   = useState('');
  const [results, setResults] = useState([]);
  const [loading, setLoading] = useState(false);
  const [error,   setError]   = useState(null);

  async function handleSearch(e) {
    e.preventDefault();
    if (!query.trim()) return;

    setLoading(true);
    setError(null);

    try {
      const res  = await fetch(`${API_URL}/api/search`, {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ query }),
      });
      const data = await res.json();
      setResults(data.results);
    } catch {
      setError('Search failed. Please try again.');
    } finally {
      setLoading(false);
    }
  }

  return (
    <div className="search-page">
      <h2>AI Job Search</h2>
      <form onSubmit={handleSearch}>
        <textarea
          value={query}
          onChange={e => setQuery(e.target.value)}
          placeholder="Describe your skills, experience, and what you're looking for..."
          rows={4}
        />
        <button type="submit" disabled={loading}>
          {loading ? 'Searching...' : 'Find matching jobs'}
        </button>
      </form>

      {error && <p className="error">{error}</p>}

      <ul>
        {results.map((r, i) => (
          <li key={i}>
            <strong>Job {r.jobId}</strong> — {r.reason}
          </li>
        ))}
      </ul>
    </div>
  );
}
```

---

## Try It Yourself

Add `USE_MOCK=true` to your `backend/.env` and mount the search router. Test `POST /api/search` with a JSON body `{ "query": "I know React and am looking for remote work" }` — you should get the mock response. Then set `USE_MOCK=false`, add a real `ANTHROPIC_API_KEY` (free tier available at console.anthropic.com), and run the same request. Verify the model returns actual job matches from your database. Add the `<SearchPage />` component to your React frontend and link it from the header. Commit with `Week 12: AI-assisted job search`. (Week 12)

---

## Go Deeper

- [READ] Anthropic docs: Messages API — https://docs.anthropic.com/en/api/messages — read "Getting started" and "Structured outputs"; the structured output section is exactly the technique used in the prompt above
- [READ] Anthropic docs: Prompt engineering overview — https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/overview — read "Be clear and direct" and "Use structured formats"; these two principles explain why the prompt above works and how to improve it

---

## The AI Age

**When to use AI for this topic:** Ask AI to help you improve your prompt for more consistent output. Paste your current prompt and a sample response that did not meet your expectations, and ask AI to revise the prompt. Iterating on prompts is the core skill of this week.

**What AI gets wrong:** AI models sometimes return valid-looking text that is not quite valid JSON — trailing commas, single quotes instead of double quotes, or prose before the array. Always wrap `JSON.parse()` in a try/catch, and consider asking the model explicitly: "Return ONLY the JSON array, with no surrounding text or explanation."

**Try this prompt in Cursor:**
```
I'm building an AI job matching feature. My current prompt returns inconsistent
JSON — sometimes it includes extra text before or after the array.

Here is my current prompt:
[paste your prompt]

Here is an example of a bad response I received:
[paste the bad response]

Rewrite the prompt to make the model return ONLY a valid JSON array,
with no surrounding text. Explain what changes you made and why.
```

---

← [Deployment](../../06-fullstack/deployment/README.md) | [Curriculum Map](../../README.md) | [Next: AI Dev Workflow →](../ai-dev-workflow/README.md)
