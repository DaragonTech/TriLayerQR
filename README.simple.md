# TriLayerQR Simple

**Everyday QR codes that hold more.** Type a text, get a QR code that stores up to **twice as many bytes in the same size**, and with compression often **three to five times as much text**. Or, the other way round: the same text in a much smaller code that prints smaller and scans from farther away.

No cards, no pictures, no accounts. One page that turns text into a code and codes back into text. It's the everyday little sibling of TriLayerQR, built by [DaragonTech](https://daragon.tech).

---

## At a glance

| Text | TriLayerQR Simple | Standard QR |
|---|---|---|
| A link, Wi-Fi details or a contact | Black and white, same size as usual | Same |
| Shakespeare's Sonnet 18 (626 characters) | **57 × 57** squares, 4 colors | 97 × 97 squares |
| A 5,000-character text | **117 × 117** squares, 4 colors | Doesn't fit at all |

Sonnet 18 needs **65% fewer squares** than in a standard QR code. Fewer squares means bigger squares at the same printed size, so the code is easier to scan from a distance or at a small size.

---

## How much it expands capacity

A QR code comes in 40 sizes, called versions, from 21 × 21 squares (version 1) to 177 × 177 (version 40). At each size, TriLayerQR Simple stores about **twice the bytes** of a standard QR, because it stacks two codes of the same size in the colors. On top of that, longer texts are compressed first.

Default error correction (Medium, 15%):

| Version | Size (squares) | Standard QR | TriLayerQR Simple, stored | Typical text that fits* |
|---|---|---|---|---|
| 1 | 21 × 21 | 14 bytes | 22 bytes | ~22 characters |
| 5 | 37 × 37 | 84 bytes | 162 bytes | ~200 characters |
| 10 | 57 × 57 | 213 bytes | 420 bytes | ~700 characters |
| 15 | 77 × 77 | 412 bytes | 818 bytes | ~1.7 KB |
| 20 | 97 × 97 | 666 bytes | 1,326 bytes | ~3 KB |
| 25 | 117 × 117 | 997 bytes | 1,988 bytes | ~5 KB |
| 40 | 177 × 177 | 2,331 bytes | 4,656 bytes | ~11–12 KB |

\* For ordinary writing. Text is compressed before it's stored: short texts barely shrink, while longer prose typically shrinks to a third or half of its size (Sonnet 18: 626 → 356 bytes; a 5,000-character text: 4,993 → 1,931 bytes). Repetitive text shrinks even more. Random-looking data, such as passwords or keys, doesn't compress, so it gets exactly the "stored" column.

So, compared with a standard QR code:
- **Same size:** about **2×** the bytes, and about **3–5×** the text for longer writing.
- **Same text:** a code with roughly **half the squares or fewer** once the text is longer than a few hundred characters.
- **Maximum:** about **11–12 KB of text** in one code, where a standard QR tops out at 2.3 KB.

---

## How it works

### Two codes, stacked in color
Every color on a screen is a mix of red, green and blue light. TriLayerQR Simple draws two QR codes of the same size on top of each other:

- the **green** channel carries the first half of the data;
- the **red and blue** channels both carry the second half (the same data twice).

Each square then shows one of just **4 colors**:

| First half (green) | Second half (red + blue) | Square |
|---|---|---|
| light | light | white |
| dark | dark | black |
| light | dark | green |
| dark | light | magenta |

**Why not 8 colors?** Three separate layers would carry more, but blue alone is the channel that cameras and image compression damage most. Giving red and blue the same data means the reader can use both at once, and the code stays reliable on phone cameras, screens and compressed images. We tested 8 colors first; 4 colors read far more reliably.

### Compression
Before splitting, the text is compressed (the same *deflate* method used in ZIP files) whenever that makes it smaller. The reader decompresses it automatically.

### Auto mode: colors only when they pay off
- **Short texts stay black and white.** Links, Wi-Fi details and contact cards that fit in a code of up to 41 × 41 squares are made as ordinary QR codes, so **any phone camera** can read them.
- **Longer texts get colors** when that makes the code clearly smaller (at least two sizes smaller), or when the text doesn't fit in a standard QR at all.
- You can override it: **Always use colors** gives the smallest possible code; **Black and white only** gives maximum compatibility.

### Reading
The reader looks for a QR code, reads the green half, then reads the red and blue channels for the second half, and joins them. It also reads ordinary black-and-white QR codes, so it works as a general QR scanner.

It reads from:
- the **camera** (it alternates between the full frame and a zoomed center, and collects the two halves across frames);
- an **image** file (PNG or JPEG);
- the code you just created, to check it.

It also works **from a screen**: a monitor is made of the same red, green and blue lights the code uses, so a phone pointed at a screen reads it well. Turn the brightness up and switch off Night Shift or other blue-light filters, which shift the colors.

---

## Compatibility

| Code | Who can read it |
|---|---|
| Black and white (Auto for short texts, or "Black and white only") | **Any** QR scanner or phone camera |
| Color (4 colors) | TriLayerQR Simple (or any app implementing the format below) |

A normal phone camera pointed at a color code only sees the green half, which is why Auto keeps everyday short codes black and white.

---

## Getting started

It's a single file: `trilayerqr.simple.v1.html`.

- **Create:** type or paste text. The code updates as you type. Choose the colors mode and error correction, then **Save PNG** (4–16 pixels per square).
- **Read:** **Scan with camera**, **Open image**, or **Read the code on the left**.

The camera needs **HTTPS** (or `localhost`), so host the page on your site, or run a local server (`python3 -m http.server`) and open it at `http://localhost:8000/`. Everything runs in your browser; nothing is uploaded.

On first use, the page loads its QR libraries from a public CDN (then from the browser cache).

### Browser support
Current Chrome, Edge, Firefox and Safari (desktop and mobile). Compression needs a browser from 2023 or later; older browsers still create uncompressed codes.

---

## For developers: the format

A **black-and-white** code is a standard QR code in byte mode containing the UTF-8 text. Nothing else is added.

A **color** code is two QR codes of the **same version and error correction level**:

- green channel: layer 0, red and blue channels: layer 1 (identical in both);
- a dark square means the channel is off (0), a light square means it's on (255).

Each layer's data:

```
'T' 'Q' | flags | chunk
flags:  bit 0 = layer index (0 = green, 1 = red/blue)
        bit 3 = the joined data is deflate-raw compressed
```

To decode: read both layers, check the `TQ` magic, join chunk 0 then chunk 1, decompress if bit 3 is set, and decode as UTF-8. Text is split in half, rounding up for layer 0.

---

## Limitations

- **Color codes need a TriLayerQR reader.** Standard scanners see only the green half.
- **Colors need true color.** Print in color, or show on a screen. Heavy image compression (for example WhatsApp photos) can wash the colors out; share the PNG as a file or document instead.
- **Fill the frame.** Phone cameras record color at lower detail than brightness, so hold the phone close enough for the code to fill most of the frame.

---

## Credits and license

TriLayerQR Simple is developed by [DaragonTech](https://daragon.tech) and released under the [MIT License](LICENSE).

It builds on:
- [qrcode-generator](https://github.com/kazuhikoarase/qrcode-generator): QR code generation
- [zxing-wasm](https://github.com/Sec-ant/zxing-wasm) / [ZXing-C++](https://github.com/zxing-cpp/zxing-cpp): QR reading
- [jsQR](https://github.com/cozmo/jsQR): fallback QR reading
