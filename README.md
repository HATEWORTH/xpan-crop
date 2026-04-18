# xpan-crop

**[Open the app →](https://hateworth.github.io/xpan-crop/)**

A browser-based cropping tool for producing Hasselblad XPan–style panoramic images (65:24 by default) from any source photo. Runs entirely client-side as a single HTML file — nothing is uploaded.

## What it does

- Opens an image via file picker or drag-and-drop.
- Overlays a crop box locked to a configurable aspect ratio (65:24 preset for XPan).
- Lets you slide the crop along the free axis by dragging or using arrow keys (Shift for 10px steps).
- Optionally adds a white matte border around the crop, matching the look of a printed XPan frame.
- Exports a lossless PNG at the source image's native resolution — no resampling, no quality loss.

## Usage

1. Open [the app](https://hateworth.github.io/xpan-crop/).
2. Drop an image onto the window or click **Open image**.
3. Adjust the aspect ratio if you want something other than 65:24.
4. Drag the crop box to frame your shot; use arrow keys to nudge.
5. Toggle **Matte** on if you want a white border baked into the export.
6. Click **Export PNG** to download the cropped result.

## Running locally

It's a single self-contained HTML file with no build step or dependencies. Clone the repo and open `index.html` directly in any modern browser.
