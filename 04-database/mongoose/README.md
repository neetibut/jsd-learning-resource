# Mongoose

> **Before this:** Complete [MongoDB](../mongodb/README.md) first.

---

## The Mental Model

Mongoose is an ODM — an Object Document Mapper. It sits between your Express server and MongoDB and does two things: it defines the shape of your data (schema), and it gives you a clean JavaScript API for interacting with the database (models). Without Mongoose, you are sending raw JSON to MongoDB and hoping it has the right fields. With Mongoose, you define once that a job has a required title (string), a required company (string), and an optional salary (number) — and Mongoose enforces that every time.

A Mongoose model is a class. You define the schema, create a model from it, and then use the model to create, read, update, and delete documents. `Job.find()` returns all jobs. `Job.findById(id)` returns one. `new Job({...}).save()` creates a new one. This API is consistent across all your models, which makes the pattern easy to learn once and apply everywhere.

Mongoose also handles the ObjectId conversion that tripped you up with the raw driver — `Job.findById('some-string-id')` automatically casts the string to an ObjectId. It also adds `createdAt` and `updatedAt` timestamps automatically when you set `{ timestamps: true }` in the schema options. These small conveniences add up to a much cleaner codebase than the raw driver, which is why virtually every Node.js application uses Mongoose rather than talking to MongoDB directly.

---

## The Code

```js
// models/Job.js — the Mongoose schema and model for job listings

const mongoose = require('mongoose');

const jobSchema = new mongoose.Schema(
  {
    title: {
      type: String,
      required: [true, 'Job title is required'],
      trim: true,
      maxlength: [100, 'Title cannot exceed 100 characters'],
    },
    company: {
      type: String,
      required: [true, 'Company name is required'],
      trim: true,
    },
    location: {
      type: String,
      default: 'Remote',
    },
    type: {
      type: String,
      enum: ['Full-time', 'Part-time', 'Contract', 'Internship'],
      default: 'Full-time',
    },
    description: {
      type: String,
      maxlength: [2000, 'Description cannot exceed 2000 characters'],
    },
    // In Week 11, this will reference a User document
    postedBy: {
      type: mongoose.Schema.Types.ObjectId,
      ref: 'User',
    },
  },
  {
    timestamps: true, // automatically adds createdAt and updatedAt fields
  }
);

// Export the model — 'Job' becomes the collection name 'jobs' in MongoDB
module.exports = mongoose.model('Job', jobSchema);
```

```js
// server.js — updated to use the Mongoose Job model

const express = require('express');
const mongoose = require('mongoose');
const Job = require('./models/Job');
require('dotenv').config();

const app = express();
app.use(express.json());

// Connect via Mongoose (replaces the MongoClient setup from Week 7)
mongoose
  .connect(process.env.MONGODB_URI)
  .then(() => {
    console.log('Connected to MongoDB via Mongoose');
    app.listen(3000, () => console.log('Server running on port 3000'));
  });

// GET /api/jobs — all jobs, newest first
app.get('/api/jobs', async (req, res) => {
  const jobs = await Job.find().sort({ createdAt: -1 });
  res.json(jobs);
});

// GET /api/jobs/:id — single job
app.get('/api/jobs/:id', async (req, res) => {
  const job = await Job.findById(req.params.id);
  if (!job) return res.status(404).json({ error: 'Job not found' });
  res.json(job);
});

// POST /api/jobs — create a job (Mongoose validates against the schema)
app.post('/api/jobs', async (req, res) => {
  try {
    const job = await Job.create(req.body);
    res.status(201).json(job);
  } catch (error) {
    // Mongoose validation errors have error.name === 'ValidationError'
    if (error.name === 'ValidationError') {
      return res.status(400).json({ error: error.message });
    }
    res.status(500).json({ error: 'Server error' });
  }
});
```

---

## Try It Yourself

Create a `models/` folder inside `backend/` and add `Job.js` with the schema above. Run `npm install mongoose` (you can remove the `mongodb` package). Update your server to use `Job.find()`, `Job.findById()`, and `Job.create()` instead of the raw driver functions from Week 7. Test that: posting a job without a title returns a 400 with a clear error message, posting with an invalid `type` value (e.g. `"Freelance"`) returns a 400, and valid jobs are saved and returned with `createdAt`/`updatedAt` fields. Commit with `Week 8: Mongoose schema and validation`. (Week 8)

---

## Go Deeper

- [READ] Mongoose docs: Schemas — https://mongoosejs.com/docs/guide.html — read "Defining your schema", "SchemaTypes", and "Options"; the `timestamps` option and `enum` validation are used directly in this topic
- [READ] Mongoose docs: Models — https://mongoosejs.com/docs/models.html — short page; read fully; pay attention to `Model.create()` vs `new Model().save()` — both work, but `create()` is cleaner for simple cases

---

## The AI Age

**When to use AI for this topic:** Ask AI to generate a Mongoose schema from a description of your data. Paste the field names, types, and constraints in plain English. AI produces correct Mongoose schemas reliably, including validators and default values.

**What AI gets wrong:** AI often adds `.lean()` to Mongoose queries without explaining it — `lean()` returns plain JS objects instead of Mongoose documents, which disables methods like `.save()` on the result. Only add `.lean()` when you know you need it (read-only queries where you want better performance).

**Try this prompt in Cursor:**
```
Write a Mongoose schema for a User model with the following fields:
- email: required, unique, lowercase, must be a valid email format
- password: required, minimum 8 characters (this will be stored as a bcrypt hash)
- createdAt/updatedAt: automatic timestamps
- role: either "user" or "admin", default "user"

Export the model as 'User'. Use CommonJS (module.exports).
```

---

← [MongoDB](../mongodb/README.md) | [Curriculum Map](../../README.md) | [Next: React Basics →](../../05-frontend/react-basics/README.md)
