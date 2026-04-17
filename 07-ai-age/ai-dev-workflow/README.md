# AI Dev Workflow

> **Before this:** Complete [LLM API](../llm-api/README.md) first.

---

## The Mental Model

Using AI in a development workflow is a skill with a learning curve, like any other tool. The failure mode is treating AI output as correct by default — copying generated code without reading it, then debugging something you do not understand when it breaks. The right mental model is AI as a fast, knowledgeable pair programmer who makes confident mistakes. It writes the scaffold; you review and own the result.

Cursor and Copilot work best for specific, constrained tasks: 'write a Mongoose schema for a job listing with these fields', 'write an Express route that validates req.body has a title and company', 'write a test for this function'. They work worst on open-ended tasks ('build me a job board') because the output is hard to verify and you learn nothing. The habit to build: use AI to generate, then read every line before accepting it. Over time you develop a sense for what AI gets right consistently (boilerplate, common patterns) and what it gets wrong often (error handling, security, edge cases).

The most durable skill from this course is not any specific technology — it is knowing enough about each layer (HTML, CSS, JavaScript, Node, Express, MongoDB, React) to evaluate AI output critically. A developer who understands the fundamentals and uses AI as a force multiplier is more productive than one who relies on AI and cannot tell when it is wrong. This week is about making that relationship intentional.

---

## The Code

```
// This topic is about workflow, not a single code file.
// The "code" is a set of prompt patterns that work across the whole stack.
```

```
# Pattern 1: Scaffolding a new file
# Use for: new routes, components, models, middleware

Write a [type] called [name] that:
1. [specific requirement]
2. [specific requirement]
3. [specific requirement]
Use [technology/version]. [any constraints: CommonJS, no external libraries, etc.]
```

```
# Pattern 2: Debugging an error
# Use for: any error you cannot diagnose in 5 minutes

I got this error:
[paste full error message and stack trace]

Here is the relevant code:
[paste the file or function]

Here is what I expected to happen:
[describe expected behavior]

What is causing this and how do I fix it?
```

```
# Pattern 3: Code review
# Use after you write something you are unsure about

Review this [function/route/component] for:
1. Security issues
2. Error handling gaps
3. Any edge cases I haven't handled

[paste code]

Be specific about each issue. Do not rewrite the code — just identify problems.
```

```
# Pattern 4: Explaining unfamiliar code
# Use when you encounter code you didn't write

Explain this code line by line. Assume I know JavaScript basics but am
not familiar with [library/pattern]:

[paste code]

After explaining, tell me: what would break if I removed [specific line]?
```

```
# Pattern 5: Test generation
# Use to write tests for code you have already written

Write unit tests for this function using [Jest/Vitest].
Cover: the happy path, the error path, and [any edge case you know about].
Do not mock [database/external service] unless there is no other way.

[paste function]
```

---

## Try It Yourself

Pick one of the patterns above and apply it to a real piece of your job board code. Specifically: use Pattern 3 (code review) on your `requireAuth` middleware from Week 11. Paste the middleware into Cursor, ask for a security review, and evaluate the response. Did AI find anything real? Did it suggest anything wrong or unnecessary? Write a brief note (3-5 sentences) in a comment at the top of your middleware file documenting what the review found and what you decided to keep or change. This reflection is the exercise — the habit of reviewing AI output critically is the skill. (Week 12)

---

## Go Deeper

- [READ] Anthropic: Claude's constitution and model card — https://www.anthropic.com/claude — skim the "Research" section; understanding how models are trained and what failure modes exist makes you a more skeptical consumer of AI output
- [VIDEO] Andrej Karpathy: Intro to Large Language Models — search YouTube "Karpathy intro to large language models" — watch the first 20 minutes; the "token prediction" mental model explains why AI confidently hallucinates and why you must always verify

---

## The AI Age

**When to use AI for this topic:** This topic is about metacognition — when to use AI and when not to. Ask AI to generate a checklist of things to verify before accepting AI-generated code for the specific type of code you are working on (Express routes, React components, database models). The checklist becomes your personal review ritual.

**What AI gets wrong:** AI cannot tell you that it is wrong. It will generate plausible-looking but subtly broken code and present it with the same confidence as correct code. The antidote is not avoiding AI — it is building enough understanding of each topic that you can spot the breakage. This is why the curriculum spends 11 weeks on fundamentals before Week 12.

**Try this prompt in Cursor:**
```
I am a bootcamp graduate who has just built a MERN job board with JWT auth.
Generate a personal code review checklist for Express routes that I can use
every time I write or accept AI-generated route code.

Focus on the mistakes that are easy to miss when you are learning:
security, error handling, and incorrect HTTP status codes.
Give me 8-10 specific, actionable checklist items.
```

---

← [LLM API](../llm-api/README.md) | [Curriculum Map](../../README.md) | [Next: Curriculum Map →](../../README.md)
