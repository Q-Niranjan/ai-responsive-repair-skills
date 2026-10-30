# Report Templates

Use these formats so every run looks the same. Keep them concise. Use real file names and line numbers; never present example text as real results.

## A. Scan summary and repair plan

```
## Scan summary
Scope: <entire project | page: X | component: Y>
Stack: <router, styling, UI library, image/font handling>
Inspected: <files/areas>
Not inspected / uncertain: <what remains unknown>

## Repair plan

### Batch 1: <area, e.g. Navigation>  (Severity: High)
Shared components touched: <list, or "none">

**1.1 <short issue title>**  [High | Medium | Low]
- **Issue:** What appears wrong.
- **Evidence:** `path/File.tsx:42`, <relevant snippet or description>
- **Root cause:** Why it likely happens.
- **Proposed fix:**
  ```diff
  - <before>
  + <after>
  ```
- **Files affected:** `path/File.tsx`
- **Risk:** Effects on behavior, accessibility, or related layouts.
- **Confidence:** High | Medium | Low (code defect vs needs visual check)

### Batch 2: ...

## Needs manual verification (not code defects)
- <items that can only be confirmed visually>

I've completed the scan and prepared the repair plan. Please review it. Should I proceed with these changes?
```

Notes:
- Order batches by severity, highest first.
- If nothing meaningful is found, replace the plan with: what was inspected, what remains uncertain, and what the user could check manually.
- Do not ask additional questions in the same message beyond the approval question.

## B. Post-fix test report

```
Applied: <batch / finding names>

- **Issue:** What was addressed.
- **Root cause:** Why it occurred.
- **Files changed:** `path/File.tsx` (only files actually modified)
- **Fix applied:** What changed and why.
- **Checks run:** <lint / type check / tests, with results, or "not run">

### Please test
- **Where:** <page or component to open>
- **What to check:** <specific things to look at>
- **Expected result:** <what correct looks like>
- **Viewports:** <e.g. 390 × 844, 320 × 568, 768 × 1024, plus the one where you saw the issue>
- **Zoom:** also check at 200% browser zoom.

### Regression spot-check
- <shared component> is also used on: <pages / components>
- <other page that may be affected>

Is the issue resolved, or is anything still wrong?
```

Notes:
- Do not claim the visual result is verified.
- Then stop and wait for feedback.

## C. Follow-up plan (partial fix, still broken, or regression)

```
## Follow-up plan
Previous attempt: <what was changed and the user's feedback>
Revised diagnosis: <what was missed or why the first fix did not work>

<same finding format as A, limited to what remains>

Please review. Should I proceed with these changes?
```
Do not repeat a failed fix without a stated reason.
