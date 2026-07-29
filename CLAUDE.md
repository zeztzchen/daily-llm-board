# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

See `AGENTS.md` for contributor conventions (2-space indent, kebab-case CSS classes, camelCase JS, manual test checklist, commit/PR style). This file covers architecture that isn't obvious from any single file.

## What this is

A single-page, dependency-free static web app: a daily checklist for reviewing LLM news/papers/blogs/models, plus a weekly notes editor. Deployed on GitHub Pages (`.nojekyll` disables Jekyll; live at zeztzchen.github.io/daily-llm-board). There is **no build step, package manifest, or test suite** — the three source files (`index.html`, `styles.css`, `README.md`) plus `favicon.svg` are served as-is.

## Run locally

Open `index.html` in a browser, or `python3 -m http.server 8000` for a local server (needed so the `fetch` to the poem API works without file:// restrictions).

## Architecture

All application logic lives in the inline `<script>` at the bottom of `index.html`. There is no framework and no module system — checklist items are declared as HTML and the script derives all state from the DOM.

**Two independent localStorage keys:**
- `daily-llm-board:v1` (`storageKey`) — checklist state + weekly notes (see state shape below).
- `daily-llm-board:poem:v1` (`poemStorageKey`) — the day's cached poem, so the jinrishici API is hit at most once per calendar day.

**Checklist state shape** under `storageKey`:
```
{
  "<data-key>": true,          // one flat boolean per checked source item
  "activeWeek": "YYYY-MM-DD",  // Monday-anchored week key currently in effect
  "weeks": {
    "YYYY-MM-DD": { "startDate": ..., "notes": { "YYYY-MM-DD": "<text>" } }
  }
}
```

**Weekly rollover is the core non-obvious behavior** (`ensureCurrentWeek`): weeks start Monday (`getDay() || 7`). On load, if `activeWeek` differs from the computed current week, all per-item checkbox booleans are **deleted** — i.e. a new week automatically resets the inspection checklist while preserving notes (notes are namespaced per week under `weeks`). There is also a one-time migration that moves a legacy top-level `state.notes` value into the current week's today entry.

**Source items are DOM-driven.** Each checklist item is a `.source-item` with:
- `data-key` — unique id, the localStorage boolean key (must be unique across the whole page).
- `data-group` — one of `news` / `paper` / `blog` / `model`, used to compute per-group counts.

Progress totals (`updateProgress`) and per-group badges (`[data-count-for]`) are computed by querying the DOM, not from a config list. The `0 / 11` in `#progressText` and the `0/N` badges are static placeholders that JS overwrites on load.

**Poem loading** (`loadPoem`) fetches `https://v1.jinrishici.com/all.json` with a 4s `AbortController` timeout and falls back to a hardcoded Lu You couplet on any failure.

## Adding or changing a source

1. Add a `.source-item` block in the correct `.source-group` in `index.html` with a **unique** `data-key` and the matching `data-group`. Copy an existing item's structure (checkbox + `.source-toggle` label + `.item-copy` + `.open-link`, all wired by `id`/`for`).
2. Update that group's `<span class="count" data-count-for="...">` total to match the new item count (cosmetic — JS recomputes it, but keep it honest).
3. Keep the source list in `README.md` in sync with the UI — they describe the same daily workflow.

New group icons live as `--icon-*` CSS variables in `:root` (inline SVG data URLs).
