# Full-Stack Roadmap App Handover

This is the future rebuild plan for turning the current static roadmap tracker into a real full-stack practice app. Treat it like an advanced todo app: same roadmap domain, but with auth, a live backend, and persistent user progress.

## When To Start

Do not start this before the Phase 4 backend work is comfortable enough that you can build and explain:

- Express routing and middleware
- MongoDB/Mongoose models and indexes
- Auth with hashed passwords and signed sessions or JWTs
- CRUD APIs with validation and error handling
- Basic deployment, environment variables, and database connection strings
- Frontend API calls with loading, empty, and error states

The roadmap UI marks this as `MERN project` in Phase 4 (practice lane), with MongoDB selected reading on the same card. That is the right time to start a small version, not the polished final product.

## Product Goal

Build a multi-user roadmap tracker where each user can:

- Sign up, log in, and log out
- View a seeded roadmap template
- Mark tasks done (with completion timestamps)
- Add personal notes/log entries per task or phase
- See progress totals by priority, phase, and time estimate
- See pace projections (start date, avg h/day, MUST vs full-plan finish)
- Use Focus (WIP + parallel lanes) and Pomodoro while studying
- See activity hints (yesterday / this week / phase finish vs ETA) when data exists
- Resume from another browser/device because progress is stored online

Keep it deliberately practical. This is a learning project, not a startup.

## Static App Feature Inventory (port these)

The current `index.html` already implements the following client-side. The MERN rebuild should preserve behavior and move persistence to the API.

### Board & progress

- Swimlane timeline (phases × lanes), spanning cards with continuation summaries
- MUST / ELECTIVE filters, expand/collapse phases
- Task checkboxes; hours reduce remaining totals immediately
- Progress storage key: `tech-learning-roadmap-progress-v1`
- UI storage key: `tech-learning-roadmap-ui-v1`

### Completion stamps (revised)

Progress is no longer a bare boolean. Shape:

```js
{
  [taskId]: { done: true, completedAt: "2026-07-12T11:22:33.000Z" | null }
}
```

- Legacy `true` values migrate on load to `{ done: true, completedAt: null }`
- Entries with `completedAt: null` count as done for totals but **do not** contribute to yesterday/week hours
- New checks always set `completedAt` to now (ISO)

### Pace tracking

- User `startDate` (min 2026-07-01) and `targetPace` (2 / 4 / 6 h/day)
- Avg h/day from stamped done hours ÷ calendar days since start
- Primary finish estimate: MUST; secondary: full plan
- What-if chips when no stamped hours yet

### Activity hints (conditional only)

Rendered under the status strip when true:

- Yesterday hours &gt; 0 → `Yesterday Xh`
- This week (Mon–today) hours &gt; 0 → `This week Xh`
- Phase MUST complete with milestones → `Finished {phase} in X wk · ETA was Y wk`

### Phase milestones (UI state)

```js
phaseMilestones: {
  [phaseId]: {
    firstAt: ISO,          // first MUST progress in that phase's home cards
    completedAt: ISO,      // all home-card MUST tasks done
    etaDaysAtFirst: number // sum(mustHoursInPhase) / targetPace at firstAt
  }
}
```

Clear with Reset progress (along with task progress).

### Focus overlay

- Explicit WIP card (`wipCardId`)
- Now / then on WIP card; parallel lane heads in same phase
- Jump + highlight on board

### Pomodoro

- Work 15/30/45; break 5/15/30
- Floating panel + mini chip when session active and panel closed (`WORK 44:55`)
- Alarms from `/sounds/` (Viktor on work end, Gnar on break end)
- Prefs: `timerWorkMins`, `timerBreakMins`, `timerPanelOpen` (remaining time not restored on reload)

### Reading / RN / MERN narrative

- Eloquent MUST; O'Reilly selective unlocks with How-to-read guides
- RN free Expo docs path; apply-after-modules ordering
- Phase 4 MERN project card + Mongo unlocks; Postgres remains complementary literacy

## Suggested MERN Stack

- Frontend: React or Next.js
- Backend: Node.js + Express
- Database: MongoDB Atlas
- ODM: Mongoose
- Auth: bcrypt for password hashing, httpOnly cookie sessions or JWTs
- Validation: Zod or express-validator
- Deployment: Render/Fly/Railway for API, MongoDB Atlas for DB, Vercel/Netlify for frontend
- Static assets: keep `sounds/` on the frontend CDN or public folder

If you use Next.js later, you can still keep Express as the learning backend. The point is to practice API boundaries, not hide everything inside framework magic.

## Core Data Model

Start with these collections.

### User

```js
{
  _id,
  email,
  passwordHash,
  displayName,
  createdAt,
  updatedAt
}
```

### RoadmapTemplate

Stores shared roadmap structure. Seed this from the current static `ROADMAP` object.

