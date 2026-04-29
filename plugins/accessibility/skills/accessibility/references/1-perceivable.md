# 1. Perceivable (인식의 용이성)

All content must be presentable in ways users can perceive through sight, hearing, or touch.

## 1.1 Text Alternatives (대체 텍스트)

| ID | Rule | Test | Scope | Level |
|----|------|------|-------|-------|
| 1.1.1 | Provide text alternatives for all non-text content that conveys meaning | Every img/icon has descriptive alt; decorative items use alt="" or role="presentation" | WM | A |
| 1.1.2 | CAPTCHA must provide alternatives for different sensory abilities | CAPTCHA offers audio alternative or other non-visual method | WM | A |

## 1.2 Multimedia Alternatives (멀티미디어 대체수단)

| ID | Rule | Test | Scope | Level |
|----|------|------|-------|-------|
| 1.2.1 | Provide synchronized captions for all pre-recorded audio in video | Every video with speech has accurate synced captions | WM | A |
| 1.2.2 | Provide transcript or audio description for pre-recorded video content | Video-only content has text transcript or audio description | WM | A |
| 1.2.3 | Provide sign language interpretation for pre-recorded multimedia | Sign language video accompanies multimedia content | WM | AA |
| 1.2.4 | Live multimedia must have real-time captions | Live streams provide real-time captioning | WM | AA |

## 1.3 Adaptable Content (적응성)

| ID | Rule | Test | Scope | Level |
|----|------|------|-------|-------|
| 1.3.1 | Use semantic markup so structure is programmatically determined | Headings use heading elements; lists use list elements; tables have headers | W | A |
| 1.3.2 | Ensure meaningful reading/focus order matches visual layout | Disable CSS/remove styles; content order still logical | WM | A |
| 1.3.3 | Structure data tables with proper headers and relationships | Every data table has th elements; complex tables use scope/headers attributes | W | A |
| 1.3.4 | Do not restrict display orientation (portrait/landscape) unless essential | App works in both orientations; no forced lock unless essential (e.g., piano app) | M | AA |
| 1.3.5 | Identify input purpose for user data fields (name, email, phone, address) | Autocomplete attributes present on personal data fields | WM | AA |

## 1.4 Distinguishable (명료성)

| ID | Rule | Test | Scope | Level |
|----|------|------|-------|-------|
| 1.4.1 | Do not auto-play audio; if unavoidable, provide stop/pause/mute within 3 seconds | No audio plays on load, or controls appear within 3s | WM | A |
| 1.4.2 | Do not convey information by color alone; use shape, pattern, or text too | Remove color: all info still distinguishable | WM | A |
| 1.4.3 | Text contrast ratio at least 4.5:1 against background (3:1 for large text 18pt+/14pt bold+) | Measure contrast with tool; all text meets threshold | WM | AA |
| 1.4.4 | UI component and graphical object contrast at least 3:1 against adjacent colors | Buttons, icons, form borders meet 3:1 against background | WM | AA |
| 1.4.5 | Ensure adjacent interactive elements are visually distinguishable from each other | Neighboring controls have visible boundaries or sufficient spacing | WM | A |
| 1.4.6 | Support text resize up to 200% without loss of content or function | Zoom to 200%; no content clipped, overlapping, or hidden | WM | AA |
| 1.4.7 | Do not use images of text; use real text (except logos, signatures, diagrams) | No meaningful text rendered as image; text is selectable/scalable | WM | AA |
| 1.4.8 | Content reflows at 320px width (mobile) / 400% zoom without horizontal scroll | At 320px viewport or 400% zoom, no 2D scrolling needed (except maps, diagrams, video) | WM | AA |
| 1.4.9 | Text spacing adjustable without content/function loss: line-height 1.5x, paragraph spacing 2x, letter-spacing 0.12x, word-spacing 0.16x of font size | Apply specified spacing values; no content clipped or overlapping | WM | AA |
