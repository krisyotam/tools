# AGENTS.md — krisyotam/tools

This repository is a personal workshop of small browser-only utilities, mounted at `krisyotam.com/tools`. It is touched **only by AI coding agents** (Claude Code, Codex CLI, the Claude web app). The human (Kris) never edits these files directly — he requests new tools by name and description, and you build them.

Read this entire file before adding or modifying anything.

---

## What this repo is

- A static site. No build step. No framework. No package.json. No dependencies.
- Each tool is a single self-contained HTML file at the repository root: `<slug>.html`.
- `index.html` is the homepage — a searchable 3-column table (name / category / description) listing every tool.
- The mounted URL is `krisyotam.com/tools/<slug>` (nginx `try_files` strips the `.html`).
- Design language is shared across every page via inlined theme tokens. Never load `style.css` or any external stylesheet other than Google Fonts.

## What this repo is NOT

- Not a sandbox for experiments. Every file in this repo ships to production.
- Not a place to write prose, blog posts, or anything that isn't a tool.
- Not React, not Next.js, not Vue. **Vanilla HTML, CSS, and JS only.**
- Not allowed to pull in npm packages, bundlers, or runtime dependencies beyond what the browser provides natively.

---

## Source of truth: `tools.json`

`tools.json` is the canonical index of every tool in the repo. **Never hand-edit the table inside `index.html`** — the homepage fetches `tools.json` at load and renders the rows from it.

Schema:

```json
{
  "version": 1,
  "updated": "YYYY-MM-DD",
  "categories": ["text", "encoding", "color", "image", "math", "time", "dev", "misc"],
  "tools": [
    {
      "slug": "regex-tester",
      "name": "regex tester",
      "category": "dev",
      "description": "live match preview with groups",
      "keywords": ["regexp", "regular", "expression", "pattern", "match"],
      "built": true,
      "added": "2026-05-13"
    }
  ]
}
```

Field rules:

| field         | rule                                                                 |
|---------------|----------------------------------------------------------------------|
| `slug`        | lowercase kebab-case, matches the `.html` filename without extension |
| `name`        | display name, lowercase by convention, may contain unicode (`↔`)     |
| `category`    | must be one of the `categories` array entries                        |
| `description` | one short phrase, no period, lowercase                               |
| `keywords`    | array of alternate names, alt-spellings, synonyms — drive search     |
| `built`       | `true` if `<slug>.html` exists and works; `false` if just a wishlist  |
| `added`       | ISO date the entry was first added                                   |

The homepage search box matches against `name + category + description + keywords` — put any alt-spelling or alias the user might type into `keywords`.

## Lookup before building (mandatory)

Before adding a new tool, **always**:

1. Read `tools.json` in full.
2. Check if a tool with the same slug, name, or close keyword match already exists.
3. If a match exists and is `built: true` → tell the user it already exists; do not duplicate.
4. If a match exists and is `built: false` → promote it (build the file, flip `built` to `true`).
5. Only if no match exists → add a new entry.

This step is non-negotiable. Tools will be merged in from other repos over time; the JSON is the deduplication source.

## Workflow: adding a new tool

When asked to "add a tool that does X" or similar:

1. **Read `tools.json`** and check for an existing match (see Lookup above).
2. **Pick a slug.** Lowercase, kebab-case, short. Examples: `base64`, `regex-tester`, `json-yaml`, `qr`.
3. **Pick a category** from the predefined list (see below).
4. **Write `<slug>.html`** by copying the page template (see below) and filling in:
   - `<title>` — `<tool name> — tools`
   - The `<h1>` and tagline
   - The tool's HTML + CSS + JS
5. **Update `tools.json`**:
   - If promoting a placeholder: flip `"built": false` → `"built": true`, refine `description` and `keywords` if needed.
   - If adding fresh: append a new object. Keep the array roughly alphabetical, but exact order doesn't matter — the homepage sorts at render time.
   - Bump the top-level `"updated"` date.
6. **Do not touch `index.html`** — it reads from `tools.json`. The only reason to edit `index.html` is a structural design change to the homepage itself.
7. **Test in your head.** Read the file you just wrote, top to bottom. Does the theme toggle work? Does the back link go to `./`? Does the page render on a 360px-wide phone?
8. **Do not run a build.** There is no build. Do not run `npm`, `make`, or anything else.

