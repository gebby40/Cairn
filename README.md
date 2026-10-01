# Cairn

**A private social network and messenger. No accounts, no server that knows who you are, end-to-end encryption for messages, groups, files and voice calls.**

Cairn is built so that nobody — not even the people running it — can read your messages or map out who you talk to. There are no accounts and no passwords stored on a server: your identity is a key pair that lives on your phone, every post and message is signed by it, and relays simply pass encrypted data along without knowing what it is or who it's between. Direct messages, groups, file sharing and voice calls are all end-to-end encrypted, and public posts are hash-anchored so edits and deletions are tamper-evident.

Think of it as the feature set of Mastodon or Signal, rebuilt on a foundation where privacy comes from the data format itself rather than from a server's promise to behave.

> **Early access.** Cairn is in active development and is being tested by a small group. Expect rough edges. It has not been independently audited — don't rely on it for anything where your safety depends on it.

## Try it

| | |
|---|---|
| **Android** | Download [`cairn-debug-arm64.apk`](https://github.com/gebby40/Cairn/raw/main/releases/cairn-debug-arm64.apk) (Android 8.0 or newer, 64-bit phones). Install guide and checksum in [docs/install-android.md](docs/install-android.md). |
| **Browser** | [app.cairnnet.com](https://app.cairnnet.com) — the same network from a desktop or phone browser. |
| **Website** | [cairnnet.com](https://cairnnet.com) |

The Android app and the web app talk to the same relay and interoperate: you can message, share files and make voice calls between a phone and a browser, and move one identity between devices.

## What you get

- **Posts, replies, follows, timelines, tags, search** — the familiar social feed. Posts are public or unlisted; anything meant for a closed audience goes in an encrypted group. Who you follow stays on your device — it's never published.
- **Direct messages** — end-to-end encrypted with a double-ratchet protocol (the same family of cryptography as Signal). Delivered through a per-day mailbox so the relay can't tell who is messaging whom.
- **Groups** — encrypted group chats using the MLS standard (RFC 9420), with forward secrecy and post-compromise security as members come and go.
- **File sharing** — photos, videos and documents are encrypted on your device, uploaded in chunks, and only the recipients hold the key. The relay stores ciphertext it cannot read.
- **Voice calls** — end-to-end encrypted WebRTC calls between personas, phone ↔ phone or phone ↔ browser. Audio is relayed by default so the other party never sees your IP address. A safety code lets both people confirm nobody is in the middle. Calls ring on Android even with the app closed and the screen off — without Google services.
- **One identity on every device** — link a phone and a browser by QR code; your data syncs through an encrypted vault the relay cannot open.
- **Contact sharing** — share your Cairn contact by link, email, QR code, or tap-to-share (NFC).
- **Tamper-evident public content** — public posts are periodically anchored into a hash chain, so a relay can't quietly alter or remove history without it being detectable.

## How it works, briefly

1. **Your identity is a key.** Creating a persona generates a signing key pair on your device. Your persona ID is derived from the public key. There is no username/password, no email, no phone number. A passphrase protects the key on your device, and a backup file lets you restore it.
2. **Everything is a signed object.** A post, a reply, a boost, a profile update, a message delivery — each is a small, signed, content-addressed object. Anyone can verify it came from you; nobody can forge or alter it.
3. **Relays are interchangeable plumbing.** A relay stores and forwards objects, serves timelines, and hands encrypted mailboxes to whoever can prove they own them. It holds no accounts, no readable social graph for private content, and no IP logs. Relays can federate into a mesh, and you can switch relays without losing anything.
4. **Private things are encrypted before they leave your device.** DMs, group messages, shared files, call setup and your synced vault are all ciphertext to the relay. The cryptography lives in one shared core used by the Android app, the web app, and the relay (for signature checks only).

The longer version — with what each kind of server can and cannot see — is in [docs/how-it-works.md](docs/how-it-works.md) and [docs/privacy.md](docs/privacy.md).

## Status

| Area | State |
|---|---|
| Relay, posts, follows, timelines, search | Live |
| Encrypted DMs and groups | Live |
| Encrypted file sharing | Live (phone and web) |
| 1:1 voice calls | Live (phone ↔ phone, phone ↔ browser) |
| Device linking and sync | Live |
| Android app | Early-access APK (this repo) |
| Web app | Live at app.cairnnet.com |
| iOS app | Planned |
| Group voice calls, video calls | Planned |
| Creator features (paid circles, video channels) | Designed, not yet live |

## Feedback

Found a bug, or something confusing? Open an issue on this repo. Please don't post private keys, backup files, or message contents in issues.

## Verifying the download

```
SHA-256  cairn-debug-arm64.apk
b81310927f14ca503b25061b212d47ef2238f9fe262e21e2fad48c6ecd9149d6
```

See [docs/install-android.md](docs/install-android.md) for how to check it.
