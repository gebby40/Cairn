# Installing Cairn on Android

Cairn is distributed here as an APK while it's in early access; it isn't on the Play Store yet. The build is for **64-bit (arm64) phones running Android 8.0 or newer**, which is almost every phone made since 2017. It has been tested on a Pixel 7 Pro (GrapheneOS) and a OnePlus 9 Pro (OxygenOS 14).

It does not need Google services — it works on GrapheneOS, LineageOS and other de-Googled phones.

## 1. Download

Get [`cairn-debug-arm64.apk`](https://github.com/gebby40/cairn/raw/main/releases/cairn-debug-arm64.apk) — the link downloads the file directly. Easiest is to open this page on the phone and tap it.

## 2. Allow installs from your browser or files app

Android will ask the first time you open an APK that didn't come from the Play Store:

- Tap the downloaded file (from your browser's downloads, or the Files app).
- When prompted, allow **"Install unknown apps"** for that app, then go back and tap **Install**.

If you're updating from an earlier Cairn APK, just install over it — your persona and data are kept.

## 3. First run

- **Create a persona** — pick a display name and a passphrase. The passphrase protects your key on this phone; there's no "forgot passphrase" because nobody else has it.
- **Back it up** — Settings → Backup produces a file you can keep somewhere safe. With it you can restore your identity on any device.
- **Already using Cairn in the browser?** Choose **Link this device** instead and scan the QR code from the web app's Settings → Devices & sync. Your identity, follows, messages and groups come across.

## 4. Calls in the background (optional)

To ring when the app is closed, Cairn keeps a lightweight connection to the relay in the background. The app will ask to be excluded from battery optimisation; if you skip it, calls still work while the app is open but may not ring from the lock screen on some phones.

## Verifying the file

Checking the hash confirms the file you downloaded is byte-for-byte the one published here.

**On a PC (Windows PowerShell):**
```
Get-FileHash .\cairn-debug-arm64.apk -Algorithm SHA256
```

**On Linux / macOS:**
```
sha256sum cairn-debug-arm64.apk
```

It should print:

```
d121e12731cb65662286b88ddc8abc5c7abc2eddc9237f04c14a008bf87fc483
```

## Known limitations of this build

- Debug build: slightly larger and slower than a release build will be, and Android shows it as "not signed by a store".
- arm64 only. 32-bit phones and Android emulators on x86 won't install it.
- No automatic updates — check this repo for a newer APK.
