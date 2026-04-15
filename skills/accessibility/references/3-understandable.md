# 3. Understandable (이해의 용이성)

Content and UI operation must be understandable regardless of user ability.

## 3.1 Readability (가독성)

| ID | Rule | Test | Scope | Level |
|----|------|------|-------|-------|
| 3.1.1 | Declare the primary language of the page/app so assistive tech selects correct voice/braille module | Web: html element has valid lang attribute. Mobile: app language setting matches content language | WM | A |
| 3.1.2 | Mark passages in a different language from the page default | Web: span/div with lang attribute on foreign-language text. Mobile: language metadata on mixed-language sections | WM | AA |

## 3.2 Predictability (예측 가능성)

| ID | Rule | Test | Scope | Level |
|----|------|------|-------|-------|
| 3.2.1 | No auto-execution of functions on focus. Receiving focus must not trigger: page navigation, form submission, new window/popup, or focus relocation. Exceptions: visual changes (highlight, border) and non-moving tooltips are OK | Tab to every control; no unintended actions fire on focus alone | WM | A |
| 3.2.2 | Selecting a value in a control (combo box, radio, checkbox) must not auto-submit or auto-navigate; require explicit submit button activation. Exceptions: visual-only changes (color inversion) are OK | Change dropdown/radio/checkbox values; verify no auto-submission or navigation | WM | A |
| 3.2.3 | New windows/popups must not open without user action; if they must, warn users in advance. Layer popups must receive focus first and return focus on close | Check for unexpected popups; verify popup focus management; close returns focus to trigger | WM | A |
| 3.2.4 | Repeated UI components appear in same position and use same appearance/name across pages | Check header, nav, footer across pages; same layout, labels, and order | WM | AA |
| 3.2.5 | Same-function components use identical visual design and naming | Find duplicate functions (e.g., search on multiple pages); verify same icon, label, behavior | WM | AA |
| 3.2.6 | Help information (contact details, FAQ, chatbot) appears in same relative order across all pages where provided | Navigate multiple pages; verify help links/buttons appear in consistent position | WM | A |

## 3.3 Input Assistance (입력 도움)

| ID | Rule | Test | Scope | Level |
|----|------|------|-------|-------|
| 3.3.1 | On input error: identify the field with the error and describe the error in text. Move focus to error or provide error summary. Does not apply to system/platform errors | Submit form with missing/invalid fields; verify error message names the field and explains the issue | WM | A |
| 3.3.2 | Every input has a visible label that is programmatically associated. Label clearly describes the input purpose | Check every input has a visible label; verify association (for/id, aria-labelledby, or wrapping label element) | WM | A |
| 3.3.3 | For important submissions (legal, financial, data deletion, test submission): provide cancel, edit/review, or confirmation step before final submission | Test checkout/delete/submit flows; verify at least one safeguard (undo, review, confirm dialog) | WM | AA |
| 3.3.4 | Do not require re-entry of previously provided information within a process. Auto-fill or let user select from prior input. Exception: security re-authentication | Check multi-step forms; verify previously entered data carries forward or is selectable | WM | A |
| 3.3.5 | Authentication must not rely solely on cognitive function tests (memorizing passwords, pattern recognition, math, image puzzles). Provide at least one non-cognitive alternative: saved credentials, OAuth, biometrics. Note: user's own name/email/phone are not cognitive tests | Test login flow; verify at least one non-cognitive auth method available | WM | AA |
