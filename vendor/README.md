# Vendor libraries

Pre-downloaded, self-contained browser libraries used by `index.html` for formats
that browsers can't decode natively. Everything here is loaded locally — no CDN,
no network access, nothing leaves your machine.

Keep this folder next to `index.html`.

| File | Source | License | Purpose |
|------|--------|---------|---------|
| `heic2any.min.js` | [npm heic2any@0.0.4](https://www.npmjs.com/package/heic2any) (bundles libheif) | MIT | Decode HEIC / HEIF / HIF images (e.g. iPhone photos) |
| `UTIF.js` | [npm utif@3.1.0](https://github.com/photopea/UTIF.js) (Photopea) | MIT | Decode TIFF / TIF images |
| `pako.min.js` | [npm pako@2.1.0](https://github.com/nodeca/pako) | MIT + Zlib | Inflate dependency of `UTIF.js` (deflate-compressed TIFFs) |

Load order matters: `pako.min.js` → `UTIF.js` (UTIF looks up `self.pako`).

All common browser-native formats (PNG, JPEG, GIF, BMP, WebP, AVIF, ICO, SVG)
need no library — they are decoded directly by the browser.
