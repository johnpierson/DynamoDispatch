---
name: dynamo-dispatch
description: Turn Dynamo GitHub release notes into a polished magazine-style editorial HTML magazine saved to issues/. Input: a GitHub URL, pasted release notes, or a changelog.
---

# Dynamo Dispatch

### What to do

1. If given a URL with an anchor (e.g. `#410`), fetch the page and extract **only** the content under that heading. Do not read, summarize, or reference any other section on the page. All content — editorial summaries, bullet lists, PR image fetching — must come exclusively from the line items in that anchored section.
2. Identify the most important shipped changes, fixes, improvements, and breaking changes within that section.
3. Group related updates into a few themes instead of repeating every bullet.
4. Rewrite the release as a magazine-style issue with clear hierarchy and plain English.
5. Highlight anything especially actionable for users, developers, or teams adopting the release.
6. If the notes are thin, say what is missing instead of guessing.

### Default output (markdown)

```
# [product] — [version]

## cover line
Short headline capturing the release.

## opener
2–4 sentences in plain English.

## why this release matters
3–5 bullets focused on impact.

## the big changes
For each major theme or feature:
- title
- what changed
- why it matters
- who should care

## under the hood
Notable technical/platform/developer-experience improvements.

## bottom line
Short editorial verdict.
```

### If asked for HTML

Generate a single self-contained HTML file as a **horizontal-swipe editorial magazine**. The page count is dynamic — one content page per release section, plus a fixed cover, verdict, and back cover.

**Fonts** (Google Fonts via `<link>`):
- `Playfair Display` — headlines
- `Source Serif 4` — body text
- `IBM Plex Mono` — metadata, tags, stats labels

**Aesthetic:** E-ink monochrome only. No color — only black (`#0a0a0a`), white (`#f5f2ed`), and grays. No box shadows. Crisp borders. Think Monocle magazine.

---

#### Page Structure (dynamic total)

**Total pages = 1 cover + N content pages + 1 verdict + 1 back cover.**
Give every distinct release section its own page — do not merge or drop sections to hit a target count. If a release has 20 sections, the magazine has 23 pages. If it has 5, it has 8.

Set `const TOTAL = <actual page count>` in the JS. Build `LIGHT_PAGES` as a `Set` of 0-indexed page numbers: page 0 = dark (cover), content pages alternate starting light at index 1, verdict = dark, back cover = dark.

**Page 1 — Dark cover:**
- `../assets/dynamo-logo.png` top-left, `width: clamp(160px, 22vw, 360px)`, **no CSS filter** (the logo is multicolored — display its natural colors)
- Product name in Playfair Display, huge (10vw+)
- Version number in IBM Plex Mono, 4vw
- Tagline (release headline) in Source Serif italic, 2vw
- Date + issue metadata in IBM Plex Mono, bottom-left, small
- **Full-bleed interactive canvas behind all content** (`position: absolute; inset: 0; pointer-events: none`). Add `position: relative` to the cover page div; wrap all cover text content in a `<div style="position:relative;z-index:1;">`.

**Cover canvas animation — "DYNAMO DISPATCH" particle formation:**

Include `family=Open+Sans:wght@400` in the Google Fonts link.

The canvas animates through three phases:
1. **Fly-in (1.5s):** 1000 nodes scatter in from random positions and ease into pixel positions sampled from "DYNAMO DISPATCH" rendered in Open Sans 400 on an offscreen canvas. Font sizes: `H * 0.30` for "DYNAMO", `H * 0.20` for "DISPATCH", gap `H * 0.03`. Sample step: 4px, alpha threshold: 120. Connection distance during formation: 55px. Node radius × 1.5 during formation.
2. **Hold (2.4s):** nodes hold their letter positions with 1.2px jitter. Connections bright (alpha 0.62, lineWidth 1.1).
3. **Scatter → drift:** nodes beyond index 250 get staggered `dieAt` timestamps (random within 700ms) and fade out via `n.alpha -= 0.025` per frame until pruned. Remaining 250 nodes drift freely, bouncing off walls. Mouse on cover page repels nearby nodes (90px radius) and draws bright connection lines to nodes within 220px. Drift connections: 170px distance, alpha 0.22, lineWidth 0.7. Connection distance expands from 55 → 170px over 1800ms.

