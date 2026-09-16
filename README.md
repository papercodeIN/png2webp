# png2webp

Local, private PNG → WebP converter with **Cloudinary-style smart encoding**: smallest file at visually-lossless quality. No server, no upload — everything runs in your browser.

## Features

- 🖼️ Multiple PNG upload (click or drag & drop)
- 🧠 Smart encode — tries qualities 75→93, keeps the smallest file scoring **PSNR ≥ 40 dB** against the original; falls back to quality 100 if needed
- ✏️ Auto-rename: `My Photo-1.png` → `My_Photo_1.webp` (spaces & dashes → underscores, unsafe chars sanitized)
- ⬇️ Per-file download + Download All (individual files, no zip)
- 🔒 100% offline after load — zero dependencies, single HTML file

## Requirements

None. No installation, no dependencies, no build step, no internet needed.

- Any modern browser (Chrome, Edge, Firefox, Safari — 2020 or newer) with WebP support. That's it.
- Copy `index.html` to any machine and double-click it.
- Optional: serve over HTTP with `python -m http.server` (only if you prefer a URL instead of opening the file directly — any static file server works).

## Use

Double-click `index.html`, or serve locally:

```bash
python -m http.server
```

Then open http://localhost:8000

## How it works

Each PNG is drawn to canvas and encoded at ascending WebP qualities. Every
candidate is decoded and compared to the original via PSNR (on a ≤2MP sample
for speed). The first candidate at ≥ 40 dB wins — giving the smallest file
with visually-lossless fidelity, like Cloudinary's png-to-webp tool.
