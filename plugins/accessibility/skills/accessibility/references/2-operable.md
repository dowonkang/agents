# 2. Operable (운용의 용이성)

All UI components and navigation must be operable by all users regardless of input method.

## 2.1 Input Device Accessibility (입력장치 접근성)

| ID | Rule | Test | Scope | Level |
|----|------|------|-------|-------|
| 2.1.1 | All functionality accessible via keyboard alone | Tab through every interactive element; activate with Enter/Space; no keyboard traps | W | A |
| 2.1.2 | Focus moves in logical order (left-to-right, top-to-bottom) matching visual layout | Tab through page; focus order follows reading order; no jumps to hidden/irrelevant elements | WM | A |
| 2.1.3 | Focused element has visible focus indicator at all times | Tab through page; every focused element shows visible outline/border/highlight | WM | AA |
| 2.1.4 | User-initiated content (popups, modals) must not obscure currently focused element | Open modals/tooltips; check they don't cover the trigger or trap focus unexpectedly | WM | A |
| 2.1.5 | Touch target size at least 24x24 CSS pixels (exceptions: inline links, OS default controls, map pins) | Measure interactive elements; all meet 24x24px minimum | M | AA |
| 2.1.6 | Controls without visible borders must have sufficient center-to-center spacing | Measure spacing between adjacent controls; adequate for finger operation | M | AA |
| 2.1.7 | Single-character keyboard shortcuts must be disableable or remappable to avoid voice-input false triggers | Find single-key shortcuts; verify they can be turned off or rebound | WM | A |
| 2.1.8 | All content accessible via skip-navigation link as first focusable element | First Tab stop is "Skip to content" link; activating it moves focus to main content | W | A |

## 2.2 Sufficient Time (충분한 시간 제공)

| ID | Rule | Test | Scope | Level |
|----|------|------|-------|-------|
| 2.2.1 | Time-limited content must allow users to turn off, adjust (10x), or extend (10+ times with 20s+ warning) time limits. Exceptions: real-time events (auctions), sessions 20h+ | Find timed features; verify extension/disable mechanism; warning appears 20s+ before expiry | WM | A |
| 2.2.2 | Auto-moving/scrolling/blinking content must have pause/stop controls | Find auto-scrolling banners, carousels; verify pause/stop/forward/back controls exist | WM | A |

## 2.3 Seizure Prevention (광과민성 발작 예방)

| ID | Rule | Test | Scope | Level |
|----|------|------|-------|-------|
| 2.3.1 | No content flashes more than 3 times per second (3-50Hz range). If unavoidable, limit to under 3 seconds and provide stop control | Check animations/videos for flash rate; verify no 3-50Hz flashing persists beyond 3s | WM | A |

## 2.4 Navigation (쉬운 내비게이션)

| ID | Rule | Test | Scope | Level |
|----|------|------|-------|-------|
| 2.4.1 | Provide appropriate headings for content blocks; headings must describe the content they precede | Every content section has a heading; heading text matches content; no empty headings | WM | A |
| 2.4.2 | Link text (or link + surrounding context) clearly describes link purpose/destination; avoid "click here" | Read each link text; purpose is clear without reading surrounding paragraph | WM | A |
| 2.4.3 | Provide multiple navigation methods (e.g., menu + search + sitemap). Exception: content that is a step in a process | At least 2 ways to reach any page (e.g., nav menu + search, or nav + sitemap) | WM | AA |
| 2.4.4 | Page/screen title accurately describes topic or purpose | Check page title/screen header; it describes the content uniquely | WM | A |

## 2.5 Input Modalities (입력 방식)

| ID | Rule | Test | Scope | Level |
|----|------|------|-------|-------|
| 2.5.1 | Multi-point gestures (pinch, rotate, two-finger tap) and path-based gestures (swipe, draw) must have single-pointer alternative. Dragging actions must also have single-tap alternative. Exceptions: essential gestures (signature), OS-level gestures | Find multi-touch/swipe/drag features; verify single-tap alternative exists for each | WM | A |
| 2.5.2 | Functions triggered by single pointer: no activation on down-event alone; must complete on up-event with ability to abort; or down-event action reversible on up-event. Exception: essential (piano key, game trigger) | Click/tap controls; verify action fires on release, not press; test aborting by dragging away | WM | A |
| 2.5.3 | Visible label text of UI components must be included in the accessible name (in same string order) | Compare visible button/link text to accessible name (aria-label, name property); visible text is substring | WM | A |
| 2.5.4 | Motion-activated functions (shake to undo, gesture photo) must also be operable via UI controls and must be disableable. Exceptions: accessibility interfaces (eye tracking), essential motion (pedometer) | Find motion-triggered features; verify UI control alternative exists; verify disable option | WM | A |
