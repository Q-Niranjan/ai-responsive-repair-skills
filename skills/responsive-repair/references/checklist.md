# Responsive Issue Checklist

Guidance for investigation, not proof of a defect. Check what applies to the scope and report meaningful findings only.

## Search signals (grep these first)

| Pattern | Likely problem |
|---|---|
| `w-\[\d+px\]`, `min-w-\[`, `width:\s*\d{3,}px`, `min-width:\s*\d{3,}px` | Fixed or minimum width wider than mobile |
| `100vw`, `w-screen` | Horizontal overflow from scrollbar width |
| `100vh`, `h-screen` | Mobile browser bar cut-off (use `dvh`) |
| `whitespace-nowrap`, `white-space:\s*nowrap`, `flex-nowrap`, `shrink-0` | Content that cannot wrap or shrink |
| `grid-cols-[3-9]`, `grid-template-columns` without media query | Rigid grid |
| `truncate`, `line-clamp`, `text-overflow` | Truncation missing hover disclosure |
| `overflow-x-hidden`, `overflow:\s*hidden` | Masked overflow or clipped popovers |
| `absolute`, `fixed`, `position:\s*(absolute\|fixed)` | Positioning that breaks on resize |
| `<table`, `mat-table`, `DataGrid` | Table forcing page scroll |
| `<iframe`, `<video`, `<img`, `next/image` | Unconstrained media |
| `innerWidth`, `matchMedia`, `useMediaQuery`, `BreakpointObserver` | JS layout, hydration risk |
| `max-width:` mixed with `min-width:` in `@media`, `max-md:` | Conflicting breakpoint direction |

## 1. Layout and dimensions
- Fixed widths or heights that constrain content (`w-[480px]`, `width: 600px`, `h-64` on text containers).
- Minimum widths larger than the available space.
- Large fixed margins, padding, or gaps that do not scale down.
- Containers that do not adapt to parent width.
- `100vw` causing overflow (it includes the scrollbar width).
- `100vh` on mobile where browser bars change the viewport (prefer `dvh` / `svh`).

## 2. Flexbox and Grid
- Flex items that cannot shrink or wrap (`flex-nowrap`, `shrink-0` misuse).
- Missing `min-w-0` / `min-width: 0` on flex or grid children holding long text.
- Fixed grid columns (`grid-cols-4`) with no smaller-screen variant.
- Grid items stretched by long content; prefer `minmax(0, 1fr)`.
- Nested layouts exceeding their parent.

## 3. Text and content
- Unexpected truncation (`truncate`, `text-overflow`, `line-clamp`).
- `whitespace-nowrap` blocking needed wrapping.
- Text clipped by `overflow: hidden` or fixed heights.
- Long labels, headings, IDs, URLs, emails, or dynamic content breaking layout (check `break-words`, `overflow-wrap: anywhere`).
- Text overlapping buttons, icons, or neighbors.
- Truncated text with no hover disclosure (no `title` or tooltip).

## 4. Overflow and positioning
- Elements extending beyond viewport or parent.
- Negative margins or transforms pushing content off-screen.
- Absolute positioning that breaks when content or viewport changes.
- Fixed or sticky headers, footers, sidebars covering content (check scroll offsets, `scroll-mt-*`).
- Dropdowns, menus, dialogs, popovers clipped by a parent's `overflow` or positioned off-screen.
- `overflow-x: hidden` masking an underlying problem.

## 5. Breakpoints
- Desktop layout staying rigid at narrow widths.
- Missing or inappropriate breakpoint styles.
- Base styles conflicting with responsive overrides.
- Tailwind: desktop-first thinking in a mobile-first system, `max-*` and `min-*` variants mixed inconsistently, arbitrary breakpoints that disagree with the config.
- Layouts failing right at breakpoint transitions (for example 767 to 768, 1023 to 1024).

## 6. Navigation and page structure
- Nav items overflowing or overlapping.
- Header with competing logo, search, and actions and no collapse strategy.
- Sidebars with fixed width and no mobile behavior (drawer, collapse).
- Excess horizontal padding on small screens.
- Search bars and toolbars that cannot fit or wrap.

