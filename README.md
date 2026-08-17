# Exobchix Apps

## Overview

`exobchix-apps` is a web app built with Astro and Vue.

- Main page entry: `src/pages/index.astro`
- UI composition: `index.astro` renders `src/components/Apps.astro`
- Interactive feature: `Apps.astro` mounts `src/components/TeamGenerator.vue`

## Stack

- Language/runtime: JavaScript (ES modules) with TypeScript config (`tsconfig.json`)
- Framework: Astro (`astro`)
- UI framework: Vue 3 (`vue`) via `@astrojs/vue`
- Styling: Tailwind CSS via `@astrojs/tailwind`
- Package manager: npm (`package-lock.json` present)

## Requirements

- Node.js (TODO: specify exact supported version)
- npm

## Setup and Run

Install dependencies:

```sh
npm install
```

Start development server:

```sh
npm run dev
```

Build production output:

```sh
npm run build
```

Preview production build locally:

```sh
npm run preview
```

Run Astro CLI commands:

```sh
npm run astro -- --help
```

## Scripts

Defined in `package.json`:

| Script | Command | Description |
| :-- | :-- | :-- |
| `dev` | `astro dev` | Start local development server |
| `build` | `astro build` | Build project into `dist/` |
| `preview` | `astro preview` | Preview built app locally |
| `astro` | `astro` | Run Astro CLI directly |

## Environment Variables

No runtime environment variables were found in the current source/config files (`import.meta.env` and `process.env` usage not detected).

- TODO: add documented env vars here if/when introduced.

## Tests

No test scripts are currently defined in `package.json`.

- TODO: add testing setup (framework + commands) and document how to run tests.

## Project Structure

```text
.
├── astro.config.mjs
├── package.json
├── package-lock.json
├── tailwind.config.mjs
├── tsconfig.json
├── public/
├── src/
│   ├── components/
│   │   ├── Apps.astro
│   │   ├── TeamGenerator.vue
│   │   └── Welcome.astro
│   ├── layouts/
│   │   └── Layout.astro
│   └── pages/
│       └── index.astro
└── README.md
```

## License

- TODO: license is not declared in `package.json` and no `LICENSE` file was found. Add the project license here.
# exobchix-apps
