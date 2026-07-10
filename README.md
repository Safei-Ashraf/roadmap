# Tech Learning Roadmap

A standalone single-page roadmap for a focused frontend-to-senior-full-stack push.

## Files

- `index.html`: publishable static page.
- `source/roadmap-tracker.original.html`: untouched local source copied from `/Users/safali/Downloads/roadmap-tracker.html`.

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
