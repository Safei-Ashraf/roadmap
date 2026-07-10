# Tech Learning Roadmap

A standalone single-page roadmap for a focused frontend-to-senior-full-stack push.

## Files

- `index.html`: publishable static page.
- `source/roadmap-tracker.original.html`: untouched local source copied from `/Users/safali/Downloads/roadmap-tracker.html`.


## Current Scope

- 80 remaining tasks across MUST and ELECTIVE lanes.
- 326h MUST, 88h ELECTIVE, 414h total remaining.
- Pace estimates: ~6.9 months at 2h/day, ~3.5 months at 4h/day, ~2.3 months at 6h/day.
- Default view is a swimlane timeline: parallel work stays side by side by phase row.
- AI Automation is included as a compact lane; lower-value elective backlog was trimmed so total hours stay flat.
- HackerRank Weeks 1-2 are removed as completed; Week 3 keeps only the mock test.
- HackerRank practice is grouped into collapsible weekly sections.
- Official resource links are included where a task maps to a specific course, book chapter, docs site, or platform.

## Run Locally

Open `index.html` directly in a browser, or run a static server:

```sh
python3 -m http.server 8080
```

Then visit `http://localhost:8080`.

## Publish

This repo has no build step. Any static host works:

- GitHub Pages: push the repo, enable Pages from the main branch root.
- Netlify: drag the folder into Netlify Drop or connect the repo.
- Cloudflare Pages: connect the repo and leave build command empty.
- Vercel: import the repo as a static project with no build command.

Progress is stored in browser `localStorage`, so it is local to the browser/device.
