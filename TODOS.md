# TODOS

- [ ] **job-board-spine repo** — Create new GitHub repo with backend/ + frontend/ scaffold.
  Create spine-week-00 tag. Enables spine/INTEGRATION-CHECKLIST.md and all spine-week-XX tags.
  Blocks: spine git tags. Priority: P1. Effort: S (human ~2hr / CC ~15min).

- [ ] **spine/INTEGRATION-CHECKLIST.md** — create after job-board-spine repo is set up.
  For each week (1-12): commands to run (npm install, npm start), what to verify
  (job list renders at localhost:5173). Ensures each spine-week-XX tag is independently
  runnable. Blocked by: job-board-spine repo creation.

- [ ] **Design doc: clarify spine tag location** — Add one sentence to the design doc:
  "spine-week-XX tags are created on the job-board-spine repo, not the curriculum repo."
  Prevents confusion at tagging time. Priority: P2. Effort: XS.

- [ ] **Prerequisite warnings in topic READMEs** — The _template/topic-README.md already
  has a `> **Before this:**` placeholder. When writing any non-00-setup topic, fill in
  the prerequisite module link. Priority: P2. Effort: S (human ~1hr / CC ~10min).

- [ ] **First cohort feedback loop** — After first cohort uses the repo, update all
  INSTRUCTOR.md files with real observed mistakes and actual timing data. Pre-written
  estimates are educated guesses; cohort data is the real thing.
  Depends on: first cohort completing 03-backend/express/. Priority: P2. Effort: S.

- [ ] **Docusaurus rendered site** — Move docusaurus.config.js from _future/ and set up
  Docusaurus rendering. Rendered sidebar nav is dramatically better UX than GitHub
  folder browsing. Depends on: most modules having stub content. Priority: P3. Effort: M.
