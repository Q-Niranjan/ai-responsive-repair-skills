# Known-Good Fix Patterns

Approved, minimal fixes for common problems. Adapt to the project's styling system; do not paste blindly. Always follow the project's existing conventions.

Examples use Tailwind and JSX because they are compact. The same ideas apply in any framework: translate them into the project's own syntax (Vue or Angular templates, Svelte markup, plain CSS) and styling system.

## Flex child overflows because of long text
Cause: flex items default to `min-width: auto`, so they will not shrink below content width.
```tsx
// Before
<div className="flex items-center gap-2">
  <span>{name}</span>
  <Button>Edit</Button>
</div>
// After
<div className="flex items-center gap-2 min-w-0">
  <span className="min-w-0 truncate" title={name}>{name}</span>
  <Button className="shrink-0">Edit</Button>
</div>
```

## Grid with fixed columns
```tsx
// Before
<div className="grid grid-cols-4 gap-6">
// After (mobile-first)
<div className="grid grid-cols-1 gap-4 sm:grid-cols-2 lg:grid-cols-4 lg:gap-6">
```
For content-driven columns: `grid-cols-[repeat(auto-fit,minmax(min(100%,16rem),1fr))]`.

## Grid item stretched by long content
Use `minmax(0, 1fr)` (Tailwind `grid-cols-[minmax(0,1fr)]`) and `min-w-0` on the child.

## Truncated text must reveal full value
```tsx
<p className="truncate" title={label}>{label}</p>
```
Use the project's tooltip component instead of `title` if one exists. The hover value must be the same dynamic string that is shown.
For multi-line clamp: `line-clamp-2` plus `title={text}`.

## Long unbroken strings (URLs, IDs, emails)
`break-words` or `[overflow-wrap:anywhere]`, plus `min-w-0` if inside flex or grid.

## Table causing page-wide horizontal scroll
```tsx
<div className="w-full overflow-x-auto">
  <table className="min-w-full">…</table>
</div>
```
Keep the wrapper as the scroll container so the page itself does not scroll sideways. Consider `whitespace-nowrap` only on cells that need it.

## `100vw` / `100vh` problems
- `w-screen` / `100vw` → `w-full` (or `100%`) unless full-bleed is truly intended.
- `h-screen` / `100vh` → `min-h-dvh` / `100dvh` (fallback to `100vh` if old browsers matter).

## Fixed-width container
```tsx
// Before
<div className="w-[600px]">
// After
<div className="w-full max-w-[600px]">
```

## Header or toolbar that cannot fit
- Allow wrapping: `flex flex-wrap items-center gap-2`.
- Or collapse secondary actions into a menu below a breakpoint, keeping them reachable.
- Make the search input flexible: `min-w-0 flex-1`.

## Sidebar on small screens
- Hide the fixed sidebar below a breakpoint and provide a drawer or toggle that reaches the same links.
- Never hide navigation without an alternative.

## Images
```tsx
<img className="h-auto max-w-full" … />
```
`next/image`:
- Known size: set `width` and `height`, add `className="h-auto w-full"` and a correct `sizes`.
- Container-fill: parent must be `relative` with a defined size or `aspect-*`; use `fill` and `object-cover`.

## Iframes and video
```tsx
<div className="aspect-video w-full"><iframe className="h-full w-full" … /></div>
```

## Modal or dropdown clipped
- Modal: `max-h-[100dvh] overflow-y-auto` on the content, `w-[calc(100vw-2rem)] max-w-lg`.
- Dropdown inside an `overflow-hidden` parent: render in a portal, or remove the parent's clipping if it is not needed.

## Fixed header covering anchored content
Add `scroll-mt-*` equal to the header height on anchor targets, or `scroll-padding-top` on `html`.

## Fluid typography
`text-[clamp(1.5rem,4vw,2.5rem)]` or CSS `font-size: clamp(1.5rem, 4vw, 2.5rem)`. Keep a `rem` component so user font settings are respected.

## Touch targets
Ensure interactive elements are at least about 44 × 44 px on mobile (`min-h-11 min-w-11` or padding).

## Component that must adapt to its container, not the viewport
Use container queries when the same component sits in a narrow sidebar and a wide main area.
```tsx
<div className="@container">
  <div className="flex flex-col gap-3 @md:flex-row">…</div>
</div>
```
Plain CSS: `container-type: inline-size;` on the parent, then `@container (min-width: 28rem) { … }`. Tailwind v3 needs `@tailwindcss/container-queries`; skip this pattern if it is not installed.

## Hydration-safe responsive behavior
Replace `window.innerWidth` layout branching with CSS: `hidden md:block` / `md:hidden`. If JS is truly needed, run it in an effect after mount and give a sensible SSR default.

## Anti-patterns (do not use as a fix)
- `overflow-x: hidden` on `html`, `body`, or a page wrapper to hide overflow.
- Deleting content or controls to make a layout fit.
- Adding `!important` to beat a conflicting rule instead of fixing specificity.
- Scaling with `transform: scale()` or `zoom` to fit a small screen.
- Adding a new styling library or dependency.