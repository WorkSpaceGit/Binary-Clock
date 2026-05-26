# ∞ Binary Clock

A minimal binary clock for iPhone. One file. No dependencies. No frameworks.

---

## Philosophy

The goal was not to build a clock app — it was to build the *least possible* clock that still works beautifully. Every line of code either earns its place or gets cut.

Dark background. Green bits. Nothing else.

---

## What's inside

A single `index.html` that does everything:

- renders a BCD binary clock (hours / minutes / seconds)
- installs as a PWA via inline manifest generated from JS
- registers a Service Worker via Blob URL — no extra files
- generates the `∞` icon at runtime via Canvas, reused for favicon, apple-touch-icon and manifest
- requests Screen Wake Lock so the display stays on
- respects iPhone notch and home indicator via `safe-area-inset` padding

---

## Performance decisions

Every choice was made with mobile CPU and battery in mind.

**Bit-level math instead of string parsing**
Each digit is decoded with a bitwise shift `(digit >> (len - 1 - j)) & 1` — zero string allocations per tick.

**Diff rendering**
`bitState[]` tracks the previous state of every bit. The DOM is only touched when a bit actually changes — not on every second unconditionally.

**`border` instead of `box-shadow` for active bits**
`box-shadow` triggers layout recalculation and full repaint. A `border-color` swap is pure composite — the cheapest possible visual change on mobile GPU.

**No transitions**
Switching is instant. Digital signal aesthetics, zero animation cost.

**`requestAnimationFrame` for cold start**
The clock waits for the browser to finish its first layout pass before rendering — no wasted recalculation on load.

**Synchronized `setTimeout`**
`1000 - (Date.now() % 1000)` aligns each tick to the wall clock. No drift, no redundant calls.

**CSS `contain: paint`**
Each bit is isolated — a repaint on one bit doesn't cascade to neighboring elements.

**Safari battery behavior respected**
When the app is backgrounded, Safari freezes timers. This is intentional system behavior and left untouched — the battery matters more than a background tick.

---

## Installation on iPhone

1. Host the file anywhere (GitHub Pages, local server, etc.)
2. Open in Safari
3. Share → Add to Home Screen

Wake Lock and PWA features are fully active only when launched from the home screen.

---

## Size

`~5 KB`. No build step. No node_modules. Open and done.
