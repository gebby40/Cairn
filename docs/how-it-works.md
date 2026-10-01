# How Cairn works

This is the plain-language version. It's accurate, but it leaves out byte layouts and protocol details.

## Identity: a key, not an account

When you create a persona, the app generates a signing key pair on your device. The public half becomes your **persona ID** — a long string that looks random, which is what people use to find you. The private half never leaves your device unencrypted. A passphrase you choose encrypts it at rest.

Because there's no account, there is nothing to sign up for and nothing a server can reset, suspend, or hand over. The flip side: **the key is the identity.** Lose the key and the backup, and the persona is gone. Cairn gives you a backup file at creation and reminds you to keep it.

A persona can have a display name, a bio and an avatar, and can be **vouched for** or **verified** by others (a relay operator, or a person you've met) — that's how others know a persona is who it says, rather than a username being unique.

## Everything is a signed object

Every action that others need to see — a post, a reply, a boost, a profile edit, a message delivery, a file chunk — is a small record, encoded in a canonical form, hashed, and signed by your key. The hash is its ID, so the same content always has the same ID and any change produces a different one.

That has three consequences:

- **Nobody can forge or alter what you said.** A relay can refuse to store something, but it can't change it or make up something in your name.
- **Relays are interchangeable.** An object is valid anywhere; it doesn't belong to the server it was first posted to. You can post through one relay today and another tomorrow, and your followers can fetch from whichever has a copy.
- **Social features are just object kinds.** Likes, boosts, lists and reports are objects like everything else. A relay builds timelines and counts by replaying the objects it has — it keeps no social-graph state of its own.

Some things deliberately *aren't* objects. **Who you follow is private**: your follow list lives on your device (and in your encrypted vault), and your home timeline is built by asking the relay for those authors' posts per request — the relay stores nothing about it. Mutes and blocks work the same way.

Posts are either **public** (in the firehose, tag timelines and search) or **unlisted** (stored in the clear, shown on your profile and in threads, but left out of discovery). There is intentionally no "followers-only" flag, because a relay-enforced audience is only as private as the relay; anything for a closed audience goes in an encrypted group instead.

## Relays

A relay is a server that stores objects, serves public timelines, search and profiles, and holds encrypted mailboxes. Relays find each other and replicate public content, so there's no single point that has to stay up.

A relay sees exactly what's in the objects it stores. For public posts that's the post. For everything private, it's ciphertext and a few routing fields designed to reveal as little as possible (see [privacy.md](privacy.md)). Relays never log IP addresses by design, and a public relay enforces per-persona rate limits rather than identity checks.

The relay you use is a setting, not a home. Switching relays loses nothing.

## Direct messages

DMs use a **double-ratchet** session (the Olm protocol, the same design family as Signal's), so every message is encrypted with a fresh key and compromising one message doesn't expose the rest.

Delivery works through **mailboxes**: each persona has a mailbox ID that changes every day, derived from the persona's keys so only the owner (and someone who already has a session with them) can compute it. A sender drops the encrypted message in the recipient's current mailbox; the recipient fetches it by proving ownership. The relay sees that *a* message was placed in *a* mailbox — not who sent it, not who the mailbox belongs to, and not what it says.

## Groups

Group chats use **MLS** (Messaging Layer Security, RFC 9420), the IETF standard for end-to-end encrypted groups. Each group has an evolving shared secret; adding or removing a member rotates it, so people who leave can't read what comes after and people who join can't read what came before. Group messages ride the same mailbox system as DMs.

Only the members hold the keys. The relay cannot add anyone to a group, cannot read it, and cannot even tell which mailboxes belong to the same group.

## File sharing

Sharing a photo, video or document in a DM or group:

1. The app generates a one-time key for the file.
2. The file is split into 1 MB chunks; each chunk is encrypted and uploaded. The relay stores each chunk under the hash of its ciphertext.
3. A small **descriptor** — the key, the chunk list, the file name and type — is sent as an ordinary encrypted message to the recipients.
4. A recipient fetches the chunks (anyone can fetch ciphertext by hash; it's useless without the key), checks each one against its hash, decrypts, and verifies the whole file.

The relay stores encrypted blobs it can't open and sees no file names or types. Chunks expire after a while; the descriptor in the thread is what lets a recipient download before then.

## Voice calls

Calls are WebRTC, so the audio path is **DTLS-SRTP** — encrypted between the two phones and unreadable to anything in between. What Cairn adds is the part most "encrypted" calling apps get wrong, the setup:

- **Call setup rides inside encrypted DMs.** The offer, answer and connection candidates are DM messages. The relay sees that messages were delivered, nothing more. There is no separate signaling server or account.
- **The media keys are authenticated by your identity.** The DTLS fingerprint inside the offer is carried by the DM ratchet, which is bound to the persona's keys. Nobody on the path — relay, TURN server, network — can substitute their own keys.
- **A safety code** (12 digits, derived from both sides' keys and fingerprints) lets the two people read it to each other to confirm there's nobody in the middle.
- **Relayed by default.** Audio goes through a TURN server run alongside the relay, so the other party never learns your IP address. The TURN server sees two addresses exchanging encrypted packets — not which personas. A per-call "direct" option trades that for lower latency.
- **Ringing without Google.** The Android app keeps a small persistent connection to the relay in the background and gets a content-free "check your mailbox" nudge when something arrives. Calls ring with the app closed and the screen off, on phones with or without Google services.

Phone ↔ phone and phone ↔ browser calls work today; group calls and video are planned on the same plumbing.

## One identity, many devices

Your data — follows, sessions, groups, messages — lives in an encrypted **vault** on the relay. The vault key is derived from your persona key, so the relay stores a blob it can't open. A new device is linked by scanning a QR code: a short-lived channel carries the persona key, sealed to the new device, and it pulls the vault from there.

One device is **active** at a time (it's the one that answers calls and admits members to groups); the others can read and sync and can take over with a tap. This is deliberate — it keeps the group and DM cryptography simple and correct.

## Tamper-evident public history

Public objects are periodically gathered into a hash tree and the root is anchored into a chain. Anyone can later check that a given public post was present at a given anchor, and a relay can't quietly rewrite or drop history without the anchors failing to match. This gives public content a property normally reserved for blockchains, without putting any content on a chain.

## Where the code lives

All cryptography is in one core library, compiled both natively (for the Android app and the relay) and to WebAssembly (for the web app). The Android app and the web app share the same data formats byte for byte, which is what lets a persona move between them. The protocol and code are called **Signet**; Cairn is the product built on it.
