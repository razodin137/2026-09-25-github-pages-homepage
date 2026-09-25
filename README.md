# repos-index

A single-file dashboard that lists every repo from [razodin137](https://github.com/razodin137) and the [mvvk-space](https://github.com/mvvk-space) org, each with one-click access to its published site, its GitHub repo, and an inline preview.

**Live at:** <https://razodin137.github.io/2026-09-25-github-pages-homepage/>

## Why

GitHub's repo list is a mess to click through. This gives a fast triage flow instead:

1. Scan the list — each card shows the repo name, description, language, and last-push date.
2. **"What's this about?"** — the description is right there.
3. **"Is it published?"** — if a site exists, OPEN SITE / PREVIEW buttons appear.
4. Move on, in seconds.

## Features

- **Live data, zero maintenance** — queries the GitHub REST API client-side on every page load. New repos and new pages sites appear automatically. No build step, no server, no cron, nothing to babysit.
- **Site links from the repo's own `homepage` field** — falls back to `https://<owner>.github.io/<repo>/` when a repo has Pages enabled but no homepage set.
- **Filters** — ALL / RAZODIN137 / MVVK-SPACE / HAS PAGES.
- **Search** — live filter across names and descriptions.
- **Inline preview** — embeds the published site in an iframe without leaving the page.
- **Neobrutalist wireframe styling** — ink borders, hard shadows, Courier New, ruled-paper background.

## Keyboard shortcuts

| Key | Action |
| --- | --- |
| `J` / `K` or `↓` / `↑` | select next / previous repo |
| click | select any repo |
| `O` | open the selected repo's **site** in a new tab |
| `P` | toggle inline **preview** of the site |
| `R` | open the **repo** on GitHub |
| `/` | focus the search box (`Esc` / `Enter` to leave) |

## How it works

The whole thing is one static `index.html`. On load it fetches:

```
GET https://api.github.com/users/<owner>/repos?per_page=100&sort=pushed
```

for each owner, then renders the cards. GitHub's API sends `access-control-allow-origin: *`, so direct browser calls work from any static host — no proxy needed.

**Rate limits:** unauthenticated API calls are limited to 60/hour per IP, and this app uses 2 requests per visit (paginated if you ever exceed 100 repos). Fine for personal use; if you hit the limit, the page tells you when it resets.

## Adding more owners

Edit the `OWNERS` array near the top of the script in `index.html`:

```js
const OWNERS = ["razodin137", "mvvk-space"];
```

Any user or org works — the API endpoint is the same.

## Embedding caveat

Some sites (GitHub itself, sites with `X-Frame-Options: DENY`, etc.) refuse to be iframed and will render blank in the preview. The OPEN SITE button always works.

## Deployment

Any static host works. This repo serves it via GitHub Pages from the `master` branch:

```
gh api -X POST repos/<you>/<repo>/pages -f "source[branch]=master" -f "source[path]=/"
gh api -X PATCH repos/<you>/<repo> -f homepage="https://<you>.github.io/<repo>/"
```

## License

No license — it's a personal utility. Do what you want.