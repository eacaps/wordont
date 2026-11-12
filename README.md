# wordont

A helper for Wordle-style puzzles. Type known letters into the five boxes (a blank box matches any letter). The word list is filtered and sorted: matching words come first, non-matching words are greyed out after them.

Each box accepts a single letter (A-Z); other characters are ignored. Matching is case-insensitive and finds the pattern anywhere in a word.

Built with Vue 3, Vite and TypeScript.

## Data

The word list is fetched at runtime from <https://eacaps.github.io/wordle-words/word-list.json> (`{ words: string[], lastUpdated: string }`).

## Scripts

| Script | Description |
| --- | --- |
| `npm install` | Install dependencies |
| `npm run dev` | Start the Vite dev server |
| `npm run build` | Type-check and build to `dist/` |
| `npm run preview` | Preview the production build |
| `npm run type-check` | Run `vue-tsc` |
| `npm run lint` | Run ESLint (check only) |
| `npm run lint:fix` | Run ESLint and apply fixes |
| `npm run format` | Format `src/` with Prettier |
| `npm run format:check` | Check `src/` formatting without writing |


## Deploy


The site is deployed to GitHub Pages automatically by `.github/workflows/deploy.yml` on every push to `main`. Pull requests are checked by `.github/workflows/ci.yml` first. Vite is configured with `base: "/wordont/"` in `vite.config.ts`, so the app is served from `/wordont/`.
