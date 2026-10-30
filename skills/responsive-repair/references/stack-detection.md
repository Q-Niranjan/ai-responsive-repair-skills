# Stack Detection

Run this at the start of every scan so fixes follow the project's own conventions.

## 1. Framework and router type
Check `package.json` dependencies and config files first:
- UI framework: React, Vue, Svelte, Angular, Solid, Lit, or none (plain HTML/CSS).
- Meta-framework: Next.js, Remix, Nuxt (`nuxt.config.*`), SvelteKit (`svelte.config.*`), Angular (`angular.json`), Astro (`astro.config.*`), Vite (`vite.config.*`).
- If the stack is unfamiliar, say so, rely on core CSS fundamentals, and lower confidence on affected findings.

### Next.js
- `app/` directory with `layout.tsx` / `page.tsx` → **App Router** (server components by default; `"use client"` marks client components).
- `pages/` directory with `_app.tsx` / `_document.tsx` → **Pages Router**.
- Both present → mixed; check which one owns the affected route.
- No Next.js dependency in `package.json` → plain React (Vite, CRA, etc.).

## 2. Styling system (check `package.json`, config files, and imports)
| Signal | System | Convention to follow |
|---|---|---|
| `tailwind.config.*`, `@tailwind` / `@import "tailwindcss"` | Tailwind | Utility classes, mobile-first `sm: md: lg:`. Read the config for custom `screens` and `container`. |
| `*.module.css` / `*.module.scss` | CSS Modules | Edit the module, use media queries; keep class names local. |
| `styled-components`, `@emotion/*` | CSS-in-JS | Edit template styles; use the project's theme breakpoints. |
| `@mui/material` | MUI | Use `sx` responsive objects, `Grid`/`Stack`, `theme.breakpoints`. |
| `@chakra-ui/react` | Chakra | Responsive array/object props. |
| `shadcn/ui` (`components/ui/`, `components.json`) | shadcn + Tailwind | Edit local component files; they are owned by the project. |
| `antd` | Ant Design | Use `Grid`, `Row`/`Col` breakpoints props. |
| Global `.css` / `.scss` | Plain CSS | Use existing media query style. |
| `<style scoped>` in `.vue` files | Vue scoped styles | Edit the component's style block; use `:deep()` only when needed. |
| `<style>` in `.svelte` files | Svelte scoped styles | Edit the component's style block. |
| Component `styles` / `.component.scss` | Angular component styles | Respect `ViewEncapsulation`; use the project's SCSS breakpoint mixins. |
| `vuetify`, `element-plus`, `primevue`, `quasar` | Vue UI libraries | Use the library's responsive props and breakpoint helpers. |
| `@angular/material` | Angular Material | Use `BreakpointObserver` only when CSS cannot do it. |

Several can coexist. Match the one used by the file you are changing.

## 3. Breakpoints in use
- Tailwind: read `theme.screens` / `theme.extend.screens`, or v4 `@theme` block. Do not assume defaults if overridden.
- CSS: grep for `@media` and note the widths used.
- Use the project's own breakpoints. Do not introduce new ones without reason.

## 4. Images and fonts
- `next/image` vs plain `<img>`; note `sizes`, `fill`, and `priority` usage.
- `next/font` or `@font-face`; note fallbacks.

## 5. Other signals
- Layout wrappers: root `layout.tsx` or `_app.tsx`, shared `Container`, `PageShell`, `Sidebar`, `Header` components.
- Existing tooltip component (for truncation hover disclosure).
- Lint, type check, and test scripts in `package.json`.
- `browserslist` / Next.js version, to know whether `dvh`, container queries, or `:has()` are acceptable.

## Output of this step
Write a one-line summary before scanning, for example:
"Next.js 14 App Router, Tailwind (default screens), shadcn/ui, next/image, next/font (Inter)."