**Use a spatial grid for connection drawing** (cell size = linkDist) — only check same-cell and 4 forward-neighbour cells `[1,0],[-1,1],[0,1],[1,1]`. Skip connection drawing entirely during fly-in. This keeps the loop O(n) instead of O(n²).

Each node shape: `{ sx, sy, tx, ty, x, y, vx, vy, r: 1.8–3.3, alpha: 1, dieAt: Infinity }`.

Use `document.fonts.ready.then(() => { init(); tick(); })` to ensure Open Sans is loaded before sampling text pixels.

Pause rendering (`requestAnimationFrame` still fires but clears and returns) when not on page 0.

Node colour throughout: `rgba(245,242,237, …)` (matches `--paper`).

**Pages 2–N — Content pages (one per release section):**
Every distinct section in the release notes gets its own page. Do not merge sections to save space, and do not invent pages to pad. Do not add a Watch-Outs page.

Alternate light/dark: page 2 = light, page 3 = dark, page 4 = light, etc.

- **Light page:** background `#f5f2ed`, text `#0a0a0a`
- **Dark page:** background `#0a0a0a`, text `#f5f2ed`

Each content page uses a **55/45 split layout** (flex row, full bleed):

*Left column (55%):* padding `4vw 3vw 5vh 6vw`. All flex children use `flex-shrink: 0` except the bullet list.
- Giant ghosted section rank number — Playfair Display, ~30vw, opacity 0.06, positioned absolute behind content
- Category tag — IBM Plex Mono, 11px, caps, letter-spacing 0.2em; `margin-bottom: 1.2vh`
- Editorial headline — Playfair Display, `clamp(24px, 4.5vw, 72px)`, line-height 1.05; `margin-bottom: 1.5vh`
- Section path — IBM Plex Mono, 13px, e.g. `DynamoDS/Dynamo → Features`; `margin-bottom: 1.5vh`
- Summary — Source Serif, `max(15px, 1.3vw)`, line-height 1.55, opacity 0.85; `margin-bottom: 1.5vh`
- Bullet list of key items — Source Serif, `max(14px, 1.1vw)`, line-height 1.65, opacity 0.85; **`flex: 1; min-height: 0; overflow: hidden`** — this is the clip element that prevents overflow