```js
{
  _id,
  version,
  title,
  phases: [{ id, label, range, goal, tone, active, concepts }],
  lanes: [{ id, title, summary, accent }],
  cards: [{ id, lane, row, span, title, summary, taskIds, groupIds, badge, guide }],
  tasks: [{ id, label, hours, priority, url, links, groupId, trackId }],
  groups: [{ id, title, summary, guide, taskIds }],
  createdAt,
  updatedAt
}
```

### UserRoadmap

Links one user to one template version.

```js
{
  _id,
  userId,
  templateId,
  title,
  startedAt,       // maps to static startDate
  targetPace,      // 2 | 4 | 6
  wipCardId,
  timerPrefs: { workMins, breakMins },
  createdAt,
  updatedAt
}
```

### ProgressEntry

Stores user-specific progress and notes without mutating the template.

```js
{
  _id,
  userId,
  roadmapId,
  taskId,
  done,
  notes,
  completedAt,     // required when done=true for activity stats
  updatedAt
}
```

Index: `{ userId: 1, roadmapId: 1, taskId: 1 }` unique.

### PhaseMilestone

Optional separate collection, or embed on `UserRoadmap`.

```js
{
  _id,
  userId,
  roadmapId,
  phaseId,
  firstAt,
  completedAt,
  etaDaysAtFirst,
  updatedAt
}
```

### Activity (derived or stored)

Prefer **derive** yesterday / this-week hours from `ProgressEntry.completedAt` + template task hours in a stats endpoint. Do not duplicate hour totals in a separate collection unless caching becomes necessary.

Pomodoro sessions can stay client-only in v1. Later:

```js
{
  _id,
  userId,
  roadmapId,
  mode,          // work | break
  plannedMins,
  startedAt,
  endedAt,
  completed      // hit zero vs abandoned
}
```

## API Shape

Keep the first API small.

```txt
POST   /api/auth/register
POST   /api/auth/login
POST   /api/auth/logout
GET    /api/me

GET    /api/roadmap
GET    /api/progress
PATCH  /api/progress/:taskId
POST   /api/progress/:taskId/notes

GET    /api/stats/pace
GET    /api/stats/activity
GET    /api/stats/phases

PATCH  /api/settings          # startedAt, targetPace, wipCardId, timer prefs
```

Later, add:

```txt
POST   /api/roadmaps
PATCH  /api/roadmaps/:id
POST   /api/templates/:id/clone
GET    /api/activity
POST   /api/pomodoro/sessions
```

### Stats contracts (match static UI)

`GET /api/stats/activity` example:

```js
{
  yesterdayHours: 6,
  weekHours: 14,
  weekStart: "2026-07-07",
  phases: [
    {
      phaseId: "start",
      label: "1 · Foundation",
      finished: true,
      actualWeeks: 2.1,
      etaWeeks: 3.4
    }
  ]
}
```

Omit or zero-out fields the UI should hide (same conditional rules as static).

## Build Order

1. Create the Express app with health check and error middleware.
2. Connect MongoDB and add User auth.
3. Seed the current roadmap as a template (including groups/guides).
4. Add `GET /api/roadmap` and render it from the frontend.
5. Add `PATCH /api/progress/:taskId` with `completedAt`; replace localStorage progress.
6. Add settings (start date, pace, WIP) and pace/activity/phase stats endpoints.
7. Port Focus + Pomodoro UI; keep timer alarms as static assets.
8. Add task notes/logs.
9. Deploy API, DB, and frontend.
10. Add migration/version strategy for roadmap template changes.

## Minimum Version

Ship the smallest useful version:

- One seeded roadmap
- Login/register
- Task done state + `completedAt` stored in MongoDB
- Notes per task
- Progress totals + pace/activity hints
- Responsive board copied from this static version

Do not build admin panels, teams, sharing, drag/drop, billing, push notifications, or AI generation in v1.

## Good Practice Targets

Use this project to practice:

- Clean route/controller/service separation
- Request validation
- Auth middleware
- Ownership checks on every user-owned record
- Indexes on `userId`, `roadmapId`, and `taskId`
- Environment variable handling
- Deployment logs and basic debugging
- Seed scripts and repeatable local setup
- README setup instructions
- Idempotent progress updates and timezone-safe day/week bucketing (store UTC, bucket in user local TZ or document UTC-only)

## Security Basics

- Hash passwords with bcrypt.
- Use httpOnly cookies if possible.
- Never store tokens in localStorage if you can avoid it.
- Validate every request body.
- Check `userId` ownership before reading or writing progress.
- Keep secrets in environment variables.
- Add rate limiting to auth routes.
- Do not expose password hashes in API responses.

## Future Enhancements

Only after v1 works:

- Multiple roadmaps per user
- Custom task creation
- Import/export JSON (including stamped progress)
- Server-side Pomodoro history
- Streaks or weekly focus goals
- Public read-only share link
- AI-assisted notes summarization
- Admin template editor

## Definition Of Done

The full-stack practice project is successful when:

- A new user can register and see the seeded roadmap.
- Checking a task persists after logout/login with `completedAt`.
- Notes persist per task.
- Totals and activity/pace stats match backend data.
- One user cannot access another user progress.
- The app is deployed and usable from a public URL.
