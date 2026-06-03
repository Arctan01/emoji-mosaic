# Emoji Mosaic

A single-file, in-browser tool that turns any image into emoji art by **color matching** — every cell becomes the emoji whose measured average color sits closest to that patch of the image, in perceptual Lab space. It also includes a **moon-phase relief** mode that sculpts images out of 🌑🌒🌓🌔🌕🌖🌗🌘.

No build step, no dependencies, no server. Open `emoji-mosaic.html` in a browser and go.

## Quick start

1. Open `emoji-mosaic.html` in any modern browser (Chrome, Safari, Firefox, Edge).
2. Drag an image onto the drop zone, or click to choose a file.
3. Adjust the controls. The output re-renders live.
4. Export with **↓ PNG**, or **⧉ Copy text** to grab the raw emoji grid as plain text.

A small sample image renders on load, so you can see the engine working before uploading anything.

## How it works

The core idea is that the emoji palette is **measured, not hard-coded**:

1. On load, each candidate emoji is drawn to an offscreen canvas using your system's emoji font.
2. Its **alpha-weighted average color** is computed over opaque pixels only (so transparent corners don't drag the color toward the background), then converted to **CIELAB**.
3. The uploaded image is downscaled to a `cols × rows` grid so each cell becomes one area-averaged color.
4. Each cell is matched to the nearest palette entry by **Lab distance**, and the emoji is painted into an output canvas.

Because colors are measured on your device, the matching stays correct whether your system renders emoji via Apple Color Emoji, Noto, Twemoji, or Segoe. The sidebar swatches show the actual measured color of each emoji — that's the palette the matcher uses.

If on-device measurement fails for any glyph (some browsers can't read color-emoji bitmaps back from canvas), the tool falls back to a built-in approximate color so the palette is never empty.

## Controls

| Control | What it does |
|---|---|
| **Mode** | `Color match` (nearest-color mosaic) or `Moon relief` (luminance + light-direction emboss). |
| **Resolution** | Number of columns, 16–512. Rows are derived from the image aspect ratio. |
| **Palette** | Emoji set used for color matching (see below). Disabled in Moon relief mode. |
| **Backing color** | Fill behind the glyphs. Matters most with non-square emoji, whose transparent corners let it show through. |
| **↓ PNG** | Download the rendered mosaic as a PNG. |
| **⧉ Copy text** | Copy the emoji grid as plain text (newline-separated rows). |

### Palettes

- **Squares** — `🟥🟧🟨🟩🟦🟪🟫⬛⬜`. Fully fill each cell, giving the cleanest solid mosaic. Only ~9 colors, so output is intentionally posterized.
- **Round** — circle equivalents; similar gamut, leaves background visible in the corners.
- **Spectrum+** — squares plus assorted colored objects for a wider range.
- **Max** — ~60 emoji spanning the broadest gamut, including the mid-grays and off-whites the small sets miss. A dedupe pass merges any emoji that measure to near-identical colors, keeping the palette efficient.
- **Hearts** — `❤️🧡💛💚💙💜🤎🖤🤍🩷🩵` for a themed look.

### Moon relief mode

Ignores color entirely and builds the image from the eight moon phases:

- **Brightness** picks the phase along the dark→light axis (🌑 → 🌕).
- The **horizontal light gradient** within the image decides waxing vs waning: cells that are brighter toward the right use right-lit phases (🌒🌓🌔), brighter toward the left use left-lit phases (🌘🌗🌖).

Encoding light direction this way gives edges a lit-from-the-side, sculpted feel rather than flat shading. Works best on images with strong directional lighting (portraits, side-lit scenes) on a dark backing.

## Notes & limitations

- **Emoji gamut is narrow.** Even the Max palette can't reach deeply desaturated or muted tones — emoji cluster around saturated primaries, mid-grays, and a few browns. Photos with subtle palettes will flatten.
- **High resolution = large exports.** Cell size shrinks adaptively as columns increase to keep PNG dimensions reasonable, but 512 columns on a tall image is still a multi-thousand-pixel file. Watch the build-time readout.
- **Text copy** preserves emoji including variation selectors; alignment in other apps depends on their emoji width handling.
- Everything runs client-side. Images never leave your machine.

## Possible next steps

- **Dithering** (e.g. Floyd–Steinberg) to make the limited palette mix optically and fake in-between colors — the single biggest quality gain available for color mode.
- **Light-direction control** for relief mode, to rotate the "sun" to any angle (including vertical gradients) instead of just left/right.
- **Per-emoji repeat penalty** so flat regions don't collapse to a single repeated glyph.

## File

- `emoji-mosaic.html` — the entire application (HTML + CSS + JS in one file).
