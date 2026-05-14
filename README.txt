tools
=====

A workshop of small browser-only utilities by Kris Yotam, mounted at
https://krisyotam.com/tools.

Each tool is a single self-contained HTML file. No build step, no framework,
no runtime dependencies beyond the browser. Visit a tool, use it, close the
tab. Nothing leaves your machine.


Layout
------

    /
    ├── README.txt        this file
    ├── AGENTS.md         the spec — read first if you're editing
    ├── CLAUDE.md         symlink → AGENTS.md
    ├── index.html        homepage; renders the table from tools.json
    ├── tools.json        canonical index of every tool (source of truth)
    └── tools/
        ├── word-counter.html
        ├── base64.html
        ├── …             one HTML file per tool

The homepage fetches tools.json at load and renders the rows. Tools live
in the tools/ subdirectory. nginx serves them at /tools/<slug> with no
.html extension.


Who edits this repo
-------------------

This repo is touched only by AI coding agents (Claude Code, Codex CLI,
the Claude web app). Kris does not edit these files by hand. He requests
new tools by description, an agent reads AGENTS.md, builds the file,
updates tools.json, and opens a pull request.

If you are a human and want to contribute, the path is the same: drive
an agent against AGENTS.md and let it follow the spec. The spec is strict
because visual coherence is the entire point of this collection.


Contributing
------------

Before opening a pull request:

  1.  Read AGENTS.md in full. It is the authoritative spec for layout,
      typography, color tokens, theme toggle, footer, file structure,
      and self-containment rules. Anything that violates it will be
      rejected.

  2.  Read tools.json. If a tool with the same purpose already exists,
      don't duplicate it. If it exists as a placeholder, promote it.
      Otherwise, append a new entry.

  3.  Build the tool inside tools/<slug>.html. Use word-counter.html or
      escape-entities.html as a structural reference. Inline every
      style and every script — no companion files, no external CSS
      beyond Google Fonts, no analytics, no telemetry.

  4.  Update tools.json. Add or promote the matching entry. Keep the
      keywords array generous (alt-spellings, synonyms, abbreviations)
      since the homepage filter searches against it. Bump the top-level
      "updated" date.

  5.  Test in your head. Open the file. Does the theme toggle work?
      Does the back link point to ../? Does the layout hold at 360px?
      Does the footer floor to the bottom of the viewport on a short
      page? Does the page work with JS disabled (it does not need to,
      but it should not crash)?

  6.  Open the pull request. One commit per tool. Imperative, lowercase
      title:

          add regex tester
          add hash (sha-256, sha-1, md5)
          fix word counter mobile layout
          link uuid tool from index

      The body, if needed, explains the design decision — not the diff.
      Do not add Claude/Codex co-author trailers.


What gets rejected
------------------

  -   Em dashes in prose (use period, semicolon, or parentheses).
  -   Cards, gradients, shadows, glow rings, or "modern" decoration.
      The repo is monochrome by default. If a tool genuinely needs a
      color signal, use the krisyotam pastel tokens (see AGENTS.md).
  -   Tailwind, React, Vue, Svelte, npm, bundlers, build steps.
  -   Hex or rgb values written directly. Use the HSL tokens.
  -   External scripts that aren't pinned to a specific version on
      jsdelivr (or equivalent). The five permitted heavy libraries
      and their versions are listed in AGENTS.md.
  -   Sending data to a server. Tools are client-side. A user pastes
      a secret into the hash tool; the secret never leaves the device.
  -   Decorative icons next to tool names in the homepage table.
  -   New top-level files that aren't a tool, index, README, AGENTS.md,
      CLAUDE.md, tools.json, or .gitignore.


Naming
------

Slugs are lowercase kebab-case and short. Examples:

    base64
    regex-tester
    json-yaml
    qr
    color-picker
    word-counter

The filename is <slug>.html. The URL is /tools/<slug>. There is no
.html in the URL.


Categories
----------

Every tool belongs to exactly one category, drawn from:

    text       manipulating, counting, comparing, transforming text
    encoding   base64, url, hashing, format conversion
    color      pickers, palettes, contrast, format conversion
    image      resizing, format conversion, ascii, qr
    math       calculators, unit conversion, statistics
    time       timestamps, durations, timezones, calendars
    dev        regex, json, yaml, diff, uuid, dns, jwt
    misc       anything that doesn't fit

If a tool fits two, pick the most specific. If it fits none, propose
misc and explain why in the commit message.


Deployment
----------

The repo is cloned to /mnt/storage/sites/tools/ on a personal server
and served by nginx at /tools/. A pull on that server is the only
deploy step. There is no CI, no build, no preview environment.


License
-------

Public domain. Use, copy, fork, host your own. No warranty.

If you fork: replace the footer link to point to your own copy. The
design language is mine; please don't ship a clone that pretends to
be the original krisyotam.com/tools.


Contact
-------

kris.yotam@protonmail.com — or open an issue on github.com/krisyotam/tools.
