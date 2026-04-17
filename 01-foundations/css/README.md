# CSS

> **Before this:** Complete [HTML](../html/README.md) first.

---

## The Mental Model

CSS is a description language for appearance — but what makes it unusual is the cascade. Rules from multiple sources (browser defaults, your stylesheet, inline styles) are layered on top of each other, and the browser resolves conflicts using a priority system called specificity. This is the part that confuses beginners: you write a rule, nothing changes, and you don't know why. Nine times out of ten, another rule with higher specificity is winning the conflict. Once you understand that CSS is always a competition between rules, the debugging model clicks into place.

The second thing to understand is that the normal document flow — the way elements stack by default — is actually doing a lot of work for you. Most beginners reach for `position: absolute` or `float` to move things around, which takes elements out of normal flow and makes layouts fragile. Flexbox and CSS Grid let you control layout while keeping elements in flow. Start with those. They are how modern layouts are built.

Responsive design means your layout adapts to the screen it is on. The job board should be readable on a phone. The tool for this is media queries: `@media (max-width: 600px) { ... }` lets you override rules for small screens. The mobile-first approach — write styles for small screens first, then override for larger ones — leads to cleaner code because it forces you to think about the most constrained layout first.

---

## The Code

```css
/* styles.css — Week 2 state of the job board */

/* 1. Reset browser defaults so we start from a consistent base */
*, *::before, *::after {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  font-family: system-ui, -apple-system, sans-serif;
  background-color: #f5f5f5;
  color: #333;
  line-height: 1.6;
}

/* 2. Page layout */
header {
  background-color: #1a1a2e;
  color: white;
  padding: 2rem 1rem;
  text-align: center;
}

header h1 {
  font-size: 2rem;
  margin-bottom: 0.5rem;
}

main {
  max-width: 800px;
  margin: 2rem auto;
  padding: 0 1rem;
}

footer {
  text-align: center;
  padding: 1rem;
  color: #888;
  font-size: 0.875rem;
}

/* 3. Job list — remove default list styling */
#jobs-list {
  list-style: none;
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

/* 4. Job card */
.job-card {
  background: white;
  border: 1px solid #e0e0e0;
  border-radius: 8px;
  padding: 1.5rem;
  /* Flexbox used here to keep internal layout manageable */
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.job-title {
  font-size: 1.125rem;
  color: #1a1a2e;
}

.job-meta {
  font-size: 0.875rem;
  color: #666;
}

.job-description {
  font-size: 0.9375rem;
}

/* 5. Responsive: on small screens, reduce padding */
@media (max-width: 600px) {
  header {
    padding: 1.25rem 1rem;
  }

  .job-card {
    padding: 1rem;
  }
}
```

---

## Try It Yourself

Create `styles.css` alongside your `index.html` and link it with `<link rel="stylesheet" href="styles.css">` in the `<head>`. Style the job board so that: the header has a dark background with white text, each job listing is a white card with a visible border, and the layout is readable on a phone screen (test by narrowing your browser window). Commit with `Week 2: CSS styling and responsive layout`. (Week 2)

---

## Go Deeper

- [READ] MDN: CSS basics — https://developer.mozilla.org/en-US/docs/Learn/Getting_started_with_the_web/CSS_basics — read through "Fonts and text" and "The box model"; those two sections explain 80% of layout confusion
- [INTERACTIVE] Flexbox Froggy — https://flexboxfroggy.com — complete all 24 levels; 20 minutes and you will understand flexbox layout intuitively
- [READ] MDN: Using media queries — https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_media_queries/Using_media_queries — focus on the "min-width/max-width" section; skip the print and accessibility sections for now

---

## The AI Age

**When to use AI for this topic:** Ask AI to explain why a specific CSS rule is not working. Paste the HTML and CSS and describe what you expected vs. what happened. AI is good at specificity debugging and catches common mistakes like applying flexbox to the wrong element.

**What AI gets wrong:** AI often adds excessive `!important` to resolve specificity conflicts instead of fixing the underlying selector. If AI suggests `!important`, ask it to solve the problem without it — that forces the correct fix.

**Try this prompt in Cursor:**
```
This CSS rule is not applying and I don't know why. Here is my HTML:
[paste HTML]

Here is my CSS:
[paste CSS]

I expected [describe expected behavior]. What is overriding my rule,
and how do I fix it without using !important?
```

---

← [HTML](../html/README.md) | [Curriculum Map](../../README.md) | [Next: Version Control →](../version-control/README.md)
