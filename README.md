# homarr-css

A custom theme for the [Homarr](https://homarr.dev) dashboard - "liquid / frosted glass": matte translucent tiles and header with a blurred background, a crisp edge and highlights, tidy title typography, and adaptation to light/dark themes.

Tested on Homarr `v1.71.0`.

<img src="image/README/style-dark.png" alt="style-dark" width="1600"/>

<img src="image/README/style-light.png" alt="style-light" width="1600"/>

## What the theme does

- **Frosted glass** on all tiles and on the header (`backdrop-filter: blur`) - the board's background shows through and is blurred behind them.
- **A unified visual language:** rounded corners, a thin light edge, a soft shadow, a subtle top-left highlight, and a hover glow.
- **Order in tile content:** icons don't "jump" on hover, and long titles wrap instead of being clipped.
- **Light and dark theme support** (via the `data-mantine-color-scheme` attribute).
- **A workaround for a Chromium bug** with a ghost strip under the header (see below).

## Installation

Homarr → **Manage → Settings → Customization → Custom CSS** (or the corresponding custom-CSS section in your version), paste the contents of [`style.css`](style.css), and save.

The theme relies on Mantine/Homarr classes (`.mantine-AppShell-*`, `.grid-stack-item-content`, `.app-*`, etc.). A major Homarr update may change the classes or CSS hashes - in that case the rules need to be re-checked.

## Known Chromium bug: ghost strip under the header

**Symptom.** On narrow screens (< ~530px), a translucent horizontal strip ("two layers of glass") appeared under the header's bottom edge, visible when moving the cursor / on hover, and disappearing in DevTools inspector mode.

**Cause.** A Chromium compositor bug: the tiles' `backdrop-filter` draws a translucent **duplicate of the bottom edge of the fixed (`position: fixed`) header**. The ghost only "bleeds" about ~10px below the header edge - so the problem only shows up while the top row of glass tiles sits flush against the header.

**What did NOT help** (verified, don't waste your time): `isolation: isolate`, `clip-path`, `contain: paint`, `translateZ`/`will-change` on the header/tiles/`main`, reducing `blur`. `html { transform: translateZ(0) }` removes the strip but breaks the header pinning (it scrolls away). `position: sticky` on the header only helps at the cost of a large gap appearing.

**Solution.** Slightly increase the top padding of the content so the top row of tiles moves out of the artifact zone (leaving the glass and the header position untouched):

```css
.mantine-AppShell-main {
  padding-top: calc(
    var(--app-shell-header-offset) + var(--app-shell-padding) + 10px
  ) !important;
}
```

The default padding is ≈ 76px (header height 60 + padding 16); `+10px` is the minimum value at which the strip is already gone.
