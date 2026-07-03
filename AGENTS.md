# Repository Guidelines

## Project Structure & Module Organization

This repository is a small static web app for a daily LLM reading checklist.

- `index.html` contains the page markup and the inline JavaScript for checklist state, progress counts, reset behavior, and notes stored in `localStorage`.
- `styles.css` contains all layout, responsive styling, and visual states.
- `README.md` mirrors the daily source list and explains basic local preview.

There is no separate build output, package manifest, or assets directory at the moment. Keep new source links in sync between `README.md` and the checklist UI when they represent the same daily workflow.

## Build, Test, and Development Commands

- Open `index.html` directly in a browser for local preview.
- Optional local server: `python3 -m http.server 8000`, then visit `http://localhost:8000`.
- Check repository state with `git status --short` before and after changes.

No install, build, or package-manager commands are currently required.

## Coding Style & Naming Conventions

Use two-space indentation in HTML, CSS, and inline JavaScript. Prefer semantic HTML sections with clear `aria-label` or `aria-labelledby` attributes when adding UI areas. Use kebab-case CSS class names such as `source-group` and camelCase JavaScript identifiers such as `storageKey`.

Keep CSS variables in `:root` for shared colors and reuse existing spacing, border radius, and responsive patterns. Avoid introducing dependencies unless the static site clearly needs them.

## Testing Guidelines

There is no automated test suite. Verify changes manually in a browser:

- Checklist items toggle and update total and per-group counts.
- Notes persist after refresh.
- The reset button clears checked items and notes.
- Layout remains readable around `560px`, `900px`, and desktop widths.

For JavaScript changes, also test with malformed or empty `localStorage` data when relevant.

## Commit & Pull Request Guidelines

Recent commits use short, imperative summaries, for example `Build daily board web page` and `Add papers section and update X lists`. Follow that style and keep commits focused.

Pull requests should include a brief description, the user-facing impact, manual test notes, and screenshots for visual layout changes. Link related issues when available.
