# LineageOS 23.2 for Motorola Moto G9 Plus (`odessa`)

**Unofficial community build of LineageOS 23.2 (Android 16) for the Motorola
Moto G9 Plus** (codename `odessa`, SM7150). Built from source; tested on
`XT2087-2` (reteu / Europe).

[![Android](https://img.shields.io/badge/Android-16%20(SDK%2036)-3DDC84?logo=android&logoColor=white)](#)
[![LineageOS](https://img.shields.io/badge/LineageOS-23.2-167C80)](#)
[![Kernel](https://img.shields.io/badge/Kernel-4.14.357--openela-orange)](#)

---

## ⬇️ Download

Everything you need is in **[Releases](../../releases/latest)**:

| Asset | What it is |
|---|---|
| `lineage-23.2-*-odessa.zip` | The ROM (A/B OTA) — flash this |
| `lineage-23.2-*-odessa-recovery.img` | Lineage Recovery — **required** to install |
| `boot-odessa-*.img` | Stock boot image (base for Magisk, and un-root file) |
| `magisk_patched-*.img` | Pre-patched boot image for root |
| `Magisk-v*.apk` | Official Magisk app |
| `microG-*.apk` | microG (Google-free services), optional |
| `odessa-twrp-stable-r25.img` | Optional standalone TWRP |
| `SHA256SUMS` | Checksums for every asset |

Always verify before flashing:

```sh
sha256sum -c SHA256SUMS
```

## 📖 Documentation

| Guide | File |
|---|---|
| **Installation** (step-by-step) | [`docs/INSTALL.md`](docs/INSTALL.md) |
| **Root with Magisk** | [`docs/MAGISK.md`](docs/MAGISK.md) |
| **microG** (no Google, no telemetry) | [`docs/MICROG.md`](docs/MICROG.md) |
| **Announcement / release notes** | [`docs/ANNOUNCEMENT.md`](docs/ANNOUNCEMENT.md) |

## ✅ Status

Hardware-verified on `XT2087-2`:

- Boot, display 1080×2400 @ 420 dpi, touch (Novatek NT36675)
- GPU (Adreno 618 / OpenGL ES 3.2)
- Wi-Fi, Bluetooth, NFC
- Camera (front / rear / ultrawide / macro), photos
- Audio (speaker, mic, 3.5 mm), vibration
- Sensors, auto-brightness, fingerprint
- Charging, battery reporting, file-based encryption (FBE), SELinux enforcing
- Telephony (SIM, calls, SMS)

**Not yet verified:** mobile data, emergency calls, GNSS, FM, in-system OTA.

## ⚠️ Important

- **Bootloader must be unlocked.**
- Signed with public AOSP **test-keys**. Verified Boot and dm-verity are
  **disabled** — this Motorola bootloader cannot take a custom AVB key.
- **Play Integrity / banking / DRM will likely fail.**
- Installing wipes `userdata` and `metadata`.
- **Third-party camera apps (GCam/LMC/BSG) cannot record video** — use the
  built-in Aperture app. Photos from GCam work and look better than stock.
  See [Known issues](docs/INSTALL.md#known-issues) for the technical reason.

## 🔧 Build from source

The build is reproducible from public repositories. See the
[source manifest](docs/source-manifest.xml) and the "Build from source" section
of [`docs/INSTALL.md`](docs/INSTALL.md).

Credit for the `odessa` LineageOS 23.2 bring-up (device tree, common tree,
kernel BPF backport, Motorola boot-control HAL) goes to **ARLBR10**.

## 📄 License & credits

- Original content of this repository (documentation, build notes) is licensed
  under **Apache-2.0** — see [`LICENSE`](LICENSE).
- The ROM itself is a compilation: LineageOS/AOSP (Apache-2.0),
  Linux kernel (GPL-2.0), and proprietary Motorola/Qualcomm components that are
  **not** part of this repository and remain the property of their owners.
- Not affiliated with or endorsed by LineageOS or Motorola.

---

*Unofficial build. Flash at your own risk.*
