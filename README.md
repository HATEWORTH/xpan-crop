# xpan-crop

**[Open the app →](https://hateworth.github.io/xpan-crop/)**

A browser-based cropping tool for producing Hasselblad XPan–style panoramic images (65:24 by default) from any source photo. Runs entirely client-side — nothing is uploaded.

Built for the workflow: shoot RAW on a crop-sensor camera (e.g. Fujifilm X-E5, 40MP APS-C) → develop in Lightroom → export as PNG or TIFF → crop here without losing a single pixel of quality.

## Format handling

The app auto-detects the source format and picks the right pipeline:

| Input | Pipeline | Quality loss |
|---|---|---|
| **PNG 8-bit** | Canvas crop, re-encode PNG | None |
| **PNG 16-bit** | UPNG decode → slice 16-bit buffer → encode PNG 16-bit | None (avoids 8-bit canvas demotion) |
| **TIFF 8-bit** | UTIF decode → canvas crop → UTIF encode | None |
| **TIFF 16-bit** | UTIF decode → 16-bit pixel slice → hand-written uncompressed 16-bit RGB TIFF | None |
| **JPEG** | Canvas crop, re-encode JPEG (q=97) | Re-encoded — use PNG/TIFF for lossless |

The info bar shows the detected format and bit depth so you know which path you're on.

## Features

- **Aspect ratio presets**: XPan (65:24), 3:2, 4:3, 1:1, 16:9, 4:5, 5:4, 2.35:1, 2.39:1, 7:6, 6:7, and custom.
- **Zoom & pan**: wheel to zoom on cursor, `space`+drag to pan, `+`/`−`/`0` shortcuts, Fit button.
- **Crop positioning**: drag the crop box, arrow keys to nudge (Shift = 10 px).
- **Rule-of-thirds overlay** inside the crop.
- **White matte** option for a printed XPan-frame look, baked into export.

## Usage

1. Open [the app](https://hateworth.github.io/xpan-crop/).
2. Drop a PNG / TIFF / JPEG onto the window or click **Open image**.
3. Pick a preset (default XPan 65:24) or type a custom ratio.
4. Drag the crop box to frame your shot; zoom in to check framing at pixel level.
5. Toggle **Matte** on if you want a white border baked into the export.
6. Click **Export** — the filename encodes the ratio (e.g. `photo_xpan_65x24.png`).

## Running locally

Single self-contained HTML file with no build step. Clone the repo and open `index.html` in any modern browser. Format-specific libraries (UTIF, UPNG, pako) are lazy-loaded from jsDelivr only when needed.
