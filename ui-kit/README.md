# Liquid Glass UI Kit

The visual "sauce" from **Nudge**, packaged so you can drop it into any project:
frosted-glass surfaces with a chromatic refracting rim, springy **drag / fling**
physics, hover **3D-tilt**, **press-dip**, gentle **idle float**, and a
**day / night theme** that persists.

No build step. No dependencies. Two files (~plus an optional font).

> The heavy WebGL water from the original is intentionally **not** included.
> The backdrop here is a lightweight CSS gradient.

## Files

| File | What it is |
|---|---|
| `liquid-glass.css` | Design tokens + all components (card, glass, button, field, theme toggle, float). |
| `liquid-glass.js`  | The physics (`makeInteractive`, `makeFloat`) + theme manager + auto-init. |
| `demo.html`        | Live showcase that doubles as copy-paste documentation. |
| `AveriaSansLibre-Bold.ttf` | (Optional, in the repo root) the display font Nudge uses. |

Open `demo.html` in a browser to see everything working.

## Quick start

```html
<!-- 1) Avoid a flash of the wrong theme: resolve it before first paint -->
<script>
  (function () {
    try {
      var s = localStorage.getItem('glasskit-theme');
      var dark = s ? s === 'dark' : matchMedia('(prefers-color-scheme: dark)').matches;
      document.documentElement.setAttribute('data-theme', dark ? 'dark' : 'light');
    } catch (e) {}
  })();
</script>

<!-- 2) The kit -->
<link rel="stylesheet" href="liquid-glass.css" />

<body class="glass-bg">
  <div class="glass-wash"></div>

  <button class="theme-toggle glass" data-theme-toggle aria-label="Toggle theme">🌙</button>

  <button class="btn glass" data-glass data-float>Press me</button>

  <script src="liquid-glass.js"></script>
  <script>GlassKit.init();</script>
</body>
```

## Declarative hooks

`GlassKit.init()` scans the DOM and wires these attributes:

| Attribute | Effect |
|---|---|
| `data-glass` | Interactive physics — hover tilt, drag-to-fling with spring-back, press-dip. A real drag cancels the follow-up click, so `onclick` only fires on a true tap. |
| `data-float` | Gentle, randomised idle drift (no two elements sync up). |
| `data-theme-toggle` | Click flips light ↔ dark and saves the choice to `localStorage`. |

Call `GlassKit.init(rootEl)` again after injecting new DOM (e.g. in a SPA);
elements are guarded against double-binding.

## CSS components

| Class | Use |
|---|---|
| `.glass-bg` | Put on `<body>` for the themed gradient backdrop + base font/colour. |
| `.glass-wash` | A soft brightening wash — a fixed, full-screen `<div>`. |
| `.card` | Large frosted container with top gloss. |
| `.glass` | Interactive liquid-glass surface (pair with `data-glass`). |
| `.glass.r-sm` / `.r-md` / `.r-lg` / `.r-pill` | Corner-radius helpers for glass tiles. |
| `.btn`, `.btn-ghost`, `.btn-row` | Buttons (touch-friendly, drag-safe). |
| `.field` | Inputs / textareas (16px to avoid iOS zoom). |
| `.theme-toggle` | Fixed circular toggle (use with the sun/moon SVGs in `demo.html`). |
| `.eyebrow` | Small uppercase label. |
| `.rise`, `.pop` | One-shot entrance animations. |

## JS API

```js
GlassKit.init(scope?)            // wire data-* hooks (default scope: document)
GlassKit.makeInteractive(el)     // add tilt/drag/press to one element
GlassKit.makeFloat(el)           // add idle float to one element

GlassKit.theme.apply()           // resolve stored/system theme & apply it
GlassKit.theme.set('dark')       // 'light' | 'dark' (persists)
GlassKit.theme.toggle()          // flip & persist
GlassKit.theme.current()         // 'light' | 'dark'
GlassKit.theme.subscribe(fn)     // fn(theme) on every change; returns an unsubscribe
```

`liquid-glass.js` is UMD-ish: it sets a global `GlassKit` and also supports
`module.exports`, so `const GlassKit = require('./liquid-glass.js')` works too.

## Theming / re-skinning

Everything is driven by CSS custom properties on `:root` (and
`:root[data-theme="dark"]`). Override any of them in your own stylesheet —
e.g. change the brand:

```css
:root        { --accent: #b4533a; --bg-grad: radial-gradient(125% 95% at 50% 0%, #ffd9a8, #f2a65a 45%, #d97742); }
:root[data-theme="dark"] { --accent: #ff9a6e; }
```

Key tokens: `--ink* ` (text), `--accent`, `--glass-*`, `--sh` (shadow tint as
`r,g,b`), `--bg-grad`, `--card-bg`, `--radius-*`, `--spring`, `--ease`.

## Font

The kit falls back to a serif stack out of the box. To match Nudge exactly,
add an `@font-face` for **Averia Sans Libre** (the `.ttf` ships in the repo
root) — see the `<style>` block at the top of `demo.html`.

## Notes & support

- Uses Pointer Events + `backdrop-filter`. Works in all current evergreen
  browsers; in browsers without `backdrop-filter` the glass degrades to a
  translucent fill (still fine).
- Respects `prefers-reduced-motion` — float and transitions are stilled.
- Touch-friendly: draggable elements set `touch-action: none` so a drag
  doesn't scroll the page.
