# AEGIS — Behavioral Security Suite

**Cyber Security Engineering Department, College of Engineering, Al-Maaqal University, Basra**
Developed by Abdulaziz Adel Abdulqader, second-year student. Festival entry, 26 November 2026.

> Passwords leak. Rhythm doesn't. Neither do traitors.

## What it is
AEGIS is a web application with four security engines that run entirely in the browser. Nothing is sent to any server.

### 1. KeyPrint: keystroke-rhythm authentication
A password can be stolen; the way you type it cannot. KeyPrint records the timing of every key press (dwell time and flight time), learns the user's rhythm from five samples, and then rejects anyone who types the correct password with the wrong rhythm.

- Features: dwell time (key held) and flight time (gap between keys), measured with `performance.now()`.
- Model: per-feature mean and standard deviation.
- Calibration: leave-one-out testing sets a personal acceptance threshold.
- Decision: scaled Manhattan distance against that threshold.
- The enrolled profile is saved on the device, so a page refresh keeps it. "Start over" (clicked twice) deletes it.
- **Accuracy lab:** before each challenge, the operator marks who is typing (the owner or someone else). The lab reports the false rejection rate (owner wrongly rejected), the false acceptance rate (others wrongly accepted) and overall accuracy, and exports every attempt as CSV. Use it with 10 to 15 real people to publish measured results.

### 2. LeakHunter: invisible traitor tracing
Send one confidential message to many people. Each copy looks identical but carries the recipient's name encoded in zero-width Unicode characters. When the message leaks, paste it into LeakHunter and it names whose copy it was.

- Payload: header byte, recipient ID, name (UTF-8), CRC-8 checksum.
- Encoding: 2 bits per character using U+200B, U+200C, U+200D, U+2060, framed by U+2063.
- Redundancy: the signature is repeated across the message so partial copies still trace.
- **Layer 2 (back-up):** the copy number (8 bits + 4-bit check) is also written by swapping some Latin letters for identical-looking Cyrillic letters. These are visible characters, so the trace survives apps that delete invisible characters. A "worst case" switch in the demo strips all invisible characters to prove it. Layer 2 needs Latin letters, so it does not apply to messages written only in Arabic.

### 3. X-Ray: hidden-threat scanner
The defensive side of the same techniques. Paste any message, link or file name and X-Ray reports:
- invisible characters (zero-width and similar), decoding an AEGIS watermark if one is present;
- homoglyphs: words mixing letters from different alphabets (for example a Cyrillic "а" inside a Latin word);
- right-to-left override characters used to disguise file types ("fdp.exe" shown as "exe.pdf");
- risky links: "@" tricks, raw IP addresses, punycode, link shorteners, phishing-prone domains, plain http;
- double file extensions such as `.pdf.exe`.

### 4. Sentinel: always-on owner recognition
Up to three owners (for example two team members). For each owner Sentinel stores a typing phrase, a behavior profile and, on the published https site, a passkey.
- **Behavior profile:** a short calibration (14 targets to click and one sentence to type) measures mouse speed and its spread, path curvature and straightness, pauses, click hold time, key gaps and key hold time. Statistics come from sliding 8-second windows.
- **Recognition:** every second the last 12 seconds of real use are compared with each owner (z-score distance, smoothed). The header badge shows "Owner · name · %" or "Stranger". When two owners swap seats, Sentinel switches to the new owner.
- **Lock:** when no owner matches for five seconds while an owner is signed in, the site blurs and locks. Unlock with a passkey (fingerprint or face via WebAuthn; the signature is verified in the browser with Web Crypto) or with the owner's typing rhythm.
- **Admin console:** only a verified owner sees the security log, the lock setting and the sensitivity (relaxed, normal, strict). Guests can watch Sentinel but not change it.
- Limits: mouse behavior is weaker evidence than typing; tune sensitivity with real people. Passkeys need https (GitHub Pages). Without a server, the check is a demonstration of the method; a production version verifies passkeys on a server.

### Interactive hero
The landing screen is a live demo: the visitor types anything, and their real keystroke timings draw a rhythm fingerprint. A simulated intruder then types the same words with a different rhythm, the two fingerprints fail to match, and access is denied. The typed words then become a sealed document that leaks and is traced. With no typing, it plays a demo on its own.

## How to run
1. Open `index.html` in Chrome or Edge on a **laptop** (KeyPrint needs a physical keyboard).
2. Works fully offline: body and code fonts are embedded in the page. With internet, the headline font (Chakra Petch) loads from Google Fonts; offline it falls back to the embedded Tektur.
No installation, no server, no build step.

## Open on a phone (QR)
The QR button in the header (and the code in the footer) shows a QR code for the page's public address, generated on the device. When AEGIS runs from a file, paste the public address once (for example your GitHub Pages link) and the code appears. See `GITHUB-GUIDE-AR.md` to publish the site for free.

## Technologies
HTML5, CSS3 (animations, grid, custom properties), JavaScript (ES2020), SVG, Canvas 2D, Web Audio API, Unicode steganography, Unicode script analysis.

## Known limits
- KeyPrint is most accurate on a physical keyboard; phones report less timing data.
- A genuine user can occasionally be rejected when typing under stress, as with any biometric system.
- The intruder in the landing animation is simulated from the visitor's own rhythm; the real two-person test is in the KeyPrint section.
- LeakHunter traces copied or forwarded text. It cannot trace screenshots or text that was retyped by hand.

## Files
- `index.html`: the complete application.
- `DEMO-GUIDE.md`: step-by-step live demo script.
- `GITHUB-GUIDE-AR.md`: how to publish AEGIS on GitHub Pages (Arabic).
- `assets/aegis-crest.svg` and `assets/aegis-crest.png`: department crest (vector and 1200 px image).

## Credits and licenses
- QR code generator: Kazuhiko Arase, MIT license.
- Fonts: Instrument Sans, JetBrains Mono and Tektur, SIL Open Font License 1.1 (embedded subsets). Chakra Petch via Google Fonts, SIL Open Font License 1.1.
