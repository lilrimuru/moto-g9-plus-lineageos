# Announcement post draft (XDA / Telegram)

---

**[ROM][UNOFFICIAL][Android 16][odessa] LineageOS 23.2 for Moto G9 Plus**

LineageOS 23.2 (Android 16, SDK 36) built from source for the Motorola
Moto G9 Plus (`odessa`).

**Tested on:** XT2087-2 (reteu / Europe)
**Kernel:** 4.14.357-openela-perf+ (with the 5.10 BPF backport required by Android 16)

### Working (verified on real hardware)
- Boot, display 1080×2400 @ 420 dpi, touch
- Wi-Fi, Bluetooth, NFC
- Camera (front / rear / ultrawide / macro), video recording + playback
- Audio (speaker, mic, 3.5 mm), vibration
- Sensors (accelerometer, gyroscope, ambient light, proximity), auto-brightness
- Fingerprint, charging, file-based encryption, SELinux enforcing
- Telephony: SIM, outgoing/incoming calls, voice, SMS

### Not verified yet
- Mobile data, emergency calls, GNSS, FM, OTA, release signing

### Before you flash — please read
- Bootloader must be **unlocked**.
- The build is signed with public AOSP **test-keys**; Verified Boot and
  dm-verity are **disabled** (this Motorola bootloader cannot take a custom AVB
  key — a verifying vbmeta is rejected and the phone won't boot).
- **Play Integrity / banking / DRM will likely fail.** Expected, not a bug.
- Installing wipes `userdata` and `metadata`.

### Install (short version)
1. `fastboot boot lineage-23.2-20260915-UNOFFICIAL-odessa-recovery.img`
2. Recovery → **Advanced → Enable ADB**
3. Recovery → **Factory reset → Format data**
4. Recovery → **Apply update → Apply from ADB**
5. `adb sideload lineage-23.2-20260915-UNOFFICIAL-odessa.zip`
6. `adb reboot` (first boot takes several minutes)

> **Don't panic at 47%.** The host counter stops around 47% and jumps to
> `Total xfer: 1.00x` — that is normal. The real work is on the phone:
> **`step 1/2`**, then **`step 2/2`** after 1–2 minutes, then it offers a reboot.
> It reboots into recovery once more (normal A/B behaviour); from there choose
> **Reboot → System**. Do not unplug USB or cancel mid-way. See the README for
> the full sequence.

Use the **included recovery** — `odessa` needs a boot-control HAL that switches
both GPT slot attributes and the UFS boot LUN.

Full instructions and checksums are in the README in the download folder.

### Download
- `lineage-23.2-20260915-UNOFFICIAL-odessa.zip` — 1.03 GB
- `lineage-23.2-20260915-UNOFFICIAL-odessa-recovery.img` — 67 MB
- `SHA256SUMS`
- `README.md`

**Optional root:** `MAGISK.md`, `Magisk-v30.7.apk`,
`boot-odessa-20260915.img`, `magisk_patched-30.7-odessa-20260915.img`

**Optional microG (no Google, no telemetry):** `MICROG.md`,
`microG-Services.apk`, `microG-Companion.apk`, `microG-GsfProxy.apk`.
Install them as normal apps — this ROM has native microG signature spoofing, so
no Xposed/LSPosed and no system install are needed. (A Magisk system module was
tested and caused a boot loop — use the APK method.)

### Verify
```
sha256sum -c SHA256SUMS
```
```
2b95223d5c7e177630cd7f57552391720bf182e402a4196d2408f0c6cb5a41ec  lineage-23.2-20260915-UNOFFICIAL-odessa.zip
1aa5c7713682ee8d1cb7488744991f4c3248a268256f39af413387a50057f554  lineage-23.2-20260915-UNOFFICIAL-odessa-recovery.img
```

### Common questions (README has full troubleshooting)
- **`step 1/2` / `step 2/2`** on the phone = install is applying. The PC counter
  stopping at ~47% is normal.
- **Bootloop after a Magisk module** = hold **Volume Down** while booting for
  Magisk safe mode, then delete the module from `/data/adb/modules`.
- **GCam/LMC crashes on launch** = it needs Google's GServices provider; install
  **microG** (`microG/MICROG.md`), or use the built-in Aperture camera.
- **`fastboot flash boot` says "Preflash validation failed"** = Motorola
  bootloader restriction; install root via Magisk Direct Install or from
  recovery (`Magisk/MAGISK.md`).

### Build from source
Device trees, common tree, kernel and boot-control HAL are public (pins in the
README / local manifest). Credit to **ARLBR10** for the `odessa` bring-up this
is built from.

### Feedback requested
Please report what works and what doesn't — especially **telephony, GNSS and
FM** — with `logcat`/`dmesg` where possible. State your exact model (XT2087-1 /
XT2087-2) and region.

*Unofficial build. Not affiliated with or endorsed by LineageOS.*

---

## Hosting notes (not part of the post)

- Total release folder: ~1.1 GB.
- Keep the OTA ZIP and recovery image together; list `SHA256SUMS` and paste the
  hashes in the post.
- Do not re-upload the ZIP under a different name without re-hashing it.
- Where to host: XDA attachments (size limit may apply), Google Drive,
  Telegram channel, SourceForge, or pixeldrain. Avoid link shorteners that
  obscure the destination.
