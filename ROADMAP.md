# Roadmap & Product Thinking

A living document. Collects open questions about whether this app needs to exist at all, gaps in existing photo tools that this app could fill, and a prioritized list of future work.

---

## Does this app actually need to exist?

Honest appraisal: **mostly not, as a generic cropper.** Lightroom, Photoshop, Capture One, Affinity Photo, and dozens of free web tools all accept a custom aspect ratio like 65:24. If you own any of those, you can already crop to XPan.

But there are real workflow gaps it fills for the specific panoramic use case, and a few are meaningful enough to justify the project:

1. **Purpose-built for the ratio.** Lightroom treats 65:24 as "just another custom crop" — you open the crop tool, remember to enter 65:24, then work. This app starts there. For a photographer shooting *intending* an XPan crop, that's genuinely faster.

2. **The crop is constrained to full-frame, slide-only.** XPan shooting discipline is "I'll crop top/bottom off the 3:2 frame." Generic crop tools give you free-size handles and let you shrink the box inward, which is a different (and often unintended) action. This app's default behavior matches the XPan mental model — you only decide vertical position. Corner handles exist for when you want something smaller, but the default is the classic XPan crop.

3. **Bit-exact output with no re-render.** Lightroom's export pipeline re-renders the image: sharpening pass, color space conversion, possibly a quality slider. If you've already finalized a 16-bit TIFF and just want to cut a rectangle out, there's no clean "crop only" pipeline in LR short of "Edit in..." which adds another round trip. This app copies the source pixels directly — no resampling, no sharpening, no color-space conversion.

4. **Zero install, zero cost, zero upload.** A photographer without a Lightroom subscription, or one working on a borrowed machine, or one who doesn't want to upload photos to an online tool, has few good options.

5. **XPan-style white matte baked into export.** The printed-frame aesthetic is part of the XPan look. Doing it by hand in LR/PS is manual canvas-extension work. Here it's one toggle.

So: not revolutionary, but a legitimate niche. The justification tightens if we lean into the panoramic identity and add features that a generic cropper can't easily replicate.

---

## Pain points in existing tools

Things I hear repeatedly from photographers, and that could differentiate this app:

### Lightroom / Capture One
- **No "lock to aspect, slide only" mode.** Every crop handle is free-resize. For panoramic work where you only care about vertical position, that's friction.
- **Batch cropping to the same aspect is tedious.** "Apply to all" in LR applies the preset but not the positioning — you still tweak each one.
- **Export re-renders everything.** There's no "I already finalized this, just cut a rectangle" path.
- **No built-in XPan-style matte/frame.** Canvas extension + fill is a manual chore.
- **Crop presets can't be tagged/colored/grouped.** Finding "XPan" in a list of 40 aspect ratios is annoying.

### Photoshop
- **Overkill for cropping.** Launch time, memory, subscription, complexity.
- **16-bit preservation through crop + export is fiddly.** Depends on color settings and save dialog choices. Easy to accidentally demote to 8-bit.

### Affinity Photo / GIMP
- Better than PS for this task, but same "no dedicated panoramic workflow" gap.

### Free online crop tools (iloveimg, croppola, etc.)
- **Re-encode everything as JPEG.** Nearly always quality loss.
- **Upload required.** Privacy concern, slow for 40 MP files.
- **No 16-bit / TIFF support at all.**
- **No pixel-accurate numeric output.**

### ImageMagick / jpegtran on the CLI
- **Lossless crop works,** but requires knowing coordinates before you see the result.
- **No live preview.**
- **Barrier for non-technical users.**

---

## Roadmap

Ordered by "value to the specific XPan/panoramic workflow" first. Items in the first two tiers directly address pain points above; later tiers widen the scope.

### Tier 1 — fills real workflow gaps

