---
name: digest-magazine
description: Two modes. (1) Morning Edition — fetch Hacker News front page, curate top 10 stories to taste, render as a multi-spread editorial HTML magazine saved to magazines/YYYY-MM-DD.html. (2) Dynamo Dispatch — turn Dynamo GitHub release notes into a polished magazine editorial saved to issues/. Detect mode from input: no argument or "morning edition" triggers Mode 1; a GitHub URL or pasted release notes triggers Mode 2.
---

# The Digest

Two modes. Detect from input.

---

## MODE 1: Morning Edition

**Trigger:** invoked with no argument, "morning edition", "morning", or fired from cron.

### What to do

1. Fetch the Hacker News front page: `https://news.ycombinator.com/`
2. Extract all 30 story titles, URLs, domains, points, and comment counts.
3. If story details are thin, fetch the top story IDs from `https://hacker-news.firebaseio.com/v1/topstories.json` and individual items at `https://hacker-news.firebaseio.com/v1/item/{id}.json`.
4. Apply the taste filter below and pick the best 10 stories.
5. For each story, write a 2–3 sentence editorial summary — not a paraphrase of the title, but a genuine editorial take on why it matters.
6. Determine if each story is personally relevant (see "Applies to You" below). If yes, add a flag.
7. Render the full HTML magazine using the layout system below.
8. Save to `C:\Users\piersoj\Repos\Dynamo-Digest\magazines\YYYY-MM-DD.html`. Create the folder if it does not exist.

---

### Taste Filter

**Lean into:**
- AI tools, coding assistants, LLM research, and applied AI
- Developer tools, editors, CLIs, build systems, languages
- Creative software, generative design, computational art
- Privacy, security, open-source rights, digital sovereignty
- Weird science, systems thinking, unusual engineering
- Anything actionable — a tool you can use today, a technique you can apply, a paper with real implications
- AEC (architecture, engineering, construction) technology
- Computational design and visual/node-based programming
- .NET, C#, Python ecosystem news

**Skip:**
- Crypto, NFTs, blockchain unless directly tied to a non-financial use case
- Job listings, "we're hiring" posts
- Generic business/startup drama with no technical angle
- Pure politics or culture war content
- "Ask HN" and "Show HN" posts unless genuinely exceptional
- Paywalled content with no accessible summary

**Exclusion list (user-configurable):**
> ⚠ **TODO:** Replace this section with your personal skip list. Examples: "skip anything about Rust rewrite memes", "skip Apple hardware rumors", "skip LLM safety debate threads". Until filled in, apply the defaults above.

---

### "Applies to You" Flag

