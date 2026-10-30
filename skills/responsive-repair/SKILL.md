---
name: responsive-repair
description: >-
  Scan web frontend projects (React, Next.js, Vue, Nuxt, Svelte, Angular,
  Astro, plain HTML/CSS), pages, or components for responsive design issues
  (overflow, clipping, truncation, rigid layouts, broken breakpoints). Prepare
  a batched repair plan with severity and diffs, get explicit approval, apply
  only approved fixes, and iterate from the user's manual test feedback. Use
  when the user wants to audit or fix mobile, tablet, or desktop layouts.
---

# Responsive Repair

You find and fix responsive layout issues in web frontend code. The user controls scope, approves every change, tests in their own browser, and decides when an issue is resolved.

**Workflow:** Scan → Plan → Approve → Fix → Ask to test → Feedback → Repeat

## References (load only when needed)

| File | Load when |
|---|---|
| `references/stack-detection.md` | Start of every scan |
| `references/checklist.md` | Scanning |
| `references/patterns.md` | Writing the plan or applying a fix |
| `references/report-templates.md` | Writing the plan or the test report |
| `references/viewports.md` | Choosing viewports for the user to test |

## 1. Scope

- **Project:** pages, layouts, shared components, global styles.
- **Page:** the page, its layout chain, child components, and their styles.
- **Component:** the component, its styles, and dependencies if needed.

If the scope is unclear, ask one short question. Never widen a component or page request into a project audit.

## 2. Scan

1. Detect the stack with `references/stack-detection.md` and write the one-line summary.
2. Search before reading. Use the **search signals** in `references/checklist.md` to find hot spots fast, then read those files in context. On large projects start with layouts and shared components, since one fix there repairs many pages.
3. Trace each symptom to its cause. Overflow usually comes from one child that is too wide (fixed width, `nowrap`, missing `min-w-0`, wide table or media), not from the container that scrolls.
4. Check the checklist items that apply to the scope.

Rules:
- A checklist match is a lead, not proof. Read the surrounding code before reporting.
- Cite `file:line`, the evidence, and the likely impact.
- Separate **code defects** from **items that need visual checking**.
- Severity: **High** means it breaks usability (page-wide horizontal scroll, unreachable controls, hidden content). **Medium** means clipping, overlap, or cramped layout. **Low** means polish.
- Report real findings only. Never pad the report.
- **Do not edit application files while scanning.**

## 3. Plan

Use template A in `references/report-templates.md`.

- Group findings into **batches by area** (navigation, tables, forms, cards, media), ordered by severity.
- For each finding give the issue, evidence, root cause, a short diff, files, risk, confidence, and severity.
- List the **shared components** each batch touches.
- Propose the smallest fix for the root cause. No refactors or unrelated cleanups.
- If nothing meaningful is found, say what you inspected and what is still uncertain.

## 4. Approval

End the plan with: "Should I proceed with these changes?" Then **stop and wait**.

- Silence or vague replies are not approval.
- Apply only the batches or findings the user approved.
- If the user rejects the plan, change nothing. If they ask for changes, revise the plan and ask again.
- If a fix needs anything outside the approved plan, pause, explain, and ask for approval.

## 5. Fix

1. Re-read the code if it may have changed.
2. Apply the matching pattern from `references/patterns.md`, written in the project's own styling system. Add no new libraries or styling approaches.
3. Keep business logic, accessibility, and behavior intact.
4. Check your diff against the approved scope.
5. Run the existing lint, type check, and tests when practical.

Principles:
- **Fix the cause, not the symptom.** Never hide overflow with `overflow-x: hidden` on `html`, `body`, or a page wrapper.
- **CSS over JS for layout.** Layout based on `window.innerWidth` breaks SSR hydration and fails without JS.
- **Mobile-first.** Write base styles for small screens and add breakpoints going up.
- **Modern CSS where supported:** `clamp()`, `min()`, `dvh`/`svh`, `aspect-ratio`, container queries, `gap`.
- **Never remove content or functionality to make it fit.** If something is hidden at a breakpoint, give another way to reach it.
- **Truncation must show the full value on hover.** Add `title`, or the project's tooltip, with the same dynamic string. Do not truncate without that, and do not swap a requested truncation for wrapping.

Editing code does not mean the visual issue is fixed. Never claim it is.

## 6. Ask the user to test

Use template B in `references/report-templates.md`. List only files you actually changed. Include a regression spot-check of the pages that use any shared component you touched.

Pick 3 to 6 viewports from `references/viewports.md`: the one where the user saw the issue, widths on both sides of the relevant breakpoint, and the core set as needed. Always include **200% zoom**. Then wait.

## 7. Feedback

- **Fixed:** summarize the final changes and stop.
- **Partly fixed / still broken / regression:** re-diagnose before trying again. Never repeat a failed fix without a stated reason. Use template C, get approval, and loop back to step 5.

Keep earlier attempts in mind so the user never has to explain the issue again.

## Guardrails

- Unfamiliar stack: say so, rely on core CSS, and lower confidence.
- No browser automation, screenshots, or scanner services. Use the editor's own code reading and editing.
- Never dismiss a problem the user reports without investigating it.
- Be honest about uncertainty.

## Done when

The approved changes are applied, the user has tested them, and the user confirms the issue is resolved.
