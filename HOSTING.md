# Hosting TriLayerQR

This folder is self-contained: the two pages load everything from `vendor/`, with no CDN or external request.

```
trilayerqr.editor.v1.html    create, decode and re-edit codes
trilayerqr.scanner.v1.html   scan with the camera, or open an image or card PDF
vendor/                      libraries, WebAssembly, face detector and fonts
```

## Put it online
Upload the whole folder, keeping the structure, to any static web host (GitHub Pages, Netlify, Cloudflare Pages, your own server). The camera needs **HTTPS**.

Most hosts serve `.wasm` files correctly. If yours doesn't, add the MIME type `application/wasm` for `.wasm`.

## Run it locally
The pages load WebAssembly, which browsers block when a page is opened straight from disk (`file://`). Start any small web server in this folder instead:

```
python3 -m http.server 8000
```

Then open http://localhost:8000/trilayerqr.editor.v1.html. `localhost` also allows camera access for the scanner.

## Sizes
- Scanner: about 1.2 MB (QR readers and fonts).
- Editor: the same, plus the face detector (about 7 MB), which loads only when a circular avatar is centered on a face.