## Categories (predefined)

| category   | scope                                              |
|------------|----------------------------------------------------|
| `text`     | manipulating, counting, comparing, transforming text |
| `encoding` | base64, url encoding, hashing, format conversion   |
| `color`    | pickers, palettes, contrast, format conversion     |
| `image`    | resizing, format conversion, ascii, qr             |
| `math`     | calculators, unit conversion, statistics           |
| `time`     | timestamps, durations, timezones, calendars        |
| `dev`      | regex, json, yaml, diff, uuid                      |
| `misc`     | anything that doesn't fit                          |

If a tool fits two, pick the most specific. If it fits none, propose `misc` and explain in the PR/commit message.

---

## Page template (`_template.html`)

Every tool page must follow this exact shell. Copy verbatim, replacing the `{{TOOL_*}}` placeholders and adding tool-specific markup/CSS/JS in the marked regions. **Do not change theme tokens, font loading, theme toggle, back link, or footer.**

```html
<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>{{TOOL_NAME}} — tools</title>
<script>(function(){try{var t=localStorage.getItem('tools-theme');var d=t||(window.matchMedia('(prefers-color-scheme: dark)').matches?'dark':'light');document.documentElement.classList.add(d);}catch(e){}})();</script>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Lora:ital,wght@0,400;0,500;0,600;1,400&family=Inter:wght@300;400;500;600&family=JetBrains+Mono:wght@400;500&display=swap" rel="stylesheet">
<style>
  :root {
    --bg:0 0% 100%; --fg:0 0% 20%; --fg-dim:0 0% 35%;
    --muted:0 0% 92%; --muted-fg:0 0% 50%;
    --border:0 0% 88%; --rule:0 0% 82%; --accent:0 0% 96%;
    --serif:'Lora',ui-serif,Georgia,serif;
    --sans:'Inter',ui-sans-serif,system-ui,sans-serif;
    --mono:'JetBrains Mono',ui-monospace,Menlo,monospace;
  }
  html.dark {
    --bg:0 0% 7%; --fg:0 0% 96%; --fg-dim:0 0% 78%;
    --muted:0 0% 12%; --muted-fg:0 0% 60%;
    --border:0 0% 17%; --rule:0 0% 22%; --accent:0 0% 11%;
  }
  html.dark { color-scheme: dark; }

  * { margin:0; padding:0; box-sizing:border-box; }
  html, body { background: hsl(var(--bg)); color: hsl(var(--fg)); }
  body {
    font-family: var(--sans);
    font-size: 15px;
    line-height: 1.55;
    -webkit-font-smoothing: antialiased;
    text-rendering: optimizeLegibility;
  }
  /* page wrapper — floors the footer to viewport bottom */
  .page { min-height: 100vh; min-height: 100dvh; display: flex; flex-direction: column; }
  .grow { flex-grow: 1; }
  .shell { width: 100%; max-width: 640px; margin: 0 auto; padding: 32px 32px 56px; }

  .nav { display: flex; align-items: center; justify-content: space-between; margin-bottom: 48px; }
  .back { font-family: var(--mono); font-size: 12px; color: hsl(var(--muted-fg)); text-decoration: none; display: inline-flex; align-items: center; gap: 6px; transition: color 120ms ease; }
  .back:hover { color: hsl(var(--fg)); }
  .back svg { width: 12px; height: 12px; }

  .toggle { appearance: none; background: transparent; border: 1px solid hsl(var(--border)); color: hsl(var(--fg-dim)); width: 32px; height: 32px; border-radius: 4px; cursor: pointer; display: inline-flex; align-items: center; justify-content: center; transition: background 120ms ease, color 120ms ease, border-color 120ms ease; }
  .toggle:hover { background: hsl(var(--accent)); color: hsl(var(--fg)); border-color: hsl(var(--rule)); }
  .toggle svg { width: 16px; height: 16px; }
  .toggle .icon-sun { display: none; }
  .toggle .icon-moon { display: block; }
  html.dark .toggle .icon-sun { display: block; }
  html.dark .toggle .icon-moon { display: none; }

  .head { margin-bottom: 32px; }
  .head h1 { font-family: var(--serif); font-weight: 500; font-size: 28px; letter-spacing: -0.01em; line-height: 1.15; margin-bottom: 4px; }
  .head p  { font-family: var(--serif); font-style: italic; font-size: 14px; color: hsl(var(--muted-fg)); }

  footer { width: 100%; max-width: 640px; margin: 0 auto; padding: 20px 32px 28px; }
  .foot-inner { padding-top: 18px; border-top: 1px solid hsl(var(--rule)); font-family: var(--mono); font-size: 11px; color: hsl(var(--muted-fg)); display: flex; justify-content: space-between; }
  footer a { color: inherit; text-decoration: underline; text-underline-offset: 2px; text-decoration-color: hsl(var(--border)); }
  footer a:hover { text-decoration-color: hsl(var(--muted-fg)); }

  @media (max-width: 560px) {
    .shell { padding: 22px 22px 40px; }
    footer { padding: 12px 22px 24px; }
    .head h1 { font-size: 24px; }
  }

  /* ── tool-specific styles below ──────────────────────────────── */
  /* {{TOOL_STYLES}} */
</style>
</head>
<body>
 <div class="page">
  <div class="grow">
  <main class="shell">
    <div class="nav">
      <a class="back" href="./">
        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="m15 18-6-6 6-6"/></svg>
        tools
      </a>
      <button class="toggle" data-theme-toggle aria-label="Switch to dark">
        <svg class="icon-moon" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M12 3a6 6 0 0 0 9 9 9 9 0 1 1-9-9Z"/></svg>
        <svg class="icon-sun" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><circle cx="12" cy="12" r="4"/><path d="M12 2v2"/><path d="M12 20v2"/><path d="m4.93 4.93 1.41 1.41"/><path d="m17.66 17.66 1.41 1.41"/><path d="M2 12h2"/><path d="M20 12h2"/><path d="m6.34 17.66-1.41 1.41"/><path d="m19.07 4.93-1.41 1.41"/></svg>
      </button>
    </div>

    <div class="head">
      <h1>{{TOOL_NAME}}</h1>
      <p>{{TOOL_TAGLINE}}</p>
    </div>

    <!-- {{TOOL_BODY}} -->
  </main>
  </div>

  <footer>
    <div class="foot-inner">
      <span>{{TOOL_NAME}}</span>
      <span><a href="https://github.com/krisyotam/tools" rel="noopener">github.com/krisyotam/tools</a></span>
    </div>
  </footer>
 </div>

<script>
  // theme toggle (do not modify)
  (function () {
    var btn = document.querySelector('[data-theme-toggle]');
    function paintLabel() {
      var dark = document.documentElement.classList.contains('dark');
      btn.setAttribute('aria-label', dark ? 'Switch to light' : 'Switch to dark');
    }
    paintLabel();
    btn.addEventListener('click', function () {
      var h = document.documentElement;
      var dark = h.classList.contains('dark');
      h.classList.remove('dark', 'light');
      var next = dark ? 'light' : 'dark';
      h.classList.add(next);
      try { localStorage.setItem('tools-theme', next); } catch (e) {}
      paintLabel();
    });
  })();

  // {{TOOL_SCRIPT}}
</script>
</body>
</html>
```