1. **Batch mode.** Drop a folder (or multi-select), apply the same aspect + vertical position to all, export to a chosen destination folder (File System Access API). Addresses the "LR batch crop is tedious" pain.
2. **EXIF / XMP passthrough.** Camera, lens, exposure, GPS, title, keywords, LR develop settings all survive the crop. Currently stripped. Big deal for any photographer with a searchable library.
3. **ICC profile passthrough.** Wide-gamut workflows (AdobeRGB, ProPhoto, Display-P3) need this. Without it, colors shift after crop.
4. **Horizon straighten / rotate.** Panoramic framing lives or dies on a level horizon. A simple −10° to +10° slider with gridlines would go a long way. Resampling required, so it's opt-in and only applies to TIFF/PNG paths, not truly-lossless JPEG.
5. **Undo / redo.** Basic UX hygiene that's currently absent.
6. **Save/restore settings (localStorage).** Remember last aspect, matte state, sidebar collapsed state, preferred export folder.
7. **Dual-format XPan preview.** XPan cameras had a 24×65 pano mode *and* a 24×36 "normal" mode. Show both crop candidates simultaneously so you can decide which frame to use.

### Tier 2 — polish and stretch into identity

8. **More grid overlays.** Golden spiral, diagonals, center cross, Pentagon compositional frames. Toggle per-session.
9. **Film-edge border option.** Beyond the plain white matte — emulate XPan's actual frame shape with rounded corners and frame-number tick marks. An aesthetic that Instagram-driven film enthusiasts already demand.
10. **Print-size calculator.** "At 300 DPI, this crop prints at 18.6 × 6.9 in / 47.3 × 17.5 cm." Displayed inline with the crop info.
11. **Before/after toggle.** Quick key to flash between original-framing and cropped view.
12. **Preset thumbnails.** Hover a preset in the dropdown to see a preview of where the crop would sit. Lightroom doesn't do this.
13. **Signature / watermark text.** Subtle corner text baked into export. Toggle and customize.

### Tier 3 — scope widens, risk widens

14. **File System Access API for direct writes.** Replace the "download a file" flow with "write to this folder." Big UX upgrade once you're doing batches.
15. **Lossless JPEG crop via jpegtran-wasm.** Only if JPEG usage grows meaningful. Current canvas path is visually indistinguishable at q=97; correctness matters more than bytes.
16. **HEIC / HEIF read support.** iPhone photos. Browser-side decoders exist but the library is chunky.
17. **DNG / RAW support.** Would transform this from a post-LR tool into a complete RAW→crop→export pipeline. libraw-wasm exists but is a large commitment (~2 MB) and probably overlaps with LR too much to justify.
18. **WebGL preview path.** GPU-accelerated zoom/pan. Useful only if huge files reveal lag on current canvas path.
19. **16-bit PNG/TIFF display pipeline.** Currently previews demote to 8-bit for on-screen display (invisible to the eye on a typical monitor, but technically incorrect). Real 16-bit preview would need WebGL.

### Tier 4 — integration experiments

20. **Lightroom export plugin.** "Crop for XPan" as an LR export preset that runs this logic on the developed image. Meets users where they already are.
21. **Capture One plugin.** Same idea.
22. **Shareable preset links.** URL-encoded settings (aspect, matte, crop position) that rebuild the app state. Good for collaborators or tutorials.
23. **CLI / headless mode.** For people who want to script batch crops into a build/deploy or asset pipeline.
24. **Preset export/import as `.xpancrop` files.** Simple JSON, shareable.

---

## Non-goals

Calling these out to prevent scope creep:

- **Full photo editor.** Exposure/contrast/curves belong in Lightroom. Adding them here turns us into a worse LR. One exception: straightening, because panoramic framing demands it.
- **AI anything.** No auto-straighten, no auto-crop suggestions, no subject detection. Not our lane.
- **Cloud sync.** The privacy-first, offline-first angle is a genuine differentiator. Don't burn it.
- **Mobile-first UI.** A cropping tool for 40 MP images on a phone screen is a worse cropping tool. Keep the desktop focus.

---

## Open questions

- Is anyone actually shooting XPan-intended on X-E5s besides the project owner? If the user base is "me and three friends," the Tier 2/3 items aren't worth the effort — Tier 1 is plenty. Worth checking before committing to a bigger build.
- Is lossless 16-bit worth it? For print output yes; for screen output no. Most users probably don't know the difference. Communicating the value is part of the product.
- Should we register the repo with the IMDB/film-camera community (r/analog, film photography forums, XPan enthusiast sites)? That's how we'd find out if a roadmap investment has an audience.
