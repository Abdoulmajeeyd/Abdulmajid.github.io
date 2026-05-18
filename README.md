# ANONYCHAT

> Anonymous · Encrypted · Free

A privacy-first anonymous chat platform with end-to-end encryption, group chats, and encrypted media sharing. Built as a Progressive Web App — installs directly on any phone, no app store required.

---

## Features

### Security
- **End-to-end encryption** on all private and group messages
- **ECDH P-256** key exchange — shared secrets never leave your device
- **AES-GCM 256-bit** message encryption
- **Encrypted image sharing** — images encrypted before leaving your device
- Private keys stored only in browser/device storage — never sent to any server
- Firebase only ever stores ciphertext — zero plaintext exposure

### Chat
- **Global Chat** — public room, open to all users
- **Private Chat** — 1-on-1 E2E encrypted conversations
- **Group Chat** — encrypted group rooms, each with a unique group key distributed securely to members
- Real-time messaging via Firebase Realtime Database
- Online/offline presence indicators
- Message deletion
- Typing-friendly — send with Enter key

### Media
- Send images in any chat
- Private and group images are **encrypted before upload** — only recipients can view them
- Fullscreen image viewer
- Global chat supports standard image uploads via Firebase Storage

### App
- **Progressive Web App (PWA)** — installable on Android and iOS from the browser
- Works fullscreen with no browser UI
- Offline-capable via service worker caching
- Install prompt built-in

---

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | Vanilla JS, HTML5, CSS3 |
| Auth | Firebase Authentication |
| Database | Firebase Realtime Database |
| Storage | Firebase Storage |
| Encryption | Web Crypto API (built-in, no library) |
| Hosting | Vercel |
| App format | PWA (Progressive Web App) |

---

## Encryption Architecture

```
PRIVATE CHAT
  Alice's ECDH private key + Bob's ECDH public key
  → Derive shared AES-256 key (ECDH)
  → Encrypt message with AES-GCM (random IV per message)
  → Store ciphertext in Firebase
  → Bob derives same key independently → decrypts

GROUP CHAT
  Group creator generates random AES-256 key
  → Encrypt group key for each member using their ECDH shared key
  → Store each member's wrapped key in Firebase
  → Each member unwraps their copy → decrypts group messages

IMAGES
  Same AES-256 key used to encrypt raw image bytes
  → Store encrypted binary in Firebase
  → Recipient decrypts and renders locally
```

---

## Project Structure

```
/
├── index.html        ← Full app (single file PWA)
├── manifest.json     ← PWA manifest (name, icons, theme)
├── sw.js             ← Service worker (offline caching)
├── icon-192.png      ← App icon (192×192)
└── icon-512.png      ← App icon (512×512)
```

---

## Firebase Setup

### Realtime Database Rules
```json
{
  "rules": {
    "publicKeys":     { ".read": "auth != null", ".write": "auth != null" },
    "globalMessages": { ".read": "auth != null", ".write": "auth != null" },
    "privateChats":   { ".read": "auth != null", ".write": "auth != null" },
    "groupMessages":  { ".read": "auth != null", ".write": "auth != null" },
    "groups":         { ".read": "auth != null", ".write": "auth != null" },
    "groupKeys":      { ".read": "auth != null", ".write": "auth != null" },
    "users":          { ".read": "auth != null", ".write": "auth != null" },
    "profiles":       { ".read": "auth != null", ".write": "auth != null" },
    "status":         { ".read": "auth != null", ".write": "auth != null" }
  }
}
```

### Firebase Storage Rules
```
rules_version = '2';
service firebase.storage {
  match /b/{bucket}/o {
    match /{allPaths=**} {
      allow read, write: if request.auth != null;
    }
  }
}
```

---

## Install as App

**Android (Chrome)**
1. Open `https://anonychat-phbh.vercel.app` in Chrome
2. Tap the install banner or 3-dot menu → "Add to Home Screen"

**iPhone (Safari)**
1. Open `https://anonychat-phbh.vercel.app` in Safari
2. Tap Share → "Add to Home Screen"

---

## Important Notes

- **Private keys never leave your device.** If you clear browser data or switch devices, you lose access to past encrypted messages. Use "Regenerate Keys" in Profile only if necessary.
- **Global Chat is not encrypted** — it is a public room visible to all authenticated users.
- **Encrypted images** are limited to ~900KB due to Firebase Database payload limits. Global chat images have no such limit.
- All users must be logged in at least once before they can receive encrypted messages (their public key must be registered).

---

## Live App

[https://anonychat-phbh.vercel.app](https://anonychat-phbh.vercel.app)

---

## License

MIT — free to use, modify, and distribute.