---

## Reference implementation

See `word-counter.html` — the first real tool. Study its structure before writing anything new. It shows how to:
- Use `var(--accent)` for input field background
- Build a stats grid with mono numerals and uppercase mono labels
- Handle responsive collapse at 560px

---

## Design rules (non-negotiable)

These rules exist because Kris cares deeply about visual coherence across every page. Violating any of them makes the site feel cheap.

### Typography

- **Headings:** Lora 500 weight, `-0.01em` letter-spacing.
- **Body / UI text:** Inter 400-500.
- **Numbers, codes, labels, captions:** JetBrains Mono.
- **Italic taglines:** Lora 400 italic, in `hsl(var(--muted-fg))`.
- **Section / column / stat labels:** Mono 11px, `0.12em` letter-spacing, uppercase, muted-fg color.
- Body font-size is `15px`. Captions are `12-14px`. Stat numbers are `22px` mono.

### Color

- Use the defined HSL tokens. **Never** hardcode hex or rgb values.
- The two surface levels are `--bg` (page) and `--accent` (input fields, hover, soft fills). That is it.
- Borders are `--border` (frames) and `--rule` (hairlines between content). They are subtly different — respect that.
- Foreground tiers: `--fg` (primary), `--fg-dim` (secondary), `--muted-fg` (tertiary captions).
- If you need an accent color (warnings, success, etc.), introduce a new pastel token from the krisyotam palette as a comment, but the default position is **monochrome**.

