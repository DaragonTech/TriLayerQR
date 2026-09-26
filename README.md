# TriLayerQR

**One QR code was never enough. So we stacked three. A new kind of QR for the AI age was born. TriLayerQR puts a photo, a title, a description and pages of text inside a single scannable code.**

TriLayerQR is a high-capacity code that stacks three QR codes into one. The green layer carries a compressed photo with its title, description and footer; the red and blue layers carry a memo, stored twice so it reads reliably. The TriLayerQR scanner reads the layers and turns them back into the photo, the text and a shareable card. At default settings, a standard QR holds 2.3 KB; TriLayerQR offers **about 4.6 KB** of storage in the same grid, twice as much. The memo gets 2.3 KB of it, and because text is compressed before it's stored, that fits about 6–7 KB of text, roughly 1,000 words. No memo? The free space makes the picture sharper and twice as large instead. Everything is optionally encrypted and decoded without ever touching a server.

Built by [DaragonTech](https://daragon.tech).

---

## Why TriLayerQR exists

It started with one question at DaragonTech: **can you jack an entire AI persona into a single QR code?**

A persona is three things: a **face** to wear, **settings** that describe it, and a **prompt** that decides how it thinks and speaks. A standard QR can barely hold the face. So we split the signal into three layers:

- **the image layer** carries the avatar, its name, its description and its footer;
- **the memo layers** carry the prompt and settings, compressed to fit;
- **the password** locks the whole ghost inside until you choose to set it free.

It didn't stay an experiment. The first internal versions of DaragonTech's AI executives (the AI agents that run parts of our work) lived inside these codes: each persona, with its face, settings and prompt, stored in a single TriLayerQR and brought to life with one scan.

Then the idea escaped the lab. While DaragonTech built across its other projects, including its own agent platform, we saw what this format really was: a portable, printable, offline data vault with a face on it. Too useful to keep behind the firewall. **So we open-sourced it.**

---

## Features

- **Three-layer signal.** Three Version-40 QR codes fused into one 4-color grid, *twice the payload of a standard QR in the same square*.
- **A face in the code.** Photos squeezed into ~2.3 KB with real WebP compression, then restored and enhanced on decode.
- **Optional memo.** 2.3 KB of red-and-blue storage that holds **about 6–7 KB of text**, because the memo is compressed before it's stored. Roughly 1,000 words, invisible to ordinary scanners.
- **Detail layer.** No memo, or a short one? The free space stores the fine detail of the picture, so the card shows it **twice as large and clearly sharper**.
- **Back image.** An optional second picture for the back of the card, sharing the memo space with the text.
- **Flip card.** The card turns over to show the sharper picture or back image, with the memo beside it in a scrollable panel, in the editor and the scanner.
- **Face lock-on.** A local neural face detector finds the largest face and centers the circular avatar on it: *head, hair, shoulders, framed*.
- **AES-256 blackout.** One password encrypts everything: image, caption, description, footer, memo and pictures.
- **Zero-cloud decoding.** Encoding, scanning, face detection and decryption all run in your browser.
- **Scans straight off a screen.** Tested with a phone camera pointed at a computer monitor: the image, the memo and the pictures all come through.
- **Trading-card output.** Every code drops as a 700 × 440 neon card, as PNG or PDF, ready to print, post or trade.

## What's inside a code

| Part | Where it lives | Who can read it |
|---|---|---|
| **Photo** | Image layer (green) | TriLayerQR scanner |
| **Caption** (title) | Image layer | TriLayerQR scanner |
| **Description** | Image layer | TriLayerQR scanner |
| **Footer** | Image layer | TriLayerQR scanner |
| **Memo** (long text) | Memo layers (red + blue, same data in both) | TriLayerQR scanner, from the camera, a photo or a PDF |
| **Detail layer** *(automatic)* | Free memo space | TriLayerQR scanner, which shows the sharper picture |
| **Back image** *(optional)* | Memo space, shared with the memo | TriLayerQR scanner, on the back of the card |
| **Password** (optional) | Encrypts all of the above | Only people who know it |

Standard phone scanners still detect the image layer as a normal QR code; the TriLayerQR scanner turns its data back into the photo and text.

---

## How it works

**Three QR codes in one.** Every color on screen is a mix of red, green and blue. TriLayerQR prints three separate Version-40 QR codes, one in each color channel:

- **green:** the image, caption, description and footer
- **red:** the memo
- **blue:** the same memo again

These are color *channels*, not the colors you see. Every square mixes the three channels, and because red and blue carry the same data they always switch on and off together. So each square is one of just 4 colors:

| Image layer (green) | Memo (red + blue) | Square |
|---|---|---|
| light | light | white |
| dark | dark | black |
| light | dark | green |
| dark | light | magenta (red + blue) |

That's a deliberate choice. Blue is the channel cameras and image compression damage most, so instead of giving it its own data, TriLayerQR reads the memo from red and blue together, and it survives phone cameras and compressed images far better. Ordinary scanners see the code in grayscale, which is ruled by green, so they still lock onto the image layer. When the memo space is unused, the code is a normal black-and-white QR.

**Squeezing a face into 2.3 KB.**
- **Automatic best fit:** the editor finds the largest picture that fits, then raises the quality as far as the remaining space allows.
- **Circular avatars** skip the corners (about 21% less data, optional).
- **Raw binary storage** avoids the 25% overhead of text encodings.
- **Decoder enhancement:** the editor picks the best clean-up filter (smoothing, undithering, deblocking or pixel-art edges) and the scanner applies it automatically.

Result: a clean portrait of about **100–190 pixels** across, pulled out of a few kilobytes.

**The detail layer.** When the memo is empty or short, its free space isn't wasted on a second copy of the photo. It stores only what the basic picture is missing: the difference between the photo at twice the size and the basic picture enlarged. The scanner enlarges the basic picture the same way and adds the detail back, so a 112-pixel portrait becomes a sharp **224-pixel** one. If the color layers can't be read, the card simply shows the basic picture. It works with the WebP modes and can be switched off in **Advanced options** for a plain black-and-white QR.

**Compressing the memo.** Text is compressed before it's stored. Normal writing shrinks to about a third of its size, so 2.3 KB of memo space holds about 6–7 KB of text.

**Back image.** Pick a second picture in the Memo tab and it becomes the back of the card. It shares the memo space with the text: the memo is stored first and the back image gets the room that's left, at the best size and quality that fit. A short memo barely affects it; a long one leaves less room.

### Scanning from a screen

TriLayerQR was tested with a phone camera pointed at a computer monitor, and it reads the full code: image layer, memo and pictures. Color QR codes usually struggle with this, so here is why this one works:

- **A screen speaks the code's own language.** Every pixel on a monitor is made of tiny red, green and blue lights, the exact three channels TriLayerQR writes to. Each square is shown with its channels fully on or fully off, so the colors are as pure and saturated as a screen can make them.
- **It makes its own light.** A screen doesn't depend on room lighting or ink quality, so the contrast between green, magenta, black and white stays strong, even in a dim room.
- **The camera sees the same channels.** Phone camera sensors also split light into red, green and blue, so each layer lands cleanly in its own channel instead of blending into its neighbors.
- **Only 4 well-separated colors.** Green and magenta are opposites, differing in both hue and brightness, which makes them hard to confuse. And because the memo is written twice, in red and in blue, the scanner averages the two for a cleaner signal.
- **The scanner is built for cameras.** It alternates between the full frame and a zoomed-in center, collects the memo across several frames, and, once the image layer is read, uses its known pattern to line up the grid and sample each square at its center, even when the camera blurs the colors a little.

**Tips for screen scanning:** turn the screen brightness up, and switch off Night Shift, Night Light or any blue-light filter, which shift the colors. Fill most of the camera frame with the code. If you see wavy stripes (moiré), move the phone slightly closer or farther away, or tilt it a little.

### Storage modes

Pick your aesthetic.

| Mode | How it stores | Vibe |
|---|---|---|
| **WebP color** *(default)* | Real image compression | Full-color portraits, max quality per byte |
| **WebP grayscale** | Compression, no color | Bigger, moodier monochrome shots |
| **1-bit black/white** | 1 bit per pixel, threshold or dither | Stencil art, logos, bold street-poster looks |
| **4-level grayscale** | 2 bits per pixel, dithered | Retro terminal, newsprint grit |
| **16-level grayscale** | 4 bits per pixel | Smooth tones, compact frame |
| **4×4 block codebook** | 12 bits per block (0.75 bits per pixel) | The biggest image, soft-focus detail |
| **8 / 16-color** | Palette ripped from the image | Pixel-art, arcade, 8-bit nostalgia |

The detail layer applies to the two WebP modes; the pixel-art modes keep their look.

### Face lock-on

Circle mode is on by default, and it doesn't just crop the middle. It **hunts for faces**.

- Runs [MediaPipe Face Detection](https://github.com/google-ai-edge/mediapipe) *locally*, in your browser. The photo never leaves the device.
- Locks onto the **largest face** and frames head, hair and a hint of shoulders.
- No face or no detector? It falls back to the center, no drama.
- Prefer manual control? Kill it in **Advanced options**.
- Circle off? The image is cut to a square from the top, keeping heads, dropping feet.

### The card

Every encode also mints a **700 × 440 trading card**:

- the photo, caption, description and footer on the left, with the scannable QR on the right;
- a glowing **capacity meter** showing total data stored and how much space is still free;
- a **"Scan me with:"** line pointing to your scanner. It's printed on the card only, never stored in the code;
- a **flip side**: when the code carries a memo, a sharper picture or a back image, "Flip card" turns the card over. The picture sits on the left and the memo on the right, in a translucent panel you can scroll. The flip is on screen only; downloads are always the front;
- a **glass reflection** that sweeps across the card every few seconds (on screen only).

Download it as **PNG** or **PDF**. The scanner rebuilds the exact same card from any code it reads, and opens card PNGs and PDFs directly.

**Sharing through messaging apps.** Apps like WhatsApp re-compress photos, which washes out the memo colors. Send the card as a **PDF** (or the PNG as a document) and every pixel arrives intact. The scanner also recovers what it can from compressed copies, but the PDF is the safe route.

---

## Capacity

| | Default error correction (M) | Maximum (L) |
|---|---|---|
| Image layer | 2.3 KB | 2.9 KB |
| Memo | 2.3 KB stored (about 6–7 KB of text) | 2.9 KB stored (about 8–9 KB of text) |
| **Whole code** | **about 4.6 KB** | **about 5.8 KB** |
| Standard QR, for comparison | 2.3 KB | 2.9 KB |

Caption, description and footer ride in the image layer, so longer text means a slightly smaller face. A password costs 44 bytes.

---

## Security

- **AES-256-GCM** with a fresh random salt and nonce for every code, and PBKDF2 key stretching (250,000 rounds).
- **Everything local.** No uploads, no accounts, no tracking.
- **Use a real passphrase.** A printed code can be copied and attacked offline. *Short passwords break; long passphrases don't.*

---

## Example uses

- **AI persona cards:** a face on the front, the full prompt jacked into the memo. *Where it all began.*
- **Business cards:** portrait and title up front, contacts and bio in the dark layers.
- **Pet and luggage tags:** "if found, scan me", with owner details on board, no internet needed.
- **Encrypted greeting cards:** a photo outside, a private letter inside, unlocked by one password.
- **Photo archives:** stamp the back of a print with the story, the names and the date.
- **Collectible and game cards:** character art on the front, a second picture on the back, stats and lore in the memo.
- **Event badges:** faces, names, bios and schedules in one scan.
- **Museum labels:** the artwork and its catalogue text, readable offline.
- **Equipment tags:** a photo of the machine and its key maintenance notes, welded to the machine itself.
- **Puzzles and treasure hunts:** a hint image, and the next clue sealed behind a password.

---

## Getting started

No install, no build, no dependencies to wrangle. Just two self-contained web pages:

- **Editor** (`trilayerqr.editor.v1.html`): create, decode and re-edit codes. Feed it a photo, a caption, a description, a footer, a memo and an optional back image, then extract the QR or the card.
- **Scanner** (`trilayerqr.scanner.v1.html`): point a camera, or open an image or a card PDF, enter the password if there is one, and watch the card, image and memo materialize.

The scanner needs **HTTPS** (or `localhost`) for camera access. Host it, and print its address on your cards.

---

## Browser support

| | Editor | Scanner |
|---|---|---|
| Chrome, Edge (desktop and Android) | ✅ | ✅ |
| Firefox | ✅ | ✅ |
| Safari (macOS, iOS 14+) | ✅ except WebP modes and the detail layer | ✅ |

On first use, the pages load a few libraries from public CDNs: QR generation and reading (ZXing), face detection (MediaPipe) and the card fonts. After that the browser serves them from its cache.

---

## Limitations

- **Thumbnail faces:** built for avatars and portraits, not billboards.
- **Memo needs close range:** phone cameras capture color at lower detail than brightness, so fill the frame. Screens work best (see *Scanning from a screen*), followed by good color prints.
- **Compressed copies lose color:** messaging apps and social networks re-compress images. Share the card as a PDF, or the PNG as a document.
- **Dense grid:** Version-40 codes are detailed. Print at 5 cm or bigger, flat and well lit.
- **Not for life-critical data:** don't trust your medical or legal records to an experimental format.

---

## Credits

TriLayerQR is developed by [DaragonTech](https://daragon.tech).

It builds on excellent open-source work:
- [qrcode-generator](https://github.com/kazuhikoarase/qrcode-generator): QR code generation
- [zxing-wasm](https://github.com/Sec-ant/zxing-wasm) / [ZXing-C++](https://github.com/zxing-cpp/zxing-cpp): QR reading
- [jsQR](https://github.com/cozmo/jsQR): fallback QR reading
- [MediaPipe Face Detection](https://github.com/google-ai-edge/mediapipe): face-centered avatars
- [Orbitron](https://fonts.google.com/specimen/Orbitron) and [Rajdhani](https://fonts.google.com/specimen/Rajdhani): card typography

---

## License

Released under the [MIT License](LICENSE). Fork it, remix it, build on it.

> **TriLayerQR** by [DaragonTech](https://daragon.tech)