## 7. Components and forms
- Cards with fixed width or height.
- Forms stuck in multi-column layouts.
- Inputs, selects, or buttons wider than the container.
- Validation messages or labels clipped.
- Tables forcing page-wide horizontal scroll (should scroll inside a wrapper, or reflow).
- Pagination, action buttons, or table controls overflowing.
- Touch targets too small on mobile (under roughly 44 × 44 px).

## 8. Images and media
- Images wider than the container (missing `max-w-full h-auto`).
- Fixed dimensions causing distortion or clipping.
- Videos, iframes, maps, or embeds wider than available space (missing aspect-ratio wrapper).
- Unsuitable `object-fit` or aspect-ratio constraints.

## 9. Typography and accessibility
- Heading or text sizes that break narrow layouts (consider `clamp()`).
- Fixed `line-height` clipping wrapped text.
- Controls hard to read or operate when small.
- Layout breaking under **200% zoom** or larger system font.
- Responsive changes hiding essential content or functionality.

## 10. Dynamic states and style conflicts
- Loading, empty, error, and validation states breaking layout.
- Dynamic data lengths (long names, large numbers, many items) causing overflow.
- Conflicting CSS rules overriding responsive styles (specificity, `!important`, global styles).
- Conditional React classes that drop responsive behavior in some states.
- Shared component styles that affect other pages.

## 11. Framework-specific

Apply only the group that matches the detected stack.

### React and Next.js
- `next/image`:
  - fixed `width`/`height` that exceed small containers;
  - `fill` without a positioned parent that has a size;
  - missing or wrong `sizes`, causing oversized downloads or blurry images.
- `app/` or `pages/` layouts (`layout.tsx`, `_app`, `_document`) that impose widths, paddings, or `overflow` on all children.
- `next/font` or custom fonts with large fallback differences causing layout shift or wrapping changes.
- Client-side code using `window.innerWidth` / `matchMedia` for layout, which risks hydration mismatch and flashes of wrong layout.
- Portals and modals (`createPortal`, dialog libraries) not constrained to the viewport (`max-h-[100dvh]`, internal scroll).
- Third-party UI components (MUI, Chakra, shadcn/ui, Ant Design) with fixed sizes or props like `width`, `minWidth`, `fullWidth` missing.
- Suspense / skeleton fallbacks with different dimensions from the loaded content.

### Vue and Nuxt
- `<style scoped>` rules that do not reach child components (need `:deep()`), or global styles overriding scoped ones.
- Fixed sizes on UI library components (Vuetify, Element Plus, PrimeVue, Quasar) and missing responsive props or breakpoint helpers.
- `window.innerWidth` or `useMediaQuery` branching for layout instead of CSS; in Nuxt this risks hydration mismatch.
- `<NuxtImg>` / `<img>` without constrained width or `sizes`.
- `v-if` / `v-show` hiding content at one breakpoint with no equivalent access.

### Svelte and SvelteKit
- Component-scoped `<style>` blocks with fixed widths or missing media queries.
- `svelte:window bind:innerWidth` used for layout, which risks hydration flashes under SSR.
- Layout files (`+layout.svelte`) imposing widths, padding, or `overflow` on all routes.
- `{#if}` blocks removing content at small sizes with no alternative.

### Angular
- Component styles (`styles` / `.scss`) with fixed widths; `ViewEncapsulation` hiding or isolating responsive rules.
- Angular Material or CDK components with fixed sizes; `BreakpointObserver` used where CSS would do.
- `*ngIf` / `@if` removing content at one breakpoint with no equivalent access.
- `mat-table` or custom tables without a horizontally scrollable wrapper.

### Astro
- Layouts (`src/layouts`) imposing widths or `overflow` on every page.
- `<Image>` / `<img>` with fixed `width` / `height` and no responsive `widths` / `sizes`.
- Islands (React, Vue, Svelte components) with fixed sizes inside flexible Astro layouts.

### Plain HTML and CSS
- Missing `<meta name="viewport" content="width=device-width, initial-scale=1">`.
- Layout built with fixed-pixel floats, tables, or absolute positioning.
- Media queries that are desktop-first (`max-width`) mixed with mobile-first (`min-width`) rules.
- Inline styles with fixed sizes that override stylesheet rules.