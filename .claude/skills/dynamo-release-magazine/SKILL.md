---
name: dynamo-release-magazine
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

### What earns a page

Every content page highlights **one customer win and leads with a great image of it.** This rule decides the page list; the release notes' section headings do not.

- **Customer win:** something a Dynamo user can see or do differently. Build changes, dependency bumps, test fixes, and repo tooling are not customer wins and never get their own page.
- **Great image:** a screenshot or GIF from the PR that shows the change clearly at page size. A tight crop of the menu, node, dialog, or wire qualifies. A full-window capture where the change is too small to read does not, and neither does a test runner, a console, or a blank first frame of a GIF.
- **Find the images first.** Fetch the body of *every* PR in the release section (see PR Image Fetching), then look at each image before you plan pages. Do not pick pages and hunt for images afterwards.
- **No image, no page.** If a change has no image, or only a weak one, reconsider it: it does not get a highlight page. Mention it in the closing roundup instead, and tell the user which changes were held back for lack of an image so they can supply a screenshot.
- **Closing roundup.** One final content page, "Also in [version]", with no image. It summarizes the important changes that did not earn a page (security fixes, performance, features without screenshots) in two short paragraphs.

A release with few good images makes a short issue. That is correct. Do not pad it.

### Writing standards

All copy follows the `dynamo-content-designer` skill (Dynamo repo, `.claude/skills/dynamo-content-designer`). Load it before writing if it is available. The rules that matter most here:

- **Source only.** Every claim, number, and stat must trace to a line item in the anchored release notes section, or to the body of a PR linked from it. Do not infer history ("before this release…"), root causes, breaking changes, or scope ("the only supported…") that the source does not state. If the notes are thin, write less and say so.
- **Why before how.** Open each page with what the reader can now do or what stopped going wrong, then the detail.
- **Plain words, short sentences.** Write for a smart reader who may not be a native English speaker. No idioms, no jokes that need cultural context.
- **Define terms on first use**, in a short clause: "A connector pin is a point you add to a wire to control its route." This includes acronyms and internal names (LibG, DYF, marshaling, rollForward).
- **One idea per paragraph.** Summaries are two short paragraphs (about 40–60 words each), never one block.
- **One word per thing.** Do not vary terms for style (wire/connector, panel/sidebar). Pick one and keep it.
- **No marketing tone.** Avoid "finally", "sleeper hit", "game-changer", "you didn't know you needed", and formula headlines repeated across pages. A headline states the change in plain words: "See Your Installed Version While You Search".
- **Recommend, do not list options.** The callout gives one or two concrete actions with their PR numbers.

Before saving, check each page against the source once more and count anything you quote as a number (fixes, entries, new nodes).

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

Generate a single self-contained HTML file as a **horizontal-swipe editorial magazine**. The page count is dynamic — one content page per illustrated customer win (see "What earns a page"), one closing roundup, plus a fixed Dispatch animation page, cover, verdict, and back cover.

**Fonts** (Autodesk Artifakt, loaded with `@font-face { src: local(...) }` per weight — copy the `@font-face` block from the latest file in `issues/`; always end font stacks with `sans-serif` so machines without Artifakt degrade cleanly):
- `Artifakt Legend` — headlines, stats, cover title
- `Artifakt Element` — body text, metadata, tags, stat labels

**The latest file in `issues/` is the reference implementation** — start from its CSS and JS (navigation, cover canvas, linkifier, narrow-screen layout) rather than rewriting them.

**Aesthetic:** E-ink monochrome only. No color — only black (`#0a0a0a`), white (`#f5f2ed`), and grays. No box shadows. Crisp borders. Think Monocle magazine.

---

#### Page Structure (dynamic total)

**Total pages = 1 Dispatch animation page + 1 cover + N content pages + 1 verdict + 1 back cover.**
N = highlight pages plus the closing roundup. If a release has 9 illustrated customer wins, N = 10 and the magazine has 14 pages total.

Set `const TOTAL = <actual page count>` in the JS. Build `LIGHT_PAGES` as a `Set` of 0-indexed page numbers: page 0 = dark (Dispatch animation), page 1 = dark (cover), content pages alternate starting light at index 2, verdict = dark, back cover = dark.

**Page 1 — Dispatch animation (dark), on a page of its own:**
- Only the full-bleed interactive canvas (`<canvas id="cover-canvas" role="img" aria-label="Dynamo Dispatch">`, `position: absolute; inset: 0; pointer-events: none`) inside `<div class="page dark intro-page">` with `position: relative`. No text, logo, or other content on this page, so nothing overlaps the wordmark. The animation is specified under "Cover canvas animation" below.

