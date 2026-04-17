# MongoDB

> **Before this:** Complete [03-backend](../../03-backend/express/README.md) first.

---

## The Mental Model

MongoDB stores data as documents — JSON-like objects — rather than rows in tables. If you have used a spreadsheet, a relational database (like PostgreSQL) feels familiar: rows and columns, rigid schema, everything typed. MongoDB is more like a folder of JSON files: each document can have different fields, nested objects, and arrays. This flexibility is why it pairs naturally with JavaScript, where your data is already JSON.

The tradeoff: a relational database enforces consistency at the database level (you cannot store a string where an integer is expected). MongoDB does not — that enforcement is up to your application. Mongoose (Week 8) adds that enforcement back, which is why most Node.js applications use MongoDB through Mongoose rather than the raw driver. For the job board, each job listing is a document: title, company, location, description, posted date. Collections are the equivalent of tables; the `jobs` collection holds all job documents.

MongoDB Atlas is the managed cloud version — no server to install or maintain. You create a free cluster, get a connection string, and connect from your Node.js app. The connection string is a secret and belongs in a `.env` file, never in your code. When you deploy to Render in Week 11, you will set this as an environment variable in the Render dashboard.

---

## The Code

```js
// db.js — connecting to MongoDB Atlas with the native driver
// (Week 8 replaces this with Mongoose; this is the raw version to understand what Mongoose wraps)

const { MongoClient } = require('mongodb');

// The URI comes from .env — never hardcode credentials in source code
const uri = process.env.MONGODB_URI;
const client = new MongoClient(uri);

// Connect once when the server starts; reuse the connection for all requests
let db;

async function connectDB() {
  await client.connect();
  db = client.db('jobboard'); // 'jobboard' is the database name
  console.log('Connected to MongoDB');
}

// --- CRUD operations on the jobs collection ---

async function getAllJobs() {
  return db.collection('jobs').find({}).toArray();
}

async function getJobById(id) {
  const { ObjectId } = require('mongodb');
  // MongoDB uses ObjectId for _id, not plain numbers
  return db.collection('jobs').findOne({ _id: new ObjectId(id) });
}

async function createJob(jobData) {
  const result = await db.collection('jobs').insertOne({
    ...jobData,
    postedAt: new Date(),
  });
  return result.insertedId;
}

async function deleteJob(id) {
  const { ObjectId } = require('mongodb');
  return db.collection('jobs').deleteOne({ _id: new ObjectId(id) });
}

module.exports = { connectDB, getAllJobs, getJobById, createJob, deleteJob };
```

```js
// server.js — updated to use MongoDB instead of in-memory array

const express = require('express');
const { connectDB, getAllJobs, getJobById, createJob } = require('./db');
require('dotenv').config(); // loads .env into process.env

const app = express();
app.use(express.json());

// Connect to the database before starting the server
connectDB().then(() => {
  app.listen(3000, () => console.log('Server running on port 3000'));
});

app.get('/api/jobs', async (req, res) => {
  const jobs = await getAllJobs();
  res.json(jobs);
});

app.get('/api/jobs/:id', async (req, res) => {
  const job = await getJobById(req.params.id);
  if (!job) return res.status(404).json({ error: 'Job not found' });
  res.json(job);
});

app.post('/api/jobs', async (req, res) => {
  const { title, company } = req.body;
  if (!title || !company) return res.status(400).json({ error: 'title and company required' });
  const id = await createJob(req.body);
  res.status(201).json({ _id: id, ...req.body });
});
```

---

## Try It Yourself

Create a free MongoDB Atlas cluster at [mongodb.com/atlas](https://www.mongodb.com/cloud/atlas). Create a database named `jobboard` and a collection named `jobs`. Insert 3 job documents directly in the Atlas UI. Then create a `.env` file in your `backend/` folder with `MONGODB_URI=<your connection string>`, run `npm install mongodb dotenv`, and update your server to use the `getAllJobs` function above. Verify `GET /api/jobs` now returns data from Atlas instead of the in-memory array. Commit with `Week 7: MongoDB stores job data`. (Week 7)

---

## Go Deeper

- [READ] MongoDB docs: Documents — https://www.mongodb.com/docs/manual/core/document/ — read "Document Structure" and "Dot Notation"; the nested document section explains how to query embedded fields in Week 8
- [READ] MongoDB docs: CRUD Operations — https://www.mongodb.com/docs/manual/crud/ — skim the overview; then read "Insert Documents" and "Query Documents" fully; "Update" and "Delete" you can reference as needed
- [VIDEO] Traversy Media: MongoDB Crash Course — search YouTube "Traversy Media MongoDB Crash Course" — watch up to the "Mongoose" section (stop there; Mongoose gets its own topic next week)

---

## The AI Age

**When to use AI for this topic:** Ask AI to help write MongoDB query filters. Describe in plain English what you want ("find all jobs where location is Bangkok and type is Full-time") and ask for the MongoDB query object. AI generates filter objects accurately.

**What AI gets wrong:** AI often uses string IDs in queries instead of `new ObjectId(id)`. MongoDB's `_id` field is not a string — it is an `ObjectId` type. A query like `findOne({ _id: id })` where `id` is a string will always return `null`. Always wrap raw ID strings in `new ObjectId()`.

**Try this prompt in Cursor:**
```
Write a MongoDB find query (using the Node.js mongodb driver) that returns
all jobs where type is "Full-time" AND location is either "Bangkok" or "Remote",
sorted by postedAt descending. Use async/await. Show the full function.
```

---

← [Express](../../03-backend/express/README.md) | [Curriculum Map](../../README.md) | [Next: Mongoose →](../mongoose/README.md)
