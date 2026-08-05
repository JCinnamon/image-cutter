# Image Cutter from Vis-O-Matic

A browser-based tool for isolating part of an image using Meta's Segment Anything model. Point at what you want, drag a box, or let the model propose every region it can find; review the result and export it as a transparent cut-out, an inverse cut-out, a binary mask, or measurements. Nothing is uploaded — segmentation runs locally in your browser.

**[Live demo →](https://www.jonathancinnamon.com/image-cutter/)**

Part of [Vis-O-Matic](https://www.jonathancinnamon.com/vis-o-matic/), alongside [Batch Image Analyzer](https://www.jonathancinnamon.com/image-colour-analyzer/) and [Image Visualizer](https://www.jonathancinnamon.com/image-colour-visualizer/).

---

## Features

- **In-browser segmentation** — runs a Segment Anything model locally via [Transformers.js](https://github.com/huggingface/transformers.js); model weights download once from the Hugging Face CDN and are then cached. Images never leave your computer.
- **Prompting modes** — point (click to add, Shift- or right-click to subtract), box, automatic (the model proposes every region), and batch across a whole folder.
- **Model choice** — select among SAM variants trading download size against mask quality; WebGPU is used when available, with a WASM fallback.
- **Output types** — transparent cut-out (selected region, everything else transparent), inverse cut-out (the complement), and binary mask (white region on black).
- **Cleanup options** — crop to the bounding box, keep only the largest connected component, and edge softening (feathering).
- **Measurements CSV** — area in pixels, share of frame, bounding box, centroid, and mask score, with the model and device recorded for every export.
- **GPS / EXIF passthrough** *(new)* — preserve the source photo's location and capture metadata through segmentation (see below).
- **Batch export** — export the current image, or all included images as a ZIP with a README and a combined `measurements.csv`.
- **Full transparency** — cut-outs carry a real alpha channel, ready for the Analyzer's transparency handling.

---

## GPS / EXIF passthrough

Exporting a segmented region to PNG normally discards all metadata, because the browser's canvas export writes no EXIF. For geotagged photography this breaks spatial analysis: the cut-out loses the GPS coordinates the original carried.

With **Preserve GPS / EXIF** enabled (on by default, in the Export panel), Image Cutter reads the source photo's EXIF once at load and re-attaches it on export:

- **Into each PNG** — the original EXIF is written as a PNG `eXIf` chunk (PNG 1.5), so coordinates travel inside the image file.
- **Into the measurements CSV** — `gps_lat`, `gps_lon`, `gps_alt`, `datetime`, `camera_make`, `camera_model`, and `orientation` columns are added.

PNG inputs and any images without EXIF simply leave those fields blank. All parsing is self-contained, so the feature works offline. [Batch Image Analyzer](https://www.jonathancinnamon.com/image-colour-analyzer/) reads the `eXIf` chunk back out of these PNGs, so location flows from capture through segmentation to colour analysis with no manual join.

---

## Usage

1. Open the tool in a browser — no installation, no account, no server.
2. Drop in an image or a folder (or choose files), and pick a model.
3. Segment: click a point, drag a box, or run automatic prompting; refine by adding or subtracting points.
4. Review the proposed masks and choose which images to include.
5. In the Export panel, tick the outputs you want (cut-out, inverse, mask, measurements CSV), set the options (crop, largest component, feathering, and Preserve GPS / EXIF), and name the project.
6. **Export current image**, or **Export included as ZIP** for a batch.

A typical research sequence runs Image Cutter → Batch Image Analyzer → Image Visualizer, handing folders of cut-outs and CSVs from one tool to the next. Each tool also works on its own.

---

## Notes & limitations

- The model proposes a mask; it does not verify one. Treat a mask as an instrument reading: report which model produced it and check a sample by eye. Every export records the model and the device it ran on.
- The model resizes each image to about 1024 px internally, so working above ~2000 px costs memory and time without sharpening the mask; exports are rebuilt at the size you choose.
- Above roughly 200 images the browser may feel sluggish, since everything runs client-side.
- EXIF orientation is copied verbatim under full passthrough; if a downstream tool auto-rotates, be aware the tag travels with the file.

---

## Privacy

No image ever leaves your computer. There is no server, no account, and no upload step. The tool works offline; web access is needed only once, to download the Segment Anything model, which is then cached locally.

---

## Citation

If this is useful in research or teaching, please cite the tool:

> Cinnamon, J. (2026). *Image Cutter* [Computer software]. Vis-O-Matic. https://www.jonathancinnamon.com/image-cutter/

---

## License

MIT. Model weights and libraries carry their own licences, noted in the tool. Free to use and adapt with attribution.

---

*Jonathan Cinnamon · [jcinnamon.github.io](https://jcinnamon.github.io/) · UBC Geography*
