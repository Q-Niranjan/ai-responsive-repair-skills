# Viewport Reference

A manual testing guide for the user. It is not an automated test system. Do not claim the app was tested at any size unless the user tested it.

## Core set (default for most fixes)
| Viewport | Width × Height |
|---|---|
| Desktop | 1440 × 900 |
| Small laptop / large tablet landscape | 1024 × 768 |
| Tablet portrait | 768 × 1024 |
| Large mobile | 430 × 932 |
| Standard mobile | 390 × 844 |
| Compact mobile | 320 × 568 |

Always add:
- **The viewport where the user saw the problem.**
- **Nearby widths around the breakpoint** involved (for example 1 px either side of the breakpoint, such as 767 and 768 for `md`).
- **200% browser zoom** at a desktop width (equals roughly 720 CSS px wide on a 1440 screen). This catches text-size and reflow issues.

## Full matrix (whole-project audits, or when the core set is not enough)
| # | Viewport | Width | Height |
|---:|---|---:|---:|
| 1 | 4K desktop | 3840 | 2160 |
| 2 | QHD desktop | 2560 | 1440 |
| 3 | Full HD desktop | 1920 | 1080 |
| 4 | Large desktop | 1680 | 1050 |
| 5 | Desktop | 1440 | 900 |
| 6 | Standard laptop | 1366 | 768 |
| 7 | Small desktop / laptop | 1280 | 800 |
| 8 | Compact laptop | 1152 | 864 |
| 9 | Small laptop / tablet landscape | 1024 | 768 |
| 10 | Tablet landscape | 960 | 600 |
| 11 | Tablet portrait (large) | 834 | 1112 |
| 12 | Standard tablet | 768 | 1024 |
| 13 | Large mobile | 430 | 932 |
| 14 | Mobile | 414 | 896 |
| 15 | Standard mobile | 390 | 844 |
| 16 | Small mobile | 375 | 812 |
| 17 | Compact mobile | 320 | 568 |

## How to choose
- Prioritize: the reported viewport, then nearby breakpoint widths, then one larger viewport to check for regressions.
- Consider **height** as well as width for modals, sticky headers, and full-height layouts (for example 960 × 600 and 320 × 568 are short).
- Suggest 3 to 6 viewports per fix, not the full list, unless doing a whole-project audit.
- Viewport size alone does not reproduce real-device behavior, zoom level, font settings, or browser UI bars. Say so when relevant.

## Quick test instructions to give the user
1. Open browser DevTools → device toolbar, set the exact width × height (or resize the window).
2. Reload after changing size, since some code reads size on load.
3. Test portrait and landscape where the layout differs.
4. Test at 200% zoom.
5. Check loading, empty, error, and long-content states if the fix touches them.
