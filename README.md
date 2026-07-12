# Tech Learning Roadmap

A standalone single-page roadmap for a focused frontend-to-senior-full-stack push.

## Files

- `index.html`: publishable static page.
- `source/roadmap-tracker.original.html`: untouched local source copied from `/Users/safali/Downloads/roadmap-tracker.html`.
- `FULL_STACK_HANDOVER.md`: future MERN/full-stack rebuild plan.


## Current Scope

- 103 remaining tasks across MUST and ELECTIVE lanes.
- 371h MUST, 106h ELECTIVE, 477h total remaining.
- Pace estimates: ~8.0 months at 2h/day, ~4.0 months at 4h/day, ~2.7 months at 6h/day.
- Default view is a Jira-style swimlane timeline: parallel work stays side by side inside collapsible phase bands, with continuing cards repeated as shared-progress summaries.
- AI Automation is included as a compact lane.
- HackerRank Weeks 1-2 are removed as completed; Week 3 keeps only the mock test.
- HackerRank practice is grouped into collapsible weekly sections.
- Stephen Grider interview prep is split into section-level tasks so progress is trackable without video-level noise.
- Eloquent JavaScript (ch.11-21) is MUST core alongside DSA.
- O'Reilly and FSA reading are selective chapter unlocks with in-UI "How to read" guides (start when, prerequisites, done criteria, links).
- Phase 4 keeps Postgres/Drizzle as complementary literacy; the MERN fullstack rebuild (practice lane) is the primary portfolio destination, with MongoDB unlocks on the same card.
- React Native lane is a free Expo/RN docs path with How-to-read modules; real-app contribution stays elective.
- Deployed as a static site (Vercel / Railway-compatible via `npm start` → `serve`).

## Run Locally

Open `index.html` directly in a browser, or run a static server:

```sh
npm start
# or: python3 -m http.server 8080
```

Then visit the local URL shown by the server.

## Publish

This repo has no build step. Any static host works:

- GitHub Pages: push the repo, enable Pages from the main branch root.
- Netlify: drag the folder into Netlify Drop or connect the repo.
- Cloudflare Pages: connect the repo and leave build command empty.
- Vercel: import the repo as a static project with no build command.
- Railway: use the `start` script (`npx serve .`) or static site settings.

Progress and phase collapse state are stored in browser `localStorage`, so they are local to the browser/device. Start date and pace target (`startDate`, `targetPace`) live in the same UI state key for projected finish dates.
