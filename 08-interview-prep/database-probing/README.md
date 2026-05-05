# Database Probing

> **Before this:** Complete [backend-probing](../backend-probing/README.md) or finish [04-database/mongoose](../../04-database/mongoose/README.md) before starting this topic.

---

## The Mental Model

Database questions in interviews are almost never about syntax. They are about whether you understand the tradeoffs behind the decisions you made when you designed your schema. The question "why did you store users and jobs as separate collections?" sounds simple, but it's probing your understanding of how MongoDB handles relationships, how queries work under the hood, and whether you thought about data integrity at all.

MongoDB stores data as documents — self-contained JSON objects that can hold nested data. This is fundamentally different from relational databases, where every relationship is a foreign key join. In MongoDB, you have two choices when one thing relates to another: embed the related data inside the document, or reference it by ID and look it up separately. Embedding is faster to read (one query gets everything) but bloats documents and makes updates harder. Referencing keeps documents lean and consistent but requires a second query or a `populate()` call. Every Mongoose schema you wrote implicitly made this choice. Interviewers want to know if you made it deliberately.

Indexing is the other common probe. Queries without indexes scan every document in a collection — fine for 100 documents, catastrophic for 1 million. MongoDB automatically indexes `_id`, but every other field you query frequently should have an index too. The spine project's job list is filtered by location and company — without indexes on those fields, every filter is a full collection scan. This is the kind of detail that signals you think about production behavior, not just local development.

---

## The Code

```js
// The spine project's User and Job schemas — read these with the tradeoffs in mind.

// User schema: sparse — just what we need for auth and profile.
// savedJobs stores Job _id references, not embedded documents.
// WHY: Jobs change (salary updates, status changes). If we embedded job data
// in every user's savedJobs array, we'd have to update hundreds of user documents
// every time a job changed. References mean one Job document is the source of truth.
const userSchema = new mongoose.Schema({
  email:     { type: String, required: true, unique: true },
  password:  { type: String, required: true }, // bcrypt hash, never plaintext
  savedJobs: [{ type: mongoose.Schema.Types.ObjectId, ref: 'Job' }],
}, { timestamps: true });

// Job schema: the core entity in the job board.
// Location and company are plain strings for simplicity — in a real system,
// these might be references to separate collections for faceted search.
const jobSchema = new mongoose.Schema({
  title:    { type: String, required: true },
  company:  { type: String, required: true },
  location: { type: String, required: true },
  salary:   { type: Number },
  postedBy: { type: mongoose.Schema.Types.ObjectId, ref: 'User' },
}, { timestamps: true });

// Index the fields we filter and sort by most often.
// Without this, GET /api/jobs?location=Bangkok scans every document.
jobSchema.index({ location: 1 });
jobSchema.index({ company: 1 });
jobSchema.index({ createdAt: -1 }); // newest first sorting
```

```js
// PROBING QUESTION: "How do you get a user's saved jobs in one query?"
//
// .populate() follows the ObjectId references and replaces them with
// full documents — similar to a JOIN in SQL, but done in application code.
const user = await User.findById(userId).populate('savedJobs');
// user.savedJobs is now an array of full Job objects, not just IDs.
// Cost: two queries under the hood (find user, then find all referenced jobs).
// Tradeoff: simpler code, but not free — avoid .populate() on large arrays.
```

---

## Try It Yourself

Open the spine project's Mongoose schemas. Pick one decision about how you structured a relationship (embedding vs. referencing) and defend it out loud as if an interviewer asked "why did you design it this way?" Cover:

1. What the two options were (embed or reference)
2. What you chose and why
3. What the downside of your choice is
4. When you would choose the other option instead

Interviewers specifically look for candidates who can articulate the *downside* of their own choices — it signals honest engineering judgment, not just memorized answers.

---

## Go Deeper

- [READ] Data Modeling Introduction — MongoDB Docs — https://www.mongodb.com/docs/manual/core/data-modeling-introduction/ — Read the "Embedded Data Models" and "Normalized Data Models" sections. These are the two options you need to be able to explain.
- [READ] Indexes in MongoDB — https://www.mongodb.com/docs/manual/indexes/ — Read the intro and the "Single Field Indexes" section. Know what `explain()` is and why it matters.
- [VIDEO] SQL vs NoSQL Explained — https://www.youtube.com/watch?v=ZS_kXvOeQ5Y — Watch the full video (15 min). Interviewers sometimes ask "why MongoDB over PostgreSQL?" — this gives you the honest tradeoff answer.

---

## The AI Age

**When to use AI for this topic:** Ask AI to critique your schema design: paste your Mongoose models and ask "what are the weaknesses in this schema for a job board application used by 10,000 users?" Use the critique to prepare answers for the follow-up questions it surfaces.

**What AI gets wrong:** AI schema suggestions often add unnecessary complexity — multiple collection references, virtuals, and discriminators that aren't needed at this scale. In an interview, proposing an over-engineered schema for a simple use case signals poor judgment. Keep it simple and be able to defend the simplicity.

**Try this prompt in Cursor:**
```
Here are my Mongoose schemas for a job board application:
[paste your schemas]

Pretend you are a senior backend engineer interviewing me.
Ask me one question about my schema design choices, then evaluate my answer
and ask a follow-up.
```

---

← [Backend Probing](../backend-probing/README.md) | [Curriculum Map](../../README.md) | [Next: System Design Probing →](../system-design-probing/README.md)