**Page 2 — Dark cover:**
- `../assets/dynamo-logo.png` top-left, `width: clamp(160px, 22vw, 360px)`, **no CSS filter** (the logo is multicolored — display its natural colors)
- Product name in Artifakt Legend, huge (10vw+)
- Version number in Artifakt Element, 4vw
- Tagline (release headline) in Artifakt Element italic, 2vw
- Date + issue metadata in Artifakt Element, bottom-left, small
- No canvas on the cover. The animation lives on page 1 only.

**Cover canvas animation — "DYNAMO DISPATCH" particle formation:**

1000 nodes animate through four phases:
1. **Fly-in (1.5s):** 1000 nodes scatter in from random offscreen positions and ease into pixel positions sampled from "DYNAMO DISPATCH" rendered in `400 Npx "Artifakt Legend", sans-serif` on an offscreen canvas. Font sizes: `min(H * 0.30, W * 0.20)` for "DYNAMO", two thirds of that for "DISPATCH", gap one tenth of the "DYNAMO" size, so the wordmark fits narrow screens. Sample step: 4px, alpha threshold: 120. **No connections during fly-in — dots only.**
2. **Connect (1.0s):** connections fade in (alpha 0 → 0.62) between only the first KEEP=250 nodes (the "survivors"). Non-survivors are visible but unconnected. Nodes hold text positions with 1.2px jitter.
3. **Hold (1.2s):** all nodes jitter at text positions. Connections bright (0.62) on the 250.
4. **Scatter → drift:** non-keep nodes get staggered `dieAt` and fade out. Surviving 250 drift freely, bouncing off walls. Mouse repels nearby nodes (90px radius) and draws bright connection lines to nodes within 220px. `linkDist` expands from 55 → 170px over 1800ms; `connAlpha` falls from 0.62 → 0.22.

**Use a spatial grid for connection drawing** (cell size = linkDist) — only check same-cell and 4 forward-neighbour cells `[1,0],[-1,1],[0,1],[1,1]`. This keeps the loop O(n) instead of O(n²).

Each node shape: `{ sx, sy, tx, ty, x, y, vx, vy, r: 1.8–3.3, alpha: 1, dieAt: Infinity, keep: bool }`.

Use `document.fonts.ready.then(...)` to ensure the font is loaded before sampling text pixels.

Stop the `requestAnimationFrame` loop when not on page 0 and restart it on return (the nav script fires a `dispatch:page` event). Size the canvas backing store by `devicePixelRatio`. Under `prefers-reduced-motion: reduce`, draw the formed wordmark once with no animation.

Node colour throughout: `rgba(245,242,237, …)` (matches `--paper`).

**Pages 2–N — Content pages (one per customer win, then the roundup):**
Each page covers one customer win with its image. Order them by how much users will notice them. Do not invent pages to pad, and do not add a Watch-Outs page. The ghost rank number is the running page number (`01`, `02`, …), the category tag is the topic (e.g. `Package Manager`), and the section path is the release notes section the change came from (e.g. `Bug Fixes`).

Alternate light/dark: page 2 = light, page 3 = dark, page 4 = light, etc. — continuation pages follow the same alternating sequence as any other page.

- **Light page:** background `#f5f2ed`, text `#0a0a0a`
- **Dark page:** background `#0a0a0a`, text `#f5f2ed`

Each content page uses a **55/45 split layout** (flex row, full bleed):

*Left column (55%):* `height: 100vh; max-height: 100vh; min-height: 0; overflow: hidden;` padding `4vw 3vw 5vh 6vw`. Use `justify-content: center` to vertically center the content group. **Critical:** use `min-height: 0` — without it, the column as a flex item ignores `height: 100vh` and grows to content size, making `overflow: hidden` useless. All flex children use `flex-shrink: 0`.
- Giant ghosted section rank number — Artifakt Legend, ~30vw, opacity 0.06, positioned absolute behind content
- Category tag — Artifakt Element, 11px, caps, letter-spacing 0.2em; `margin-bottom: 1.2vh`; `flex-shrink: 0`
- Editorial headline — Artifakt Legend, `clamp(24px, 4.5vw, 72px)`, line-height 1.05; `margin-bottom: 1.5vh`; `flex-shrink: 0`
- Section path — Artifakt Element, 13px, e.g. `Features` (section name only); `margin-bottom: 1.5vh`; `flex-shrink: 0`
- Summary — Artifakt Element, `max(15px, 1.3vw)`, line-height 1.6, opacity 0.85; `overflow: hidden` — the summary IS the content. Write it as two `<p class="page-summary">` paragraphs (see Writing standards): first what changed for the reader and any term that needs defining, then the supporting changes. Explain a cause only when the source states it. No bullet list follows — the summary must stand alone. Do not add a numbered or bulleted list to content pages.

