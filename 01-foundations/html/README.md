# HTML

> **Before this:** Complete [00-setup](../../00-setup/README.md) first.

---

## The Mental Model

HTML is not a programming language — it has no logic, no conditionals, no loops. It is a description language. You are describing the structure and meaning of content, not telling the computer what to do. A paragraph tag does not make text look like a paragraph; it tells the browser (and screen readers, and search engines, and AI scrapers) that this text IS a paragraph. That distinction matters more than you think.

The single most useful mental shift in learning HTML is to think about semantics before appearance. Every element has a meaning: `<nav>` means navigation, `<article>` means self-contained content, `<button>` means something will happen when clicked. Using `<div>` for everything works visually, but it throws away information. Browsers, assistants, and accessibility tools all read that information. When you use the right element, you get a lot of correct behavior for free.

Forms deserve special mention because they are where users give you data. A `<form>` with proper `<label>` elements and a `<button type="submit">` gives you keyboard navigation, accessibility, and the browser's built-in validation — for free. The job board's post-a-job form in Week 11 starts here, with clean, semantic HTML.

---

## The Code

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Dev Jobs</title>
  <link rel="stylesheet" href="styles.css">
</head>
<body>

  <header>
    <h1>Dev Jobs</h1>
    <p>Find your next developer role in Thailand</p>
  </header>

  <main>
    <!-- In Week 3, JavaScript will replace this hardcoded list -->
    <ul id="jobs-list">

      <li class="job-card">
        <h2 class="job-title">Frontend Developer</h2>
        <p class="job-meta">Acme Corp &middot; Bangkok &middot; Full-time</p>
        <p class="job-description">Build React interfaces for our e-commerce platform.</p>
      </li>

      <li class="job-card">
        <h2 class="job-title">Backend Engineer</h2>
        <p class="job-meta">StartupTH &middot; Remote &middot; Full-time</p>
        <p class="job-description">Node.js APIs and PostgreSQL. 2+ years experience.</p>
      </li>

      <li class="job-card">
        <h2 class="job-title">Junior Web Developer</h2>
        <p class="job-meta">Digital Agency BKK &middot; Bangkok &middot; Part-time</p>
        <p class="job-description">HTML, CSS, and JavaScript. Great for new grads.</p>
      </li>

      <li class="job-card">
        <h2 class="job-title">Full Stack Developer</h2>
        <p class="job-meta">FinTech Co &middot; Chiang Mai &middot; Full-time</p>
        <p class="job-description">MERN stack. Help us build the next generation of payment tools.</p>
      </li>

      <li class="job-card">
        <h2 class="job-title">DevOps Engineer</h2>
        <p class="job-meta">CloudBase TH &middot; Remote &middot; Contract</p>
        <p class="job-description">AWS, Docker, and CI/CD pipelines. Strong Linux skills required.</p>
      </li>

    </ul>
  </main>

  <footer>
    <p>Job Board &copy; 2025</p>
  </footer>

  <!-- In Week 3, we add: <script src="app.js"></script> -->

</body>
</html>
```

---

## Try It Yourself

Create `index.html` in your `job-board-spine` repo with the 5 hardcoded job listings above. Open it directly in a browser (`File → Open` or drag the file in). Verify all 5 jobs appear and the page has a title in the browser tab. This is the Week 1 state of the spine project — commit it with the message `Week 1: static HTML job board`. (Week 1)

---

## Go Deeper

- [READ] MDN: HTML basics — https://developer.mozilla.org/en-US/docs/Learn/Getting_started_with_the_web/HTML_basics — read all of it; it is short and dense with useful mental models
- [READ] MDN: HTML elements reference — https://developer.mozilla.org/en-US/docs/Web/HTML/Element — bookmark this; use it whenever you are unsure which element to reach for
- [VIDEO] Kevin Powell: HTML for beginners — search YouTube "Kevin Powell HTML semantic elements" — watch for the section on why semantic elements matter; skip any CSS sections (that's next week)

---

## The AI Age

**When to use AI for this topic:** Ask AI to check your HTML for accessibility issues. Paste your markup and ask: "Are there any semantic or accessibility problems with this HTML?" AI catches missing `alt` attributes, unlabelled form inputs, and heading hierarchy errors reliably.

**What AI gets wrong:** AI almost always uses `<div>` when a semantic element is more appropriate. If you ask AI to "write HTML for a job listing," it will wrap everything in divs. Ask it to use semantic elements explicitly, or review its output against the MDN elements reference.

**Try this prompt in Cursor:**
```
Review this HTML for semantic correctness and accessibility. Replace any <div>
elements that should be a more specific HTML element. Explain each change:

[paste your HTML here]
```

---

← [00-setup](../../00-setup/README.md) | [Curriculum Map](../../README.md) | [Next: CSS →](../css/README.md)
