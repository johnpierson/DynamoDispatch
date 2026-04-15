---
name: dynamo-release-magazine
description: turn Dynamo GitHub release notes into a polished magazine-style editorial summary. use when user provides a GitHub release URL, pasted release notes, or changelog and wants a readable narrative output instead of raw technical notes.
---

# Dynamo Dispatch

Turn GitHub release notes into a concise magazine-style editorial summary.

## Use this for
- a GitHub release URL
- pasted release notes
- changelogs or version announcements
- Dynamo release summaries that should feel polished, readable, and editorial

## Inputs
Accept any of these:
- GitHub release URL
- raw release notes text
- repo or product name, version, and notes

## What to do
1. Read the source material closely.
2. Identify the most important shipped changes, fixes, improvements, and breaking changes.
3. Group related updates into a few themes instead of repeating every bullet.
4. Rewrite the release as a magazine-style issue with clear hierarchy and plain English.
5. Highlight anything especially actionable for users, developers, or teams adopting the release.
6. If the notes are thin, say what is missing instead of guessing.

## Default output
Use this structure unless the user asks for something else.

# [product] — [version]

## cover line
Write a short headline that captures the release.

## opener
Write 2 to 4 sentences explaining the release in plain English.

## why this release matters
Give 3 to 5 bullets focused on impact.

## the big changes
For each major theme or feature, include:
- **title**
- **what changed**
- **why it matters**
- **who should care**

## under the hood
Summarize notable technical, platform, or developer-experience improvements.

## watch-outs
Call out breaking changes, migration notes, known issues, caveats, or unclear areas.

## bottom line
End with a short editorial verdict.

## Style
- Write like a smart product-and-technology magazine editor.
- Stay concise, clear, and useful.
- Prefer plain English over changelog jargon.
- Keep a confident tone without hype.
- Do not invent details that are not in the source material.
- If the release is minor, say so.
- If the release is dense, cluster items into themes.

## Optional extras
If the user wants a stronger magazine feel, add:
- a more dramatic headline
- a one-line subtitle
- a pull-quote style callout
- a "should you upgrade?" mini verdict
- "best for" or "notable for" labels

## If given a GitHub release URL
- Use the published release notes as the primary source.
- Prefer the release body over inferred context.
- If details are missing, say so plainly.

## If asked for HTML
Generate a single self-contained HTML file with:
- large, readable typography
- generous spacing
- clear editorial hierarchy
- inline CSS only
- no tiny text or dashboard-style UI
