---
name: accessibility
description: Korean accessibility standards reference (KWCAG 2.2 + MACAG 2025, based on WCAG 2.2 Level A+AA). Use this skill whenever building, modifying, or reviewing any UI component — forms, buttons, images, navigation, modals, media players, authentication flows, or any user-facing interface. Also use when auditing a page or screen for accessibility compliance, checking contrast ratios, verifying focus order, or reviewing ARIA usage. Triggers on any frontend/UI work including HTML, CSS, React, Vue, Angular, SwiftUI, Jetpack Compose, Flutter, or any framework that produces user-facing interfaces.
---

# Korean Accessibility Standards + WAI-ARIA

Reference covering KS X OT0003:2022 (web), KS X 3253:2025 (mobile), WAI-ARIA 1.2 roles/states/properties, and ARIA Authoring Practices Guide widget patterns. 59 Korean standard requirements + full ARIA reference.

## How to use

1. Identify what you're working on from the lookup table below
2. Read **only** the relevant reference file(s) — don't load everything
3. When **building**: follow the **Rule** column in each requirement table
4. When **reviewing**: use the **Test** column to verify compliance
5. For code snippets, read `references/patterns.md`

## Conventions

In the reference files:
- **Scope**: `W` = web only, `M` = mobile only, `WM` = both
- **Level**: `A` = basic (must have), `AA` = enhanced (should have)

## Quick Lookup

| Task | File to Load |
|------|-------------|
| Images, icons, alt text, CAPTCHA | `references/1-perceivable.md` |
| Video, audio, captions, transcripts | `references/1-perceivable.md` |
| Color, contrast, text sizing, spacing | `references/1-perceivable.md` |
| Semantic structure, reading order, orientation | `references/1-perceivable.md` |
| Keyboard/touch access, focus order, focus visible | `references/2-operable.md` |
| Touch target size, skip navigation | `references/2-operable.md` |
| Time limits, animation, auto-play control | `references/2-operable.md` |
| Flashing/seizure prevention | `references/2-operable.md` |
| Headings, link text, navigation methods | `references/2-operable.md` |
| Gestures, pointer input, drag alternatives | `references/2-operable.md` |
| Motion actuation, label-in-name | `references/2-operable.md` |
| Language declaration | `references/3-understandable.md` |
| Predictable behavior, consistent UI | `references/3-understandable.md` |
| Forms, labels, error messages, error prevention | `references/3-understandable.md` |
| Authentication, repeated input | `references/3-understandable.md` |
| HTML validity, ARIA roles, name/role/value | `references/4-robust.md` |
| Status messages, AT compatibility | `references/4-robust.md` |
| ARIA roles, states, properties reference | `references/aria-roles.md` |
| Widget patterns (tabs, dialogs, menus, etc.) | `references/aria-patterns.md` |
| Code patterns and examples | `references/patterns.md` |
| Map to original KS/WCAG standard IDs | `references/cross-reference.md` |

## When building UI

Before writing or modifying any user-facing component:

1. Look up the task above and read the relevant reference file
2. Apply every `WM` and platform-specific (`W` or `M`) requirement at your conformance level
3. For custom widgets (tabs, menus, dialogs, trees, etc.), read `references/aria-patterns.md` for the correct ARIA + keyboard contract
4. For choosing roles and attributes, read `references/aria-roles.md`
5. Check `references/patterns.md` for copy-paste code snippets
6. **Prefer native HTML over ARIA** — a `<button>` is always better than `<div role="button">`. Use ARIA only when no native element provides the semantics you need

## When reviewing UI

For each component or page under review:

1. Read all 4 principle files (they're small — ~1,000 tokens each)
2. Walk through every requirement's **Test** column
3. Flag failures with the requirement ID (e.g., "Fails 1.4.3: contrast ratio is 3.2:1, needs 4.5:1")
4. Use `references/cross-reference.md` to cite the original Korean standard ID if needed for compliance reports

## Key numbers to remember

- Text contrast: **4.5:1** minimum (3:1 for large text 18pt+)
- UI component contrast: **3:1** minimum
- Touch target: **24x24 CSS px** minimum
- Text resize: support up to **200%** without loss
- Flashing: never **3-50 Hz**
- Time extension warning: **20 seconds** before expiry
- Text spacing: line-height **1.5x**, paragraph **2x**, letter **0.12x**, word **0.16x**