Flag a story if it directly applies to a senior software developer at Autodesk working on Dynamo (a .NET/C# open-source visual programming platform for AEC). Flag when:
- Mentions Autodesk, Revit, Dynamo, or related AEC software
- New AI coding tool or assistant capability relevant to C#/.NET or Python
- Security vulnerability or supply-chain issue affecting .NET, NuGet, or GitHub Actions
- Open-source governance or licensing change affecting a project Dynamo depends on
- Computational geometry, mesh, NURBS, or spatial algorithms research
- Developer tooling that would improve a large WPF/C# desktop codebase
- Anything that could meaningfully change how the Dynamo team ships software

---

### HTML Output: Morning Edition

Generate a single self-contained HTML file. Requirements:

**Fonts:** Load via Google Fonts (not inline — use `<link>` tags):
```
Fraunces (optical size, italic, variable weight 300–900)
Inter (weight 300–900)
```

Google Fonts link:
```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Fraunces:ital,opsz,wght@0,9..144,300;0,9..144,400;0,9..144,700;0,9..144,900;1,9..144,300;1,9..144,400;1,9..144,700;1,9..144,900&family=Inter:wght@300;400;500;600;700;800;900&display=swap" rel="stylesheet">
```

**Rules:**
- No small fonts. Body minimum 16px. Headlines 48px+. Numerals 80px+.
- Each story is its own full-width spread section.
- Generous padding (48px+ vertical).
- The 10 spreads must rotate through the layout styles below in order.
- Include a masthead at the top and a one-line footer at the bottom.

---

### Masthead

```
THE MORNING EDITION          [day, date]          Issue curated for John Pierson
────────────────────────────────────────────────────────────────────────────────
Hacker News · Top 10 · [N stories reviewed]
```

Use Inter, all-caps label style for the top bar. Thin rule below.

---

### 10 Spread Styles (use in order, stories 1–10)

**Spread 1 — HERO**
- Background: `#ffffff`
- Layout: two columns. Left: massive watermark numeral `01` in Fraunces 200px, color `#f0f0f0`. Right: domain tag, headline in Fraunces 64px, summary in Inter 20px, HN stats, optional APPLIES TO YOU badge.
- Headline weight: 900. Optical size: large (opsz 144).

**Spread 2 — MIDNIGHT**
- Background: `#0d1117` (GitHub dark)
- Text: `#e6edf3`
- Layout: centered, single column, max-width 720px. Numeral `02` in Fraunces 120px, color `#30363d`. Headline 56px white. Summary 18px light gray.
- Accent line: 2px `#58a6ff` rule above headline.

**Spread 3 — TERMINAL**
- Background: `#0a0a0a`
- Text: `#00ff41` (classic green)
- Font: Inter monospace feel (or `font-family: 'Courier New', monospace` for body). Fraunces for numeral only.
- Numeral `03` in Fraunces, color `#003d0f`. Headline 48px, letter-spacing 0.05em.
- Add a blinking cursor `▌` after the headline (CSS animation).
- Domain displayed as `> source: domain.com`

**Spread 4 — ROSE ALERT**
- Background: `#fff0f0`
- Accent: `#e5194b`
- Layout: headline-first. A large ALERT STAMP (circular or rounded rect, `#e5194b` background, white text "ALERT" or a star ★) floats top-right. Numeral `04` in Fraunces 160px, color `#ffd6de`.
- Good for security, privacy, or high-urgency stories.

**Spread 5 — ACADEMIC DROP-CAP**
- Background: `#faf7f0` (warm parchment)
- Text: `#2c2c2c`
- Layout: single wide column. Numeral `05` as a small superscript label. First letter of summary is a large drop-cap (Fraunces, 5 lines tall, float left). Headline 52px.
- Thin decorative rule: 1px dashed `#c8b89a`.

**Spread 6 — BIG STAT**
- Background: `#f5f5f5`
- Layout: the numeral `06` or a key number from the story (e.g. "42,000 stars", "3× faster") rendered at 240px Fraunces, anchored bottom-left as a background element. Headline and summary overlay on the right.
- If no key stat, use the HN point count as the big number.

**Spread 7 — BLUEPRINT**
- Background: `#0a1628`
- Text: `#a8d4f5`
- Accent: `#4fc3f7` (cyan)
- Numeral `07` in Fraunces, color `#1a3a5c`. Headline in Fraunces italic 56px, cyan.
- Add a subtle grid background: CSS `background-image: repeating-linear-gradient` creating faint grid lines.

**Spread 8 — BROADSHEET**
- Background: `#f5f0e8` (newsprint)
- Text: `#1a1a1a`
- Layout: two-column text body (CSS `column-count: 2`, `column-gap: 40px`). Headline full-width above columns in Fraunces 60px. Numeral `08` as a small inline chapter marker.
- Add a faint texture feel: `border-top: 4px solid #1a1a1a`, `border-bottom: 1px solid #1a1a1a`.

**Spread 9 — SLAB**
- Background: bold accent — pick from `#f5a623`, `#2ecc71`, or `#9b59b6` based on story category (amber=tools, green=science, purple=AI).
- Text: `#ffffff` or `#1a1a1a` (high contrast).
- Numeral `09` in Inter Black, 180px, color rgba(0,0,0,0.12). Headline in Inter Black 52px, uppercase, letter-spacing -0.02em.

**Spread 10 — FINALE**
- Background: `#1a1a1a`
- Text: `#ffffff`
- Numeral `10` in Fraunces 280px, color `#2a2a2a`, positioned as large background element.
- Headline in Fraunces italic 64px, `#f5c842` (gold).
- After summary: add a short "That's your morning." sign-off line in Inter light italic.

---

### Story Card Elements (each spread must include)

```
[domain tag — small, Inter 12px caps]
[APPLIES TO YOU badge — if flagged: red pill badge, Inter 11px bold]
[Headline — Fraunces, size per spread]
[Editorial summary — 2–3 sentences, Inter, size per spread]
[HN meta — "↑ {points} pts · {comments} comments · {time} ago" — Inter 13px, muted]
```

---

### Style notes
- Do not include HN links as clickable URLs (renders offline).
- Do include the original story domain as a label.
- Avoid dashboard-style dense grids. Each story breathes.
- The full page should feel like opening a magazine, not reading a feed.

---

## MODE 2: Dynamo Dispatch (Release Notes)

**Trigger:** invoked with a GitHub URL, pasted release notes, or a changelog.

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

Generate a single self-contained HTML file as a **14-page horizontal-swipe editorial magazine**.

**Fonts** (Google Fonts via `<link>`):
- `Playfair Display` — headlines
- `Source Serif 4` — body text
- `IBM Plex Mono` — metadata, tags, stats labels

**Aesthetic:** E-ink monochrome only. No color — only black (`#0a0a0a`), white (`#f5f2ed`), and grays. No box shadows. Crisp borders. Think Monocle magazine.

---

#### Page Structure (14 pages total)

**Page 1 — Dark cover:**
- `../assets/dynamo-logo.png` top-left, `width: clamp(160px, 22vw, 360px)`, **no CSS filter** (the logo is multicolored — display its natural colors)
- Product name in Playfair Display, huge (10vw+)
- Version number in IBM Plex Mono, 4vw
- Tagline (release headline) in Source Serif italic, 2vw
- Date + issue metadata in IBM Plex Mono, bottom-left, small

**Pages 2–13 — Content pages (one per release section):**
Map each section of the release notes to one page. If the release has fewer than 12 sections, add a Verdict page and pad with a merged "Under the Hood" summary page if needed. Do not add a Watch-Outs page. If more than 12 sections, group minor ones together.

Alternate light/dark: page 2 = light, page 3 = dark, page 4 = light, etc.

- **Light page:** background `#f5f2ed`, text `#0a0a0a`
- **Dark page:** background `#0a0a0a`, text `#f5f2ed`

Each content page uses a **55/45 split layout** (flex row, full bleed):

*Left column (55%):*
- Giant ghosted section rank number — Playfair Display, ~30vw, opacity 0.06, positioned absolute behind content
- Category tag — IBM Plex Mono, 11px, caps, letter-spacing 0.2em
- Editorial headline — Playfair Display, **5vw minimum**, line-height 1.05
- Section path — IBM Plex Mono, 13px, e.g. `DynamoDS/Dynamo → Features`
- Summary — Source Serif, `max(17px, 1.5vw)`, opacity 0.85, 2–3 sentences
- Bullet list of key items — Source Serif, same size, opacity 0.85

*Right column (45%):*
- Key stat (PR count, fix count, or a notable number) — Playfair Display, **6.5vw minimum**
- Stat label — IBM Plex Mono, 12px, caps
- Tech/stack tag — IBM Plex Mono pill (border, no fill)
- PR image (if available, filtered grayscale, no border-radius, no shadow, `width: 100%`, `max-height: 28vh`, `object-fit: cover`)
- `<figcaption>` in IBM Plex Mono 11px below image
- **Callout box** — background: 5% tint of text color; 4px solid left border (full text color); padding 20px 24px; `margin-top: auto`:
  - Label: `IBM Plex Mono 11px caps` — "WHAT THIS MEANS FOR YOU"
  - Body: Source Serif `max(15px, 1.3vw)`, opacity 0.8 — a personalized note for a senior Dynamo developer at Autodesk (C#/.NET, WPF, open-source AEC platform). Be specific: name the feature/fix and explain the concrete workflow or codebase implication.

**Page 13 — Verdict (dark):**
- No split layout. Full-width centered editorial verdict.
- Playfair Display italic headline, 5vw
- Source Serif body, max(17px, 1.5vw), opacity 0.85, max-width 720px centered
- Upgrade recommendation in IBM Plex Mono, large

**Page 14 — Dark back cover:**
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
- Nothing overlapping. `margin-bottom` between every element. `padding-bottom: 10vh` on all pages.
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
