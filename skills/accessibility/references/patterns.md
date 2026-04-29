# Common Accessible Patterns

Reusable patterns for building accessible interfaces. Copy and adapt.

## Images

```
Informative:  <img src="chart.png" alt="Sales grew 40% in Q3 2025">
Decorative:   <img src="divider.png" alt="" role="presentation">
Complex:      <img src="org-chart.png" alt="Organization chart (details below)">
              <details><summary>Chart details</summary>...</details>
Icon button:  <button aria-label="Close dialog"><svg>...</svg></button>
```

## Forms

```html
<!-- Every input needs a visible, associated label -->
<label for="email">Email address</label>
<input id="email" type="email" autocomplete="email" required aria-describedby="email-err">
<span id="email-err" role="alert"></span>  <!-- populated on error -->

<!-- Group related inputs -->
<fieldset>
  <legend>Shipping address</legend>
  ...
</fieldset>

<!-- Required field: don't use color alone -->
<label for="name">Name <span aria-hidden="true">*</span></label>
<input id="name" required aria-required="true">
```

## Navigation

```html
<!-- Skip link: first focusable element -->
<a href="#main" class="skip-link">Skip to content</a>

<!-- Landmarks -->
<header role="banner">...</header>
<nav role="navigation" aria-label="Main">...</nav>
<main id="main" role="main">...</main>
<aside role="complementary">...</aside>
<footer role="contentinfo">...</footer>
```

## Headings

```
Use one h1 per page. Headings must not skip levels.
Correct:   h1 > h2 > h3 > h2 > h3
Incorrect: h1 > h3 (skipped h2)
```

## Focus Management

```javascript
// Modal: trap focus inside, restore on close
dialog.showModal();  // native dialog traps focus
dialog.addEventListener('close', () => triggerButton.focus());

// Dynamic content: move focus to new content
newSection.setAttribute('tabindex', '-1');
newSection.focus();
```

## Status Messages

```html
<!-- Announced without focus change -->
<div role="status" aria-live="polite">3 results found</div>
<div role="alert" aria-live="assertive">Session expires in 2 minutes</div>

<!-- Loading state -->
<div role="status" aria-live="polite">Loading...</div>
```

## Color and Contrast

```
Text contrast:     minimum 4.5:1 (normal text), 3:1 (18pt+ or 14pt bold+)
UI contrast:       minimum 3:1 for borders, icons, focus indicators
Never use color alone: add icon, pattern, underline, or text label
```

## Multimedia

```html
<!-- Video with captions -->
<video controls>
  <source src="video.mp4" type="video/mp4">
  <track kind="captions" src="captions.vtt" srclang="ko" label="Korean" default>
</video>

<!-- Audio with transcript -->
<audio controls src="podcast.mp3"></audio>
<details><summary>Transcript</summary>...</details>
```

## Mobile Touch Targets

```css
/* Minimum 24x24 CSS px; recommended 44x44 for comfortable tapping */
.button {
  min-width: 44px;
  min-height: 44px;
  padding: 10px;
}

/* Sufficient spacing between adjacent targets */
.icon-button + .icon-button {
  margin-left: 8px;
}
```

## Text Resizing

```css
/* Use relative units so text scales with user preferences */
body { font-size: 100%; }       /* respects browser default */
h1   { font-size: 2rem; }       /* scales with root */
p    { font-size: 1rem; }
line-height: 1.5;               /* minimum for readability */

/* Support reflow: no fixed-width containers */
.container { max-width: 100%; }
```

## Language Declaration

```html
<!-- Page language -->
<html lang="ko">

<!-- Inline language change -->
<p>이 문서는 <span lang="en">Web Content Accessibility Guidelines</span>를 기반으로 합니다.</p>
```
