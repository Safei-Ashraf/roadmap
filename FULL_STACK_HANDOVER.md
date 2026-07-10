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

The roadmap UI marks this as `AFTER MERN` in Phase 4. That is the right time to start a small version, not the polished final product.

## Product Goal

Build a multi-user roadmap tracker where each user can:

- Sign up, log in, and log out
- View a seeded roadmap template
- Mark tasks done
- Add personal notes/log entries per task or phase
- See progress totals by priority, phase, and time estimate
- Resume from another browser/device because progress is stored online

Keep it deliberately practical. This is a learning project, not a startup.

## Suggested MERN Stack

- Frontend: React or Next.js
- Backend: Node.js + Express
- Database: MongoDB Atlas
- ODM: Mongoose
- Auth: bcrypt for password hashing, httpOnly cookie sessions or JWTs
- Validation: Zod or express-validator
- Deployment: Render/Fly/Railway for API, MongoDB Atlas for DB, Vercel/Netlify for frontend

If you use Next.js later, you can still keep Express as the learning backend. The point is to practice API boundaries, not hide everything inside framework magic.

## Core Data Model

Start with four collections.

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
  phases: [{ id, label, range, goal, tone, active }],
  lanes: [{ id, title, summary, accent }],
  cards: [{ id, lane, row, span, title, summary, taskIds, groupIds, badge }],
  tasks: [{ id, label, hours, priority, url, links, groupId, trackId }],
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
  startedAt,
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
  completedAt,
  updatedAt
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
```

Later, add:

```txt
POST   /api/roadmaps
PATCH  /api/roadmaps/:id
POST   /api/templates/:id/clone
GET    /api/activity
```

## Build Order

1. Create the Express app with health check and error middleware.
2. Connect MongoDB and add User auth.
3. Seed the current roadmap as a template.
4. Add `GET /api/roadmap` and render it from the frontend.
5. Add `PATCH /api/progress/:taskId` and replace localStorage progress.
6. Add task notes/logs.
7. Add progress summaries on the backend.
8. Deploy API, DB, and frontend.
9. Add migration/version strategy for roadmap template changes.

## Minimum Version

Ship the smallest useful version:

- One seeded roadmap
- Login/register
- Task done state stored in MongoDB
- Notes per task
- Progress totals
- Responsive board copied from this static version

Do not build admin panels, teams, sharing, drag/drop, billing, notifications, or AI generation in v1.

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
- Import/export JSON
- Activity history
- Streaks or weekly focus
- Public read-only share link
- AI-assisted notes summarization
- Admin template editor

## Definition Of Done

The full-stack practice project is successful when:

- A new user can register and see the seeded roadmap.
- Checking a task persists after logout/login.
- Notes persist per task.
- Totals match backend data.
- One user cannot access another user progress.
- The app is deployed and usable from a public URL.