### Layout

- Page content max-width: **720px**.
- Outer padding: `32px` desktop, `22px` phone (<560px).
- Top padding on shell: `32px` (tool page) / `80px` (homepage).
- Bottom padding on shell before footer: `64px` (tool page) / `48-64px` (homepage).
- The footer must be **floored** to the viewport bottom — `body` uses `display: flex; flex-direction: column; min-height: 100%`, and `.shell` uses `flex: 1`.

### Interaction

- All hover transitions: `120ms ease`. Borders/inputs: `150ms ease`.
- No animations beyond color/border transitions. No fades, no slides, no fancy entrances.
- All inputs are borderless or rule-bottom-only — never boxed. Exception: textareas may have a full border + `--accent` fill.
- Focus state: borders deepen to `--fg-dim`. No glow, no shadow rings.

### Accessibility & phone-first

- All interactive controls must have an `aria-label` if their purpose isn't textual.
- Theme toggle uses Lucide `Sun` / `Moon` SVGs, swapped via CSS based on `html.dark`.
- Test mental layout at 360px width. If it doesn't fit, write a media query at 560px.
- Inputs must be usable on touch (min 32px tap target, prefer 40px+).
- No reliance on hover for primary interaction — touch users can't hover.

---

## Self-containment rules

- **One file per tool.** No companion `.js` or `.css` files. Everything inlined.
- **No external scripts** except the Google Fonts stylesheet. No CDN jQuery, no React from unpkg, nothing.
- **No build pipeline.** A new tool must be deployable by copying its `.html` file to the server.
- **If a tool needs a library** (e.g. a QR encoder), prefer a small inline implementation. If too large, fetch from a versioned CDN URL inside `<script src=...>` with `integrity=` hash, and document the dependency at the top of the file in a comment.
- **No analytics, no telemetry, no tracking.** Period.
- **No data leaves the page.** All processing is client-side. The user pastes a secret into a hashing tool, the secret never leaves their device.

---

## Anti-patterns to refuse

If asked to do any of these, push back:

- "Make it look more modern" → ask what's specifically off; don't reach for shadows, gradients, or rounded corners by default.
- "Add a dashboard" → this isn't an app, it's a list of single-purpose pages.
- "Use Tailwind" → no. The CSS is small enough that adding Tailwind is heavier than the rest of the page combined.
- "Add a build step" → no. The point is zero infra.
- "Send the result to a server" → no. Tools are client-side only.
- "Make the homepage a grid of cards" → no. The 3-column table is the design.
- "Add icons next to tool names in the table" → no. Names carry meaning; icons add visual noise.

---

## Commit messages

- Short. Imperative. Lowercase. Examples:
  - `add regex tester`
  - `add hash (sha-256, sha-1, md5)`
  - `fix word counter mobile layout`
  - `link uuid tool from index`
- Never add a Claude/Codex co-author line.

---

## Final checklist before finishing

- [ ] Looked up `tools.json` first; no duplicate built
- [ ] `<title>` set correctly
- [ ] Back link points to `./`
- [ ] Theme toggle present, Sun/Moon SVGs in place
- [ ] Footer hairline stays within the centered column width
- [ ] Footer source link → `github.com/krisyotam/tools`
- [ ] Mobile (<560px) tested mentally
- [ ] `tools.json` updated: entry promoted to `built: true` OR new entry added; `updated` date bumped
- [ ] `index.html` left untouched (unless intentional design change)
- [ ] No external dependencies, no analytics, no build step
