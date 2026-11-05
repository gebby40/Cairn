# What the servers can and can't see

Cairn's privacy comes from the data format, not from a server's policy. This page lists, for each kind of server in the system, what it learns. "Relay" is the server you connect to; "TURN" is the audio relay used for calls.

| | What the relay sees | What it cannot see |
|---|---|---|
| **Public posts** | The post, who signed it, when. | — (it's public) |
| **Unlisted posts** | The post (hidden from public timelines and search, visible on your profile and in threads). | — |
| **Likes, boosts, replies** | The objects (they're public, like Mastodon). | — |
| **Who you follow** | Public follows (the default): the follow itself — it's a public object, shown in follower counts. Private follows: only the list of authors your device asks for when it loads your home timeline, in that request. | Your private follows — they're never stored on the relay. |
| **Mutes and blocks** | Nothing. | Everything — they're applied on your device. |
| **Direct messages** | Which persona a message is for, when it arrived and how big it is. | The sender and the content. |
| **Group messages** | Which persona sent each message, when, how big it is, and an opaque group id. | The content and the group's name. |
| **Shared files** | Encrypted chunks stored by hash, their sizes, who uploaded them (signed upload). | File names, types, content, who the recipients are. |
| **Call setup** | Encrypted DMs being delivered. | That a call happened at all (it looks like any DM). |
| **Your synced vault** | An encrypted blob, its size, when it changed. | Anything inside: follows, sessions, messages, groups. |
| **Device linking** | A short-lived encrypted channel. | The key passing through it. |
| **Your IP address** | Only in the moment of the request, never written to a log. | — |
| **Your identity** | Your persona ID and public profile, and that this persona uses this relay. | An email, phone number, real name, or anything else you didn't put in your profile. |

| | What the TURN server sees | What it cannot see |
|---|---|---|
| **Voice calls** | Two IP addresses exchanging encrypted packets, and how long. | Which personas are talking, the audio. |

## Things Cairn deliberately does *not* do

- **No phone number or email.** There's nothing to leak, sell, or be subpoenaed for.
- **No contact upload.** Finding people is by persona ID, link, QR code, or search of public profiles.
- **No stored social graph for private content.** Your contact list, your full list of groups and their member lists live only on your devices and in your encrypted vault. The relay still sees the traffic described above: which persona each message is for, and which personas post in a group.
- **No IP logging.** Relays don't write client addresses to logs.
- **No third-party push services on Android.** Background ringing uses Cairn's own connection to the relay, not Google's.
- **No telemetry, no analytics, no ads.**

## Things you should know

- **Public is public.** A public post is replicated between relays and anchored. Deleting it sends a tombstone that well-behaved relays honour, but you should assume anyone who saw it kept it — same as any social network.
- **Metadata that remains.** A relay can see that a persona is active (it fetches its mailbox regularly), roughly how much it sends and receives, and — while your home timeline loads — which authors you're asking for. Message sizes are visible. These are the limits of what a mailbox design can hide without padding and cover traffic, which are not implemented.
- **The relay operator can refuse service.** A relay can rate-limit, block, or stop storing content from a persona. It cannot read, alter, or impersonate. If a relay misbehaves, switch relays — your identity and data come with you.
- **Your key is your identity.** There is no recovery through a server. Keep the backup file.
- **Not yet audited.** The cryptographic building blocks are standard, well-reviewed libraries (MLS, the Olm double ratchet, XChaCha20-Poly1305, Ed25519, BLAKE3, DTLS-SRTP), but the way Cairn assembles them has not been independently reviewed. Treat it accordingly during early access.