*Right column (45%):*
- Key stat (PR count, fix count, or a notable number) — Playfair Display, **6.5vw minimum**
- Stat label — IBM Plex Mono, 12px, caps
- Tech/stack tag — IBM Plex Mono pill (border, no fill)
- PR image (if available, filtered grayscale, no border-radius, no shadow, `width: 100%`, `max-height: 28vh`, `object-fit: cover`)
- `<figcaption>` in IBM Plex Mono 11px below image
- **Callout box** — background: 5% tint of text color; 4px solid left border (full text color); padding 20px 24px; `margin-top: auto`:
  - Label: `IBM Plex Mono 11px caps` — "WHAT THIS MEANS FOR YOU"
  - Body: Source Serif `max(15px, 1.3vw)`, opacity 0.8 — a personalized note for a senior Dynamo developer at Autodesk (C#/.NET, WPF, open-source AEC platform). Be specific: name the feature/fix and explain the concrete workflow or codebase implication.

**Page N+1 — Verdict (dark):**
- No split layout. Full-width centered editorial verdict.
- Playfair Display italic headline, 5vw
- Source Serif body, max(17px, 1.5vw), opacity 0.85, max-width 720px centered
- Upgrade recommendation in IBM Plex Mono, large

**Page N+2 — Dark back cover (always last):**
- Centered: "DYNAMO DISPATCH" in IBM Plex Mono caps, large
- Issue number + date below in Source Serif italic
- `../assets/dynamo-logo.png` centered, height 48px, `filter: brightness(0) invert(1)`, `opacity: 0.5` (white silhouette on dark)

---

#### Rules
- 100vw × 100vh pages. `overflow: hidden` on body. No scrollbars.
- CSS transform-based pagination: `translateX(-100vw * currentPage)` on a flex track.
- **Navigation:** arrow keys (←→), mouse wheel (any delta → page), touch swipe (>50px threshold), dot nav fixed at bottom center.
- Dots: small circles, filled = current page, outline = others. Adapt color to current page background.
- Huge fonts everywhere. Nothing below 15px. No small text.
- High contrast on dark pages. Body text opacity 0.85 minimum. Callout body 0.8 minimum.
- Nothing overlapping. `margin-bottom` between every element. `padding-bottom: 5vh` on content columns.
- Full-bleed edge-to-edge. No floating cards. No padding boxes around content areas.
- All PR images: `filter: grayscale(100%) contrast(1.1)` to match e-ink aesthetic.

---

#### Image placement
Place PR images in the right column of the relevant content page, between the stat and the callout box. One image per page maximum. Apply grayscale filter. If no image available, omit gracefully — the callout box fills the space with `margin-top: auto`.

Save to `C:\Users\piersoj\Repos\Dynamo-Digest\issues\YYYY-MM-DD-brief-title.html`.
Where `brief-title` is a 2–4 word kebab-case summary (e.g. `node-graph-performance`).

### PR Image Fetching

When generating HTML, enrich spreads with screenshots and images pulled from the linked GitHub PRs. Do this before writing the HTML so images can be placed inline.

**Step 1 — Identify candidate PRs.**

Only consider PRs that appear in the anchored section's bullet points. Do not fetch PRs from other sections of the page, prior releases, or inferred context.

Among those, only fetch PRs likely to contain visual content. Look for these signals in the PR title or surrounding context:

- Keywords: `icon`, `UI`, `panel`, `style`, `color`, `theme`, `preview`, `thumbnail`, `gizmo`, `drag`, `animation`, `splash`, `badge`, `button`, `menu`, `sidebar`, `tooltip`, `highlight`, `render`, `template`, `home screen`, `node`, `canvas`
- Section type: Features, Templates, Bug Fixes with visual symptoms (rendering, bubbles, pins). Skip: Build & Infrastructure, PythonNet3, pure dependency bumps.

Aim for 6–10 candidate PRs per release. Prioritize the most user-visible changes.

**Step 2 — Fetch each PR body.**

Preferred method (via Bash):
```bash
gh api repos/DynamoDS/Dynamo/pulls/{number} --jq '.body'
```

Fallback (if gh unavailable): WebFetch on `https://github.com/DynamoDS/Dynamo/pull/{number}` and extract image URLs from the rendered HTML.

**Step 3 — Extract image URLs.**

Parse the PR body for:
- Markdown images: `![...](url)`
- Raw GitHub image URLs matching these hosts:
  - `https://user-images.githubusercontent.com/`
  - `https://github.com/user-attachments/assets/`
  - `https://github.com/DynamoDS/Dynamo/assets/`

Include: `.png`, `.jpg`, `.jpeg`, `.gif`, `.webp`
Skip: `.mp4`, `.mov` (video files won't render in HTML img tags)

If a PR body contains multiple images, use the first 1–2 only.

**Step 4 — Place images in the HTML.**

- Place images inside the spread for their section, below the headline and summary.
- Style as a `<figure>` with `max-width: 100%`, `border-radius: 6px`, subtle box-shadow.
- `<figcaption>` shows: PR number + a short description of what the image shows.
- If multiple PRs in a section have images, use a simple 2-column flex grid for the figures.
- Never stretch images. Use `object-fit: contain` or natural sizing.

**Graceful degradation:** If a PR has no images, or fetching fails, skip it silently. Do not block HTML generation waiting for images.

### Style
- Write like a smart product-and-technology magazine editor.
- Concise, clear, useful. Confident tone without hype.
- Do not invent details not in the source material.
- If the release is minor, say so.

### Optional extras
- Dramatic headline, one-line subtitle
- Pull-quote callout
- "Should you upgrade?" mini verdict
- "Best for" / "Notable for" labels
