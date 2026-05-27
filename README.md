# MultiTalk

A browser-based push-to-talk walkie-talkie using WebRTC / PeerJS. Open the same URL on multiple devices, pick a role, share the room code, and talk.

> **HTTPS is required.** Browsers block microphone access on plain HTTP. The setup below creates a locally-trusted certificate so you get no security warnings.

---

## Local dev — trusted HTTPS with mkcert + http-server

### 1. Install prerequisites (once)

```bash
# mkcert — creates locally-trusted certs
brew install mkcert        # macOS
# or: choco install mkcert (Windows), apt install mkcert (Ubuntu 24.04+)

# http-server — zero-config static file server
npm install -g http-server
```

### 2. Trust the local CA (once per machine)

```bash
mkcert -install
```

This adds mkcert's CA to your system/browser trust store. Only needs to be done once.

### 3. Generate a certificate for localhost

Run this inside the repo:

```bash
mkcert localhost 127.0.0.1 ::1
```

This creates two files:

```
localhost+2.pem       ← certificate
localhost+2-key.pem   ← private key
```

### 4. Serve the app

```bash
http-server . \
  --ssl \
  --cert localhost+2.pem \
  --key  localhost+2-key.pem \
  -p 8443 \
  -o           # opens browser automatically
```

Then open **https://localhost:8443** — no certificate warning.

### Tip: other devices on the same network

Find your machine's local IP (e.g. `192.168.1.42`) and regenerate the cert to include it:

```bash
mkcert localhost 127.0.0.1 ::1 192.168.1.42
```

Re-run the `http-server` command, then on the other device open **https://192.168.1.42:8443**.  
The remote device also needs the mkcert CA installed — [see mkcert docs](https://github.com/FiloSottile/mkcert#mobile-devices) for mobile.

---

## Quick reference

| Step | Command |
|------|---------|
| Install CA (once) | `mkcert -install` |
| Generate cert | `mkcert localhost 127.0.0.1 ::1` |
| Serve | `http-server . --ssl --cert localhost+2.pem --key localhost+2-key.pem -p 8443 -o` |

---

## How it works

- **PeerJS** (loaded from CDN) handles WebRTC signalling via the public PeerJS cloud server.
- One participant creates a room and shares the 4-character code; others join with it.
- Audio is transmitted peer-to-peer — no media passes through any server.