*Right column (45%):* `height: 100vh; max-height: 100vh; min-height: 0; overflow: hidden;` padding `4vw 6vw 5vh 4vw`. Same `min-height: 0` rule applies.
- Key stat — Artifakt Legend, **6.5vw minimum**. Stat label: Artifakt Element, 12px, caps. **Always frame as a user-facing outcome, not a raw count.** The number describes what the user now experiences — a `0` stat ("0 tab switches to check your installed version") or a specific outcome count ("8 fixes for connector pins and pinned notes", "Every template shows a thumbnail or a placeholder icon"). The number itself can be a word (e.g. `Every`, `patch`). Avoid labels like "N Feature Additions" or "N Pipeline Updates" — describe the result, not the work. **The stat must be literally true from the source.** Use a `0` stat only when the notes say the thing was eliminated; never claim "0 remaining" bugs or gaps. Count the line items before you print a count.
- Tech/stack tag — Artifakt Element pill (border, no fill). Show where the work happened (e.g. `CLR · Dispatcher · LibG`).
- PR image (required on every highlight page; no filter, no border-radius, no shadow, `width: 100%`, `max-height: 38vh`, `object-fit: contain; object-position: left top` so the whole screenshot shows)
- `<figcaption>` in Artifakt Element 11px below image
- **Callout box** — background: 5% tint of text color; 4px solid left border (full text color); padding 20px 24px; `margin-top: auto`:
  - Label: `Artifakt Element 11px caps` — "WHAT THIS MEANS FOR YOU"
  - Body: Artifakt Element `max(15px, 1.3vw)`, opacity 0.8 — a personalized note for a senior Dynamo developer at Autodesk (C#/.NET, WPF, open-source AEC platform). Be specific: name the feature/fix with its PR number and give one or two concrete actions. Do not speculate about side effects the source does not mention.

**Page N+1 — Verdict (dark):**
- No split layout. Full-width centered editorial verdict.
- Artifakt Legend italic headline, 5vw — one plain sentence or phrase, not a summary. **Always frame positively** — what the user gains, not what was broken. "A Faster, Steadier Dynamo for Large Graphs" not "The Release That Fixed What You Stopped Noticing."
- Artifakt Element body, max(17px, 1.5vw), opacity 0.85, max-width 680px centered — **2–4 sentences maximum**. One sentence on what changed, one on who benefits most, one on whether to upgrade. No recap of individual features — the content pages covered those. Be direct.
- Upgrade recommendation in Artifakt Element, large — a single terse verdict line, e.g. `UPGRADE — STABILITY FIXES JUSTIFY IT` or `HOLD — BREAKING CHANGES, REVIEW FIRST`

**Page N+2 — Dark back cover (always last):**
- Centered: "DYNAMO DISPATCH" in Artifakt Element caps, large
- Issue number + date below in Artifakt Element italic
- `../assets/dynamo-logo.png` centered, height 48px, `filter: brightness(0) invert(1)`, `opacity: 0.5` (white silhouette on dark)

---

#### Rules
- 100vw × 100vh pages. `overflow: hidden` on body. No scrollbars.
- CSS transform-based pagination: `translateX(-100vw * currentPage)` on a flex track.
- **Navigation:** arrow keys (←→), PageUp/PageDown, Home/End, Space, mouse wheel (one page per gesture), touch swipe (>50px, mostly horizontal), clickable side arrows, dot nav fixed at bottom center.
- Dots: small circles, filled = current page, outline = others. Adapt color to current page background. Dots and arrows are `<button>` elements with `aria-label`s.
- **Narrow / portrait screens** (`max-width: 820px` or portrait): stack the two columns and let the page scroll vertically instead of clipping.
- Huge fonts everywhere. Reading text (summary, callout, verdict) never below 15px. Only the caps metadata labels specified above (tags, captions, stat labels) may be 11–13px.
- High contrast on dark pages. Body text opacity 0.85 minimum. Callout body 0.8 minimum.
- Nothing overlapping. `margin-bottom` between every element. `padding-bottom: 5vh` on content columns.
- Full-bleed edge-to-edge. No floating cards. No padding boxes around content areas.
- All PR images: no filter — display in full color.
- **PR references are always clickable links.** Do not manually wrap them in the HTML — instead include the auto-linkifier script below just before `</body>`. It finds every `PR #NNNNN` and `#NNNNN` pattern in `.callout-body`, `.page-summary`, `.pr-caption`, and `.verdict-body` and wraps them in `<a class="pr-link">` tags pointing to `https://github.com/DynamoDS/Dynamo/pull/NNNNN`.

```css
a.pr-link {
  font-family: 'Artifakt Element', sans-serif; font-size: 0.88em;
  color: inherit; text-decoration: underline;
  text-decoration-style: dotted; text-underline-offset: 3px;
  opacity: 0.85; transition: opacity 0.15s;
}
a.pr-link:hover { opacity: 1; text-decoration-style: solid; }
```

```html
<script>
  (function() {
    const BASE = 'https://github.com/DynamoDS/Dynamo/pull/';
    const SCOPES = '.callout-body, .page-summary, .pr-caption, .verdict-body';
    const RE = /(?:\bPR\s+)?(?<![\w&#])#(\d{4,})\b/g;
    function linkify(node) {
      if (node.nodeType === 3) {
        if (!RE.test(node.textContent)) return;
        RE.lastIndex = 0;
        const frag = document.createDocumentFragment();
        let last = 0, m;
        while ((m = RE.exec(node.textContent)) !== null) {
          if (m.index > last) frag.appendChild(document.createTextNode(node.textContent.slice(last, m.index)));
          const a = document.createElement('a');
          a.href = BASE + m[1]; a.target = '_blank'; a.rel = 'noopener'; a.className = 'pr-link';
          a.textContent = m[0]; frag.appendChild(a);
          last = m.index + m[0].length;
        }
        if (last < node.textContent.length) frag.appendChild(document.createTextNode(node.textContent.slice(last)));
        node.parentNode.replaceChild(frag, node);
      } else if (node.nodeType === 1 && !['SCRIPT','STYLE','A'].includes(node.tagName)) {
        Array.from(node.childNodes).forEach(linkify);
      }
    }
    document.querySelectorAll(SCOPES).forEach(linkify);
  })();
</script>
```

---

#### Image placement
Place PR images in the right column of the relevant content page, between the stat and the callout box. Exactly one image per highlight page. No filter — full color. Only the closing roundup has no image; there the callout box fills the space with `margin-top: auto`. Add an `error` listener that hides any `img.pr-image` that fails to load.

Save to `issues/YYYY-MM-DD-brief-title.html` (relative to the repo root).
Where `YYYY-MM-DD` is the date the version was released, not today's date, and `brief-title` is a 2–4 word kebab-case summary (e.g. `dynamo-430-dispatch`). Get the release date with `gh api repos/DynamoDS/Dynamo/releases --jq '.[]|[.tag_name,.published_at]|@tsv'`. The cover and back cover show the same release month and year. Number issues in release order.

After saving, add a row for the new issue to the table in `README.md`.

### PR Image Fetching

When generating HTML, enrich spreads with screenshots and images pulled from the linked GitHub PRs. Do this before writing the HTML so images can be placed inline.

**Step 1 — Identify candidate PRs.**

Only consider PRs that appear in the anchored section's bullet points. Do not fetch PRs from other sections of the page, prior releases, or inferred context.

Among those, only fetch PRs likely to contain visual content. Look for these signals in the PR title or surrounding context:

- Keywords: `icon`, `UI`, `panel`, `style`, `color`, `theme`, `preview`, `thumbnail`, `gizmo`, `drag`, `animation`, `splash`, `badge`, `button`, `menu`, `sidebar`, `tooltip`, `highlight`, `render`, `template`, `home screen`, `node`, `canvas`
- Section type: Features, Templates, Bug Fixes with visual symptoms (rendering, bubbles, pins). Skip: Build & Infrastructure, PythonNet3, pure dependency bumps.

Fetch every PR in the section that could be a customer win, not a sample. Skipping PRs here is how good images get missed.

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

**Never use `https://private-user-images.githubusercontent.com/...?jwt=...` URLs.** Those are signed links from the rendered PR page and expire in about five minutes, leaving a broken image. Use the durable `https://github.com/user-attachments/assets/<uuid>` form from the raw PR body (the `<uuid>` is the trailing GUID in the signed URL's filename).

If a PR body contains multiple images, pick the single best one.

**Step 4 — Place images in the HTML.**

Follow "Image placement" above: right column, between the stat and the callout, one image per page, with a caption showing the PR number plus a short description of what the image shows.

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
