# Dynamo Dispatch

A magazine-style release summary for the [Dynamo](https://github.com/DynamoDS/Dynamo) visual programming platform. Each issue is a self-contained HTML slideshow covering features, bug fixes, performance improvements, and ecosystem changes from a Dynamo release.

## Issues

| Issue | Release | Date |
|-------|---------|------|
| [02 — Dynamo 4.3.0](issues/2026-11-17-dynamo-430-dispatch.html) | 4.3.0 | November 2026 |
| [01 — Dynamo 4.1.0](issues/2026-04-16-dynamo-410-dispatch.html) | 4.1.0 | April 2026 |

## Structure

```
assets/          Shared assets (logo, etc.)
issues/          One HTML file per issue
```

## Viewing

Open any file in `issues/` directly in a browser. No build step required.

- Arrow keys, Page Up/Down, Space, scroll, or swipe to navigate slides; Home/End jump to the first and last
- Click the side arrows or the dot indicators to move between slides

Headlines and body text use Autodesk's Artifakt fonts when installed locally and fall back to the system sans-serif otherwise.

Pushing to `main` publishes the issues to GitHub Pages with a generated index (`.github/workflows/deploy-pages.yml`).
