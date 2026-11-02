# png2webp

Local, private **any-image → WebP** converter with **Cloudinary-style smart encoding**: smallest file at visually-lossless quality. No server, no upload, no storage — everything runs in your browser and stays in memory until you close the tab.

## Supported inputs

| Group | Formats | How it's decoded |
|-------|---------|------------------|
| Native | PNG · JPEG · GIF · BMP · WebP · AVIF · ICO · SVG | Directly by your browser (AVIF needs a 2020+ browser) |
| iPhone / modern camera | **HEIC · HEIF · HIF** | Bundled libheif decoder (`vendor/heic2any.min.js`); multi-image containers are split into one WebP per image |
| Scanner / pro | **TIFF · TIF** | Bundled decoder (`vendor/UTIF.js` + `pako`); first page of multi-page files |

All inputs are converted to **WebP**.

## Features

- 🖼️ Multiple images at once (click or drag & drop — files never leave your machine)
- 🧠 Smart encode — tries qualities 75→93, keeps the smallest file scoring **PSNR ≥ 40 dB** against the original; falls back to quality 100 if needed
- ⚡ Parallel worker-pool encoding — batch conversions run in the background, the page stays responsive, and quality selection is identical to the classic loop (verified byte-identical output)
- ✂️ Multi-image HEIC containers (iPhone bursts) are split automatically — one WebP per image
- 🚦 Per-file status chips (decoding → smart-encoding → ready / failed) with clear reasons for undecodable files, and toasts for skipped unsupported files
- ✏️ Auto-rename: `My Photo-1.png` → `My_Photo_1.webp` (spaces & dashes → underscores, unsafe chars sanitized)
- ⬇️ Per-file download + Download All (individual files, no zip)
- 📋 Output path list (`/uploads/Your_File.webp`) with editable prefix and one-click copy
- 🔒 **Private by design** — there is no server. Files are read from disk into browser memory, converted locally, and held only until you clear the queue or close the tab. Nothing is uploaded, logged, or stored anywhere. No analytics, no cookies, no network requests.

## Requirements

None beyond a modern browser. No installation, no build step, no internet.

- Any modern browser (Chrome, Edge, Firefox, Safari — 2020 or newer) with WebP support.
- Keep `index.html` and the `vendor/` folder together (the vendored decoders are what make HEIC/TIFF work, including offline and from a `file://` double-click). PNG/JPEG and other native formats work even if `vendor/` is missing.

## Use

Double-click `index.html`, or serve locally:

```bash
python -m http.server
```

Then open http://localhost:8000

## How it works

1. **Decode** — HEIC/HEIF is decoded with bundled libheif (`heic2any`), TIFF with bundled `UTIF`; everything else (PNG, JPEG, GIF, BMP, AVIF, WebP, ICO, SVG) is decoded natively by the browser. Decoded pixels are drawn to a canvas.
2. **Smart encode** — the canvas is encoded at ascending WebP qualities (75→93). Every candidate is decoded again and compared to the original via PSNR (on a ≤2MP sample for speed). The first candidate at ≥ 40 dB wins — the smallest file with visually-lossless fidelity, like Cloudinary's png-to-webp tool. Encoding runs in a parallel worker pool, so batch conversions don't block the page; when workers aren't available it falls back to the identical main-thread loop.
3. **Output** — filenames are sanitized, output paths are generated with your prefix, and downloads happen straight from memory.

## Credits

Bundled decoder libraries (see `vendor/README.md` for sources and licenses):

- [heic2any](https://github.com/alexcorvi/heic2any) (libheif) — MIT
- [UTIF.js](https://github.com/photopea/UTIF.js) (Photopea) — MIT
- [pako](https://github.com/nodeca/pako) — MIT + Zlib

## Changelog

### 2026-11-03 — Any-image support, faster encoding, UI refresh

- **Any image in:** added HEIC / HEIF / HIF (bundled libheif decoder) and TIFF / TIF (bundled UTIF decoder) on top of the natively-supported PNG, JPEG, GIF, BMP, AVIF, WebP, SVG and ICO. Everything converts to WebP.
- **Multi-image HEIC:** containers holding several photos (e.g. iPhone bursts) are split into one queue item per image, named `name_1.webp`, `name_2.webp`, …
- **Faster, non-blocking encoding:** the smart-encode loop (WebP candidates + PSNR verification) moved into a parallel worker pool. Batch conversions encode side by side and the page never freezes; the original main-thread loop is kept as an automatic fallback. Output selection logic is unchanged — A/B-verified byte-identical results.
- **Clearer feedback:** status chips per file (decoding → smart-encoding → ready / failed), toast notifications for skipped unsupported files, per-item remove, and friendly error messages (corrupt file, browser can't decode a format, `vendor/` folder missing).
- **UI refresh:** simplified single-screen layout in the Fusion Automate dark + lime style, with the shop-style angular buttons; details live in the Rules modal.

### Earlier

- PNG/JPEG → WebP with Cloudinary-style smart encoding (q75→93, PSNR ≥ 40 dB), auto-rename, `/uploads/` path list, per-file + bulk download, 100% local operation in a single HTML file.
