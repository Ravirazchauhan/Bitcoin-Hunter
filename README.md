# ⛓ Bitcoin Scanner — Puzzle & Early Wallet Hunter

> **v1.0.0-legacy** · Single-file browser tool for Bitcoin private key research

A self-contained, zero-dependency HTML tool for Bitcoin cryptographic research. Generates and checks Bitcoin private keys against live balances using multiple public APIs, with special support for the Bitcoin Puzzle challenge and early-era wallet patterns.

---

## ⚡ Quick Start

1. **Download** `bitcoin_scanner_legacy.html` from the [Releases](../../releases) page
2. **Open locally** — double-click the file or drag it into your browser (`file://` protocol)
3. Click **⚡ Test APIs** to verify which balance APIs are reachable from your machine
4. Choose a scan mode and press **▶ Start Scan**

> **Run locally for best results.** When hosted on a web server, browser CORS policies may block direct API calls. The tool will warn you automatically if this is the case.

---

## 🔍 Features

### Scan Modes
| Mode | Description |
|------|-------------|
| **Sequential** | Starts at key `#1` and increments. Position is saved to `localStorage` so you can resume between sessions. |
| **Random** | Cryptographically random keys across the full 256-bit secp256k1 space using `crypto.getRandomValues`. |
| **🎯 Puzzle** | Targets the [Bitcoin Puzzle](https://bitcointalk.org/index.php?topic=1306983.0) addresses — publicly funded addresses with mathematically constrained key ranges. Select a puzzle from the grid. |
| **📅 Era 2009–13** | Targets historically weak key patterns from early Bitcoin Core wallet generation: ultra-low keys, Unix timestamp seeds, 32/48-bit Randstorm ranges, and block-height entropy seeds. |

### Crypto Engine
All cryptography runs entirely in-browser with no external dependencies beyond two CDN libraries:
- **secp256k1** elliptic curve via [elliptic.js](https://github.com/indutny/elliptic)
- **SHA-256** via [js-sha256](https://github.com/emn178/js-sha256)
- **RIPEMD-160**, **Base58Check**, and **WIF encoding** implemented inline

### API Layer
Balance checks use a rotating, auto-fallback strategy across three free public APIs:

| API | Notes |
|-----|-------|
| `blockchain.info` | Supports batch queries of up to 100 addresses. ~300 req/5min. Best default. |
| `blockcypher` | Per-address. ~200 req/hour free tier — strict limits. |
| `blockchair` | Per-address. ~30 req/min free tier. |

All APIs are accessed with CORS proxy fallback (`corsproxy.io`) when direct requests are blocked.

### Other Features
- **Manual Check** — paste any Bitcoin address or 64-char hex private key for instant lookup
- **Batch Check** — paste up to 20 addresses at once
- **Finds tab** — all hits (balance > 0 or any tx history) saved to `localStorage`
- **Export** — download finds as JSON or CSV, full scan history as CSV
- **Rate & delay controls** — configurable batch size (1–10) and scan delay (500ms–4s)
- **Progress bar** — tracks position within a defined key range

---

## 🎯 Bitcoin Puzzle

In 2015, an anonymous creator funded 256 Bitcoin addresses. Each puzzle `#N` has its private key constrained to exactly `N` bits:

```
Range: 2^(N-1)  →  2^N - 1
```

Puzzles `#1–#70` and several others have been solved. Remaining unsolved puzzles hold their number in BTC (e.g. puzzle `#75` = 7.5 BTC). The constrained ranges make targeted scanning computationally feasible for lower-numbered puzzles.

**Solved puzzles** are displayed in the grid for reference but cannot be selected as targets.

---

## ⚙️ Configuration Tips

- **Recommended settings for sustainable scanning:** Delay `1s`, Batch `3`, API `Auto`
- **Run ⚡ Test APIs first** — it checks all three sources against the Genesis block address and shows which are reachable from your network
- **Set a custom range** using the Start/End hex inputs and click **Set Range** before starting
- **Resume position** is auto-saved to `localStorage` in sequential mode — just re-open the file to continue

---

## 🌐 Running from a Web Server (CORS)

If you host this file on a web server rather than opening it locally, browser CORS restrictions will block direct API calls. The tool detects this automatically and shows a warning. In this case:

- The CORS proxy (`corsproxy.io`) is used as fallback — this may be slower or rate-limited
- For reliable API access, always prefer running the file locally via `file://`

---

## ⚠️ Legal & Ethical Disclaimer

This tool is provided **for educational and cryptographic research purposes only**.

- The Bitcoin 256-bit keyspace contains ~10⁷⁷ possible keys. The probability of finding a funded wallet by random scanning is effectively zero outside of mathematically constrained ranges (like the Puzzle).
- **You must not** attempt to access, transfer, or claim funds from Bitcoin addresses that do not belong to you. Unauthorised access to computer systems and theft are criminal offences in most jurisdictions.
- The Bitcoin Puzzle is a publicly announced research challenge. All other scanning targets should be addresses you own or have explicit permission to test.
- The authors of this tool accept no liability for misuse.

---

## 🏗️ Technical Notes

### Self-Contained Single File
The entire tool — HTML, CSS, JavaScript, and all cryptographic primitives — is one `.html` file. No build step, no npm, no server. Two small libraries are loaded from CDN:

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/elliptic/6.5.4/elliptic.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/js-sha256/0.9.0/sha256.min.js"></script>
```

If you need fully offline operation, download these scripts and embed them inline before the closing `</body>` tag.

### Key Derivation
For each private key integer `n`:
1. Derive uncompressed and compressed public keys via secp256k1
2. Hash: `SHA256 → RIPEMD160` to get the public key hash
3. Apply `Base58Check` with version byte `0x00` → Bitcoin address
4. Encode private key as WIF (uncompressed and compressed variants)

### localStorage Keys
| Key | Contents |
|-----|----------|
| `bth1_finds` | JSON array of all hits |
| `bth1_all` | JSON array of last 10,000 scanned records |
| `bth1_pos` | Hex string of current sequential scan position |

---

## 📋 Changelog

### v1.0.0-legacy
- Initial public release
- Sequential, Random, Puzzle, and Era 2009–13 scan modes
- Auto-fallback API layer (blockchain.info / blockcypher / blockchair)
- Bitcoin Puzzle grid (#71–#130 unsolved, #66–#70 solved reference)
- Era ranges: ultra-low keys, Unix timestamps, Randstorm 32/48-bit, block-height seeds
- Manual single and batch address/key checker
- JSON and CSV export
- localStorage persistence for finds and scan position
- CORS auto-detection (warning only shown when not running locally)

---

## 📄 License

MIT — see [LICENSE](LICENSE) for details.
