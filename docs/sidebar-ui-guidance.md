# Sidebar UI Improvement Guide

## Context and current-state observations

The project currently uses at least two different sidebar patterns:

- `index.html` uses an off-canvas sidebar with overlay, grouped sections, and a mobile toggle flow.
- `email-processing-suite.html` uses a fixed desktop sidebar that switches to slide-in on smaller screens.

Because these patterns are visually and behaviorally different, users can experience inconsistent navigation rules and styling when moving between tools.

## Primary goals

1. **Usability**: Improve wayfinding, reduce navigation friction, and preserve orientation.
2. **Accessibility**: Ensure robust keyboard, screen reader, and reduced-motion support.
3. **Visual consistency**: Standardize spacing, type, icon size, active states, and theming across all tools.
4. **Responsive reliability**: Keep the sidebar predictable on desktop, tablet, and mobile.

---

## 1) Information architecture and content model

### Recommendations

- Keep one canonical sidebar structure across all pages:
  - **Brand/header**
  - **Primary navigation groups** (Security, Processing, Utilities)
  - **Context actions** (theme switcher, account)
- Add a clear **current location indicator** (group + page).
- Avoid mixing link types in the same visual treatment. Distinguish:
  - navigation links,
  - actions/buttons,
  - settings controls.
- Keep labels concise and sentence-cased for scannability.

### Why it matters

A stable hierarchy helps users transfer learning across tools and minimizes cognitive load when switching pages.

---

## 2) Interaction behavior (open/close, state, motion)

### Recommendations

- On desktop (>= 1024px):
  - Keep sidebar persistently visible.
  - Optional: support a collapsed icon-only mode with tooltips.
- On tablet/mobile (< 1024px):
  - Use off-canvas drawer + overlay.
  - Close on overlay click, Escape, and successful link activation.
- Add **focus trapping** while the mobile drawer is open.
- Return focus to the menu trigger when the drawer closes.
- Respect `prefers-reduced-motion` by reducing animation distance/duration.

### Why it matters

Predictable behavior plus focus management prevents keyboard users from "falling behind" visual state changes.

---

## 3) Accessibility checklist

### Structural semantics

- Sidebar container should be a `<nav aria-label="Primary">`.
- If there is page-level sectioning, consider distinct labels (e.g., `aria-label="Security Tools"`).
- Use a real button for the menu trigger with:
  - `aria-controls="sidebar"`
  - `aria-expanded="true|false"`
  - `aria-label="Open navigation menu"`

### Active state and announcements

- Use `aria-current="page"` on the active link.
- Ensure active styling is not color-only:
  - include weight, indicator bar, icon fill, or background treatment.

### Keyboard support

- Tab order should be linear and predictable.
- Visible focus rings must meet contrast and remain visible in dark mode.
- Escape should always close open mobile drawer.

### Touch targets

- Minimum hit area: **44x44 px** for toggle and links.
- Maintain adequate spacing between targets to avoid accidental taps.

### Color and contrast

- Ensure text and icon contrast against backgrounds meets WCAG (AA at minimum).
- Validate hover, active, disabled, and focus states in both light and dark themes.

---

## 4) Visual system standardization

### Recommended design tokens

Create shared tokens and apply them in all HTML files:

- `--sidebar-width: 280px`
- `--sidebar-padding-x: 16px`
- `--sidebar-padding-y: 12px`
- `--sidebar-gap: 8px`
- `--sidebar-radius: 10px`
- `--sidebar-icon-size: 20px`
- `--sidebar-link-font-size: 14px`
- `--sidebar-link-weight: 500`
- `--sidebar-active-weight: 600`

### Component consistency rules

- Same icon size/alignment for every link.
- Same active indicator style across all tools.
- Same border/shadow depth for sidebar container.
- Same section label style (uppercase + letter spacing).
- Same motion timing for hover and open transitions.

---

## 5) Responsive layout strategy

### Breakpoint guidance

- **Desktop (>= 1024px)**: fixed/persistent sidebar.
- **Tablet (768–1023px)**: optional compact sidebar or overlay drawer.
- **Mobile (< 768px)**: full-height overlay drawer.

### Practical sizing

- Desktop width: 260–300 px.
- Mobile width: min(85vw, 320px).
- Avoid `100vw` drawers unless app content is secondary.

### Safe area support

- Add safe-area padding where needed:
  - `padding-bottom: env(safe-area-inset-bottom);`
  - `padding-top: env(safe-area-inset-top);`

---

## 6) Suggested implementation plan (phased)

### Phase 1 — quick wins

- Standardize active styles and add `aria-current`.
- Add robust menu button ARIA attributes.
- Add/verify visible focus rings for links and controls.
- Normalize link height to 44px minimum.

### Phase 2 — behavior hardening

- Add focus trap + return-focus behavior.
- Add reduced-motion CSS branch.
- Unify open/close logic across files into a shared script pattern.

### Phase 3 — visual unification

- Extract shared sidebar tokens/styles into a reusable stylesheet.
- Align iconography, spacing, and section title style across pages.
- Harmonize dark mode values and contrast pairs.

### Phase 4 — validation

- Keyboard-only walk-through on all primary pages.
- Screen reader smoke test (NVDA/VoiceOver).
- Responsive checks at key breakpoints (360, 768, 1024, 1280).
- Contrast validation for all sidebar states.

---

## 7) QA acceptance criteria

Use these as sign-off checks:

- User can identify current page from sidebar in under 1 second.
- Sidebar fully operable using keyboard only.
- No hidden focus, no focus loss when drawer closes.
- All touch targets meet 44x44 px minimum.
- Sidebar appearance and behavior feel equivalent across every tool page.
- Dark mode and light mode both pass contrast checks for nav text and controls.

---

## 8) Optional enhancements

- Add a searchable command palette fallback for large nav sets.
- Add pin/unpin preference for desktop collapse state.
- Persist last open section in localStorage for multi-section sidebars.
- Add subtle usage telemetry (open rate, misclick rate) for iterative UX tuning.

