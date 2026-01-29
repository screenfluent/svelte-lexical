# Upgrade Lexical 0.33.1 -> 0.39.0: Test Fixes Tracking

## Status: IN PROGRESS

## Branch: `upgrade-lexical-0.39.0`
## Fork PR: https://github.com/screenfluent/svelte-lexical/pull/1
## Upstream PR: https://github.com/umaranis/svelte-lexical/pull/143

---

## Key Breaking Changes in Lexical (affecting HTML output)

### v0.35.0 - RTL/LTR CSS Classes REMOVED (HIGH IMPACT)

**This is the main cause of test failures!**

Before:
```html
<p class="PlaygroundEditorTheme__paragraph PlaygroundEditorTheme__ltr" dir="ltr">
```

After:
```html
<p class="PlaygroundEditorTheme__paragraph" dir="ltr">
```

Classes `PlaygroundEditorTheme__ltr` and `PlaygroundEditorTheme__rtl` are NO LONGER added to DOM.

**Fix strategy:** Remove these classes from all expected HTML in tests.

### v0.37.0 - ParagraphNode exports as div for block content

When paragraph contains block-level elements (figure, etc.), exports as:
```html
<div role="paragraph">...</div>
```
instead of `<p>...</p>`

### v0.37.0 - ImageNode uses figure/figcaption

Images with captions now use semantic HTML:
```html
<figure>
  <img src="..." alt="...">
  <figcaption>Caption</figcaption>
</figure>
```

### v0.39.0 - textFormat/textStyle serialization

Only serialized when useful (not for root/shadow nodes).

---

## Test Files to Update

Location: `demos/playground/src/__tests__/e2e/`

### Stats
- **879 assertHTML() calls** across all tests
- **552 assertSelection() calls**
- **53 spec files** total

### File Categories

1. **Core functionality** (31 files) - TextEntry, TextFormatting, Links, List, Tables, etc.
2. **Copy & Paste** (6 files) - CopyAndPaste/, HTML variants
3. **Headings** (3 files) - Headings/

---

## Fix Checklist

### Phase 1: Bulk Replace LTR/RTL Classes
- [ ] Remove `PlaygroundEditorTheme__ltr` from all expected HTML
- [ ] Remove `PlaygroundEditorTheme__rtl` from all expected HTML
- [ ] Verify pattern: ` PlaygroundEditorTheme__ltr` (with leading space)

### Phase 2: Run Tests & Identify Remaining Failures
- [ ] Run E2E tests on fork CI
- [ ] Categorize remaining failures by type
- [ ] Document new expected HTML patterns

### Phase 3: Fix Remaining Issues
- [ ] Image/figure related tests
- [ ] Table related tests
- [ ] Any structural changes

### Phase 4: Final Verification
- [ ] All E2E tests pass
- [ ] Build passes
- [ ] Update PR description with changelog

---

## Commands

### Run tests locally:
```bash
cd demos/playground
pnpm build-dev
pnpm preview --port 4000 &
pnpm test-e2e-chromium
```

### Run tests on fork CI:
Push to `upgrade-lexical-0.39.0` branch, workflows auto-run.

### Bulk replace in test files:
```bash
# Remove PlaygroundEditorTheme__ltr class
find demos/playground/src/__tests__ -name "*.mjs" -exec sed -i '' 's/ PlaygroundEditorTheme__ltr//g' {} \;

# Remove PlaygroundEditorTheme__rtl class
find demos/playground/src/__tests__ -name "*.mjs" -exec sed -i '' 's/ PlaygroundEditorTheme__rtl//g' {} \;
```

---

## Progress Log

### 2026-01-29
- [x] Created tracking document
- [x] Identified main breaking change (v0.35.0 LTR/RTL classes)
- [ ] Phase 1: Bulk replace in progress...

---

## Notes

- All expected values are INLINE in test files (no external snapshots)
- Tests use `prettifyHTML()` to normalize before comparison
- Can use `{ignoreClasses: true}` option if only classes changed
- 577 tests total, ~100+ failures related to LTR/RTL class removal
