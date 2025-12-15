**Purpose**: Provide compact, actionable guidance so an AI coding agent can be immediately productive in this Svelte + Vite codebase.

- **Repo type**: SvelteKit app (Svelte v5) using Vite. Entry routes live in `src/routes/`. Reusable UI and SVG pattern components are in `src/lib/components/`.

**Dev / Build commands**
- Start dev server: `npm run dev` (uses Vite).
- Create production build: `npm run build`.
- Preview production build: `npm run preview`.
- Format/check: `npm run format` / `npm run lint` (Prettier + `prettier-plugin-svelte`).

**Big picture architecture**
- Routing: `src/routes/+page.svelte` and `+layout.svelte` are the app shell. `+page.svelte` imports the pattern component used in the page.
- Components: visually important files live in `src/lib/components/` (examples: `1_PatternRect.svelte`, `2_PatternRectStruct.svelte`, `3_Meinpattern.svelte`, `Header.svelte`). These components render SVGs using a consistent `viewBox="0 0 1000 1000"` coordinate system.
- Assets/static: static files under `static/` and `src/lib/assets/` (favicon imported in `+layout.svelte`).

**Project-specific patterns & conventions**
- Grid/pattern coordinate system: components use a fixed 1000x1000 viewBox and compute cell size with `const squareSize = 1000 / squareCount;`. Follow this convention when adding pattern logic so visual spacing remains consistent.
- Inline coordinate math in `<script>`: position/point arrays and helper functions are defined in component scripts (e.g. `calculatePosition`, `rotatePoint`) and then injected into SVG `transform` or `points` attributes.
- SVG transforms: use SVG transform syntax (not CSS) and try to avoid giving a pivot when rotating. Better use translate to rotate around a specific point.
Avoid invalid constructs like `transform="translate rotate(45)"` — Vite/Svelte will not throw, but the transform will be ignored by the browser.
- Use template string `points` for `<polygon>` coordinates when generating shapes programmatically. Keep point arrays in script for easier math and tests.
- Prefer adding `stroke` or visible contrast when debugging geometry (several components use `fill` and sometimes `stroke` to make edges visible).
- Always give comments to clarify non-trivial math or coordinate logic.

**Common file targets for changes**
- `src/lib/components/3_Meinpattern.svelte` — good example of computing points, rotating and translating to align min X/Y to origin.
- `src/lib/components/1_PatternRect.svelte` — shows `#each` loops producing grid of `<rect>` with `transform="translate(x y)"`.
- `src/routes/+page.svelte` — where to swap which component is rendered for UI testing.

**Developer workflows & tips for AI**
- When changing SVG math, run `npm run dev` and visually confirm in browser; unit tests are not present in this repo.
- Use `npm run format` after edits; repo relies on Prettier (with Svelte plugin) for formatting.
- When adding exports for reuse, put them in `src/lib/index.js` (project already contains this file as the `$lib` index).
- If you add new components intended for the page, update `src/routes/+page.svelte` (it imports `Header` and the pattern component).

**Integration points & external deps**
- Primary deps are Svelte (`svelte`), SvelteKit (`@sveltejs/kit`), and Vite. No backend services or APIs are present.

**What the AI should NOT assume**
- There are no tests; do not create changes that rely on an existing test harness.
- There is no CI config present; do not modify CI expectations unless asked.

**Short examples to copy/use**
- Grid cell size calculation:
  - `const squareCount = 20; const squareSize = 1000 / squareCount;`
- Positioning a rect in the grid:
  - `transform={`translate(${xi * squareSize} ${yi * squareSize})`}`
- Rotate + translate polygon to origin (example):
  - `transform={ `rotate(${angle} ${cx} ${cy}) translate(${-minX} ${-minY})` }`

If anything here is unclear or you want the instructions to prefer different conventions (e.g. CSS transforms, different coordinate system), tell me which parts to change and I'll update the file.