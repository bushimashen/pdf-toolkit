# PDF Toolkit · PDF 工具箱

> Merge, split, delete pages, rotate, watermark and number PDFs — entirely in your browser. No upload, no server, works offline.

**English** | [简体中文](README.zh-CN.md)

<a href="https://bushimashen.github.io/pdf-toolkit/"><b>▶ Live demo</b></a>

## Features

| Tab | What it does |
|---|---|
| **Merge** | Combine any number of PDFs, reorder them with ↑ ↓ before stitching |
| **Split** | Keep only the pages you list (`1-3,5,8-`) and export them as one new PDF |
| **Delete pages** | Remove the pages you list, keep the rest |
| **Rotate** | 90° / 180° / 270°, applied to all pages or a subset |
| **Watermark** | Text watermark with position (3×3 grid), size, opacity, angle, colour — plus a tiled "fill the page" mode |
| **Page numbers** | Four label styles, five positions, custom start number and font size |

Everything is client-side. Drop in a PDF, get a new PDF back; the original file is never touched.

## Why this exists

Most "free online PDF tools" upload your file to a server and hand back a download link. That is a
bad deal for contracts, ID scans and internal decks. `pdf-lib` is a pure-JavaScript PDF writer that
runs in the page, so the file never leaves the machine — and the tool keeps working with the network
switched off.

## Details worth knowing

**Chinese watermarks go through a canvas.** `pdf-lib`'s built-in fonts are the PDF standard 14, and
none of them contain CJK glyphs — `drawText('内部资料')` throws. So the watermark text is drawn onto
an offscreen `<canvas>` at the chosen size / colour / angle / opacity, baked to a transparent PNG, and
stamped as an image. Side effect: any font the browser has becomes usable, and the same trick gives
free rotation and alpha.

The canvas is sized to the **rotated bounding box** (`w·|cos| + h·|sin|`, etc.) rather than a
diagonal-sized square. A square is 70–90 % empty pixels, which bloats the embedded PNG by an order of
magnitude for no visual gain.

**Page numbers use Helvetica on purpose.** They contain only digits and ASCII punctuation, so the
PDF standard font covers them and no reader will ever show missing-glyph boxes.

**Rotation is additive.** `page.setRotation()` writes the `/Rotate` entry, so the tool reads the
current value and adds to it instead of overwriting — rotating twice gives 180°, not 90° again.

**Page ranges are forgiving.** `1-3,5,8-` parses to `{1,2,3,5,8,9,10}`; out-of-range numbers are
clipped, reversed ranges (`3-1`) are swapped, and an empty box means "all pages". Deleting every page
is rejected rather than producing a zero-page file.

## Usage

Keep the folder structure — `index.html` loads `vendor/pdf-lib.min.js` by relative path.

```bash
python -m http.server 8000   # then open http://localhost:8000
```

`file://` generally works too, but if your browser restricts local script loading you will see a
red banner saying `pdf-lib` failed to load; a local server is the reliable path.

## Browser support

Any current Chrome / Edge / Firefox / Safari. Uses `Promise`, `Set`, `canvas.toBlob`,
`File.arrayBuffer` and `<input type="file">` — no build step, no polyfills.

## License

MIT for the page code — see [LICENSE](LICENSE).
`vendor/pdf-lib.min.js` is [pdf-lib](https://github.com/Hopding/pdf-lib) 1.17.1, MIT, © Andrew Dillon —
see [vendor/pdf-lib-LICENSE.md](vendor/pdf-lib-LICENSE.md).
