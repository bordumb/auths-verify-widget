# `<auths-verify>` Web Component

[![npm version](https://img.shields.io/npm/v/auths-verify.svg)](https://www.npmjs.com/package/auths-verify)
[![license](https://img.shields.io/npm/l/auths-verify.svg)](https://github.com/bordumb/auths-verify-widget/blob/main/LICENSE)

A drop-in web component for decentralized commit verification — the decentralized equivalent of GitHub's green "Verified" badge. Powered by [Auths](https://github.com/bordumb/auths) cryptographic attestation verification via WASM.

## Quick Start

```html
<script type="module" src="https://unpkg.com/auths-verify/dist/auths-verify.mjs"></script>

<auths-verify
  attestation='{"version":1,"rid":"...","issuer":"did:keri:...","subject":"did:key:z...","device_public_key":"...","identity_signature":"...","device_signature":"...","revoked":false}'
  public-key="aabbccddeeff00112233445566778899aabbccddeeff00112233445566778899"
></auths-verify>
```

The widget loads WASM and verifies the attestation automatically on mount.

## Display Modes

### Badge (default)

Compact inline pill showing verification status.

```html
<auths-verify attestation="..." public-key="..." mode="badge"></auths-verify>
```

### Detail

Badge with an expandable panel showing the full attestation chain. Click the badge to expand.

```html
<auths-verify attestations="[...]" public-key="..." mode="detail"></auths-verify>
```

### Tooltip

Badge with a hover tooltip summarizing verification status.

```html
<auths-verify attestation="..." public-key="..." mode="tooltip"></auths-verify>
```

## Attributes

| Attribute | Type | Default | Description |
|---|---|---|---|
| `attestation` | JSON string | `""` | Single attestation to verify |
| `attestations` | JSON array string | `""` | Chain of attestations to verify |
| `public-key` | hex string | `""` | Root/issuer Ed25519 public key |
| `mode` | `badge\|detail\|tooltip` | `badge` | Display mode |
| `size` | `sm\|md\|lg` | `md` | Badge size |
| `wasm-url` | string | `""` | Optional WASM URL override |
| `auto-verify` | boolean | `true` | Verify on connect/attribute change |

## JavaScript API

```js
const el = document.querySelector('auths-verify');

// Trigger verification manually
await el.verify();

// Get the last verification report
const report = el.getReport();

// Listen for events
el.addEventListener('auths-verified', (e) => {
  console.log('Status:', e.detail.status.type);
  console.log('Chain:', e.detail.chain);
});

el.addEventListener('auths-error', (e) => {
  console.error('Error:', e.detail.error);
});
```

## Theming

All colors are overridable via CSS custom properties:

```css
auths-verify {
  --auths-verified-bg: #eef2ff;
  --auths-verified-fg: #3730a3;
  --auths-verified-border: #a5b4fc;
  --auths-font-family: 'Inter', sans-serif;
  --auths-border-radius: 6px;
}
```

Available properties: `--auths-{state}-bg`, `--auths-{state}-fg`, `--auths-{state}-border` for each state (`verified`, `invalid`, `expired`, `revoked`, `error`, `loading`, `idle`), plus `--auths-font-family`, `--auths-font-size`, `--auths-border-radius`, `--auths-detail-border-radius`.

## Development

### Prerequisites

- Node.js >= 18
- Rust 1.93+ with `wasm32-unknown-unknown` target (for WASM builds)
- [wasm-pack](https://rustwasm.github.io/wasm-pack/)
- The [auths](https://github.com/bordumb/auths) repo cloned alongside this one:
  ```
  auths-base/
  ├── auths/                  # main auths repo
  └── auths-verify-widget/    # this repo
  ```

### Setup

```bash
npm install
```

### Build WASM (requires Rust)

```bash
npm run build:wasm
```

### Run tests

```bash
# Unit tests (no WASM required — mocked)
npm test

# Type check
npm run typecheck
```

### Dev server

```bash
npm run dev
```

Opens the examples directory with hot reload via Vite.

### Production build

```bash
# Build WASM first
npm run build:wasm

# Build both full (inlined WASM) and slim (split WASM) bundles
npm run build
```

Outputs:
- `dist/auths-verify.js` — single file with WASM base64-inlined (~100-200KB gzipped)
- `dist/slim/auths-verify.js` — smaller JS, loads `.wasm` separately

### Visual testing

Open `tests/visual/index.html` in a browser to see all states and modes rendered side by side.

## Build Outputs

| File | Description |
|---|---|
| `dist/auths-verify.js` | CDN-ready single file (WASM inlined) |
| `dist/slim/auths-verify.js` | Smaller JS bundle (loads WASM separately) |

## License

MIT
