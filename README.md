# 📡 Network Logger – HAR Exporter

> Capture browser network requests across all tabs and export them as a HAR file — **without opening DevTools**.

[![Chrome Web Store](https://img.shields.io/badge/Chrome%20Web%20Store-Install-blue?logo=google-chrome&logoColor=white)](https://chromewebstore.google.com/detail/godhingolimlfgekelfpmkomgnfgngek)
[![Manifest V3](https://img.shields.io/badge/Manifest-V3-green)](https://developer.chrome.com/docs/extensions/mv3/intro/)

---

## What it does

Capture network requests across all tabs and export as a HAR file — no DevTools needed. Perfect for debugging and sharing logs.

**Key difference from DevTools:** DevTools must be open and on the correct tab *before* the request fires. Network Logger captures traffic from all tabs the moment you click Start, even if DevTools has never been opened.

---

## Features
### v1.0.0 
- 🔴 **One-click recording** — Start/Stop from the extension popup
- 📦 **Full bodies** — Request payloads (POST/PUT) and response bodies via CDP
- ⬇ **Standard HAR 1.2** — Loads in Chrome DevTools, Charles Proxy, Fiddler, Postman
- 🔒 **100% local** — No server, no analytics, no data ever leaves your device
- ⚡ **Zero page impact** — No script injection, no DOM modification
- 🕐 **Precise timings** — Send, wait (TTFB), receive in milliseconds
- 🌐 **All tabs** — Captures traffic across every open tab simultaneously
- 💾 **Memory-safe** — Capped at 50,000 entries; cleared on Stop or Close

### 🆕 v1.1.0 

- 📝 **Custom HAR filename** — name your export based on the flow you're testing (e.g. `login-flow.har`, `checkout-bug.har`). Leave blank for auto-generated timestamp name.

  | Input | Output |
  |-------|--------|
  | _(empty)_ | `network-log-2026-05-11T14-30-00.har` |
  | `login-flow` | `login-flow.har` |
  | `checkout bug` | `checkout bug.har` |
  | `test<>file` | `test__file.har` (invalid chars sanitized) |

- 🛡️ **Sensitive data scrubbing** — toggle ON to automatically redact Authorization headers, Cookies, JWT tokens, API keys, passwords, and secrets before export. Safe to share HAR files with your team.

  | What's Scrubbed | Example |
  |-----------------|---------|
  | **Auth headers** | `Authorization: Bearer eyJ...` → `Authorization: [REDACTED]` |
  | **Cookies** | `Cookie: session=abc123` → `Cookie: [REDACTED]` |
  | **Set-Cookie** | `Set-Cookie: token=xyz` → `Set-Cookie: [REDACTED]` |
  | **Cookie arrays** | `{ name: "session", value: "abc" }` → `{ name: "session", value: "[REDACTED]" }` |
  | **JWT tokens** | `eyJhbGciOi...` → `[REDACTED]` |
  | **JSON secrets** | `"password": "abc123"` → `"password": "[REDACTED]"` |
  | **URL params** | `?api_key=xyz` → `?api_key=[REDACTED]` |
  | **URL-encoded** | `password=abc123` → `password=[REDACTED]` |
- 🔢 **Live badge counter** — see the request count directly on the toolbar icon without opening the popup( Note-pin the extension to see in toolbar ). Red while recording, green when stopped with data.

  | State | Badge | Color |
  |-------|-------|-------|
  | Idle, no data | _(empty)_ | — |
  | Recording | `47` | 🔴 Red |
  | Stopped with data | `47` | 🟢 Green |
  | Cleared | _(empty)_ | — | 


---

## Installation

### From the Chrome Web Store (recommended)

1. Visit the [Chrome Web Store listing](https://chromewebstore.google.com/detail/network-logger-har-export/godhingolimlfgekelfpmkomgnfgngek)
2. Click **"Add to Chrome"**
3. Click **"Add extension"** in the confirmation dialog
4. The 📡 icon will appear in your Chrome toolbar



---

## How to use

### Step 1 — Start recording

Click the 📡 icon in your Chrome toolbar to open the popup, then click **Start Recording**.

> A yellow **"Chrome is being debugged"** bar will appear — this is Chrome's built-in security notification that the DevTools Protocol is active. It disappears automatically when you stop recording.

### Step 2 — Browse normally

Use your browser as usual. The popup shows a live counter of captured requests. You can close the popup — recording continues in the background.

### Step 3 — Stop recording

Click **Stop Recording** in the popup. The debugger detaches from all tabs and the yellow bar disappears.

### Step 4 — Export as HAR

Click **Export as HAR**. A file named `network-log-YYYY-MM-DDTHH-MM-SS.har` is saved to your Downloads folder.

### Step 5 — Load in DevTools (optional)

1. Open Chrome DevTools with `F12` or `Cmd+Option+I`
2. Click the **Network** tab
3. Drag-and-drop the `.har` file onto the request list
4. All requests appear with full headers, bodies, and timing data

---

## What's captured in the HAR

| Field | Details |
|---|---|
| Request line | Method, full URL, HTTP version, query params |
| Request headers | All headers incl. `Authorization`, `Cookie` |
| Request body | POST/PUT payloads — JSON, form data, binary |
| Response status | HTTP status code and status text |
| Response headers | All response headers incl. `Set-Cookie` |
| Response body | Full content — text, JSON, HTML; binary as base64 |
| Timing | Send, wait (TTFB), receive in milliseconds |
| Protocol | HTTP/1.1, h2, h3 per request |

---

## Permissions

| Permission | Why it's needed |
|---|---|
| `debugger` | Attaches CDP to observe network traffic and retrieve response bodies. Active only while recording. |
| `downloads` | Saves the `.har` file to your local disk. |
| `storage` | Persists only the on/off state across service worker restarts. No user data stored. |
| `offscreen` | Chrome MV3 requirement to perform anchor-element download preserving `.har` extension. |
| `<all_urls>` | Allows the debugger to attach to tabs on any domain. No script injection; no DOM reads. |

---

## ⚠️ Security notice

> HAR files contain **passwords, session tokens, API keys, cookies, and other sensitive data** transmitted over the network. **Do not share HAR files publicly** or with untrusted parties without scrubbing sensitive values first.

---

## FAQ

**Why does a yellow bar appear when I start recording?**
This is Chrome's built-in security notification for any extension using the DevTools Protocol API. It cannot be suppressed — it's intentional browser behavior. It disappears automatically when you stop.

**Does this send my data anywhere?**
No. All data stays in your browser's memory. The extension makes zero outbound network requests. Data is discarded when you clear or close the browser.

**Why can't I see some response bodies?**
Some responses aren't bufferable by CDP: very large streaming responses, WebSocket frames, Server-Sent Events, and some service-worker-cached responses. Headers and timing for these are still captured.

**What Chrome version is required?**
Chrome 116 or later (uses Manifest V3 + `chrome.offscreen` API).

**Is it compatible with other HAR tools?**
Yes — HAR 1.2 spec-compliant. Works with Chrome DevTools, Firefox DevTools, Charles Proxy, Fiddler, Postman, [Google HAR Analyzer](https://toolbox.googleapps.com/apps/har_analyzer/), and [HAR Viewer](https://www.softwareishard.com/har/viewer/).

---

## Privacy

All captured data is stored in-memory only and is **never transmitted** to any server. See the full [Privacy Policy](https://themindfuel-ai.github.io/network-logger/privacy-policy.html).

The use of information received from Chrome APIs adheres to the [Chrome Web Store User Data Policy](https://developer.chrome.com/docs/webstore/program-policies/limited-use), including the Limited Use requirements.

---

## Contributing


Please open an [issue](https://github.com/themindfuel-ai/network-logger/issues) before starting large changes.

---

<p align="center">
  <a href="https://chromewebstore.google.com/detail/godhingolimlfgekelfpmkomgnfgngek">Chrome Web Store</a> ·
  <a href="https://themindfuel-ai.github.io/network-logger/">Website</a> ·
  <a href="https://themindfuel-ai.github.io/network-logger/privacy-policy.html">Privacy Policy</a> ·
  <a href="https://github.com/themindfuel-ai/network-logger/issues">Report an Issue</a>
</p>
