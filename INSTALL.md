# LineageOS 23.2 (Android 16) for Motorola Moto G9 Plus — `odessa`

> **About file paths below.** This document is written for the release package.
> Paths like `Magisk/…`, `microG/…` and `platform-tools/…` refer to folders in
> that package. If you downloaded files individually from **Releases**, they are
> flat — use the same file name without the folder prefix. Guides `MAGISK.md`
> and `MICROG.md` are also in this `docs/` folder on GitHub.

**UNOFFICIAL, community build.** Built from source for the Motorola Moto G9 Plus
(`odessa`). Tested on **XT2087-2** (reteu / Europe); should also work on
`XT2087-1` and other SM7150 `odessa` variants, but those have not been verified
by the author.


| | |
|---|---|
| ROM | LineageOS 23.2 |
| Android | 16 (SDK 36) |
| Build ID | `BP4A.251205.006` |
| Kernel | `4.14.357-openela-perf+` |
| Build date | 2026-09-15 |
| SoC | Qualcomm SM7150 (SDDMMAGPIE) |
| Security patch | 2026-09-01 (platform) / 2022-05-01 (vendor) |

---

## ⚠️ Read this first

- **Bootloader must be unlocked.** See Motorola's official unlock page.
- **This build is signed with public AOSP `test-keys`.** Anyone can sign an
  update that your phone will accept. Do **not** treat OTA sources as trusted.
- **Verified Boot / dm-verity are disabled** (`vbmeta --flags 3`). This is a
  hard requirement of this Motorola bootloader: it provides no way to install a
  custom AVB key, so a verifying `vbmeta` signed with our key is rejected and
  the phone will not boot. This matches upstream LineageOS `sm6150-common`.
- **Play Integrity / banking / DRM will likely fail** (unlocked bootloader +
  test-keys). This is expected and not a bug.
- **Back up your data.** Installing this wipes `userdata` and `metadata`
  (all apps, accounts, photos, files).
- Installing a custom ROM can leave the phone unbootable. Know your restore
  path **before** you start (see *Restore / rollback* below).

---

## What works (hardware-verified by the author on XT2087-2)

- Boot, display (1080×2400 @ 420 dpi), touch (Novatek NT36675)
- GPU (Adreno 618 / OpenGL ES 3.2)
- Wi-Fi (association + IP), Bluetooth, NFC
- Camera (front, rear, ultrawide, macro), video recording and playback
- Audio (speaker, mic, 3.5 mm), vibration/haptics
- Sensors (accelerometer, gyroscope, ambient light, proximity)
- Auto-brightness, fingerprint reader
- Charging, battery reporting, file-based encryption (FBE), SELinux enforcing
- **Telephony: SIM, outgoing/incoming calls, voice, SMS**

## Not verified yet

- Mobile data, emergency calls
- GNSS, FM radio
- Codecs / HDR
- In-system OTA updates, release signing

**A successful boot is not the same as a complete hardware pass.** Test the
features you depend on and report what you find.

---

## Requirements

- Motorola Moto G9 Plus, codename `odessa`
- Bootloader unlocked
- Host PC with `adb` and `fastboot` (Android platform-tools)
- A working USB data cable and a charged battery
- Ideally, the phone already on a recent stock firmware (this ROM does **not**
  flash low-level firmware such as `modem`, `dsp`, `bluetooth` or the
  bootloader, so it keeps whatever the phone currently has)

---

## Files

| Path | Purpose |
|---|---|
| `lineage-23.2-20260915-UNOFFICIAL-odessa.zip` | A/B OTA — the ROM |
| `lineage-23.2-20260915-UNOFFICIAL-odessa-recovery.img` | Lineage Recovery (must be used to install) |
| `boot-odessa-20260915.img` | Stock boot image of this build (base for Magisk; also your un-root file) |
| `odessa-twrp-stable-r25.img` | Optional standalone TWRP (for backups/inspection; not for installing this ROM) |
| `odessa-local_manifest.xml` | Source manifest for reproducing the build |
| `Magisk/MAGISK.md` | How to get root (optional) |
| `Magisk/Magisk-v30.7.apk` | Official Magisk app |
| `Magisk/magisk_patched-30.7-odessa-20260915.img` | Pre-patched boot image (root) |
| `microG/MICROG.md` | How to use microG instead of Google services |
| `microG/microG-Services.apk` | microG Services (GmsCore) 0.3.16 |
| `microG/microG-Companion.apk` | microG Companion (FakeStore) 0.3.16 |
| `microG/microG-GsfProxy.apk` | microG Services Framework Proxy 0.1.0 |
| `platform-tools/` | Bundled `adb`/`fastboot` (Linux x86_64) so you have everything in one place |
| `SHA256SUMS` | Checksums for every file above |

Verify before flashing:

```sh
sha256sum -c SHA256SUMS
```

Key sums (the full list is in `SHA256SUMS`):

```
2b95223d5c7e177630cd7f57552391720bf182e402a4196d2408f0c6cb5a41ec  lineage-23.2-20260915-UNOFFICIAL-odessa.zip
1aa5c7713682ee8d1cb7488744991f4c3248a268256f39af413387a50057f554  lineage-23.2-20260915-UNOFFICIAL-odessa-recovery.img
be3f63a09599a5ff7e6e04b12ecffca05f37444e79b38a0df22aed071cf50a20  boot-odessa-20260915.img
8c212fdc9127d0de5e37571e4cd4cf4170dcc78762a16fa791aaea7561b1affd  Magisk/magisk_patched-30.7-odessa-20260915.img
e0d32d2123532860f97123d927b1bb86c4e08e6fd8a48bfc6b5bee0afae9ebd5  Magisk/Magisk-v30.7.apk
```

---


## Installation

You **must** use the included Lineage Recovery. This ROM needs a boot-control
HAL that switches both the GPT slot attributes and the UFS boot LUN; stock or
generic recovery does not do this on `odessa` and the slot switch will fail.

### 1. Unlock the bootloader (once)

Follow Motorola's official instructions. This wipes the phone.

### 2. Boot the included recovery (no permanent write)

Put the phone in fastboot mode (hold **Volume Down + Power**), connect USB, then:

```sh
fastboot devices
fastboot boot lineage-23.2-20260915-UNOFFICIAL-odessa-recovery.img
```

`fastboot boot` loads the recovery into RAM without overwriting anything — a
safe first check. (If it does not boot on your unit, you may instead
`fastboot flash recovery <same image>` and boot into recovery.)

### 3. Enable ADB in recovery

In Lineage Recovery: **Advanced → Enable ADB**. If a
*"Allow USB debugging?"* prompt appears, accept it.

```sh
adb devices      # should show your device as "recovery"
```

### 4. Wipe data

In recovery: **Factory reset → Format data/factory reset → confirm**.

This is required, including when coming from another ROM.

### 5. Sideload the ROM

In recovery: **Apply update → Apply from ADB**. Then on the host:

```sh
adb sideload lineage-23.2-20260915-UNOFFICIAL-odessa.zip
```

#### ⚠️ What you will see (this is normal — do not worry)

**The percentage on your PC will stop at about 47% and appear to freeze.**
This is expected and is **not** a failure. Do not cancel and do not unplug the
cable. Here is the full sequence:

1. `adb sideload` streams the package. The host counter climbs to roughly
   **47%**, then jumps to `Total xfer: 1.00x`. **It will not reach 100%** —
   ignore it. The host counter is only the transfer; the real work happens on
   the phone.
2. On the **phone screen** you will see **`step 1/2`**. The phone is verifying
   and applying the update — this takes **1–2 minutes** (sometimes longer).
   During this time the terminal may look idle.
3. Then **`step 2/2`** appears, and finally a success message offering to
   **reboot**.
4. Choose to reboot. The recovery **reboots into recovery again, now on the new
   slot** — this is normal for A/B updates on this device, not a loop.
5. From recovery, choose **Reboot → System** (or run `adb reboot` from the PC).
   This is the step that actually starts LineageOS.

Do not disconnect USB during steps 1–3. Total time is typically 3–6 minutes.

> The "reboot recovery to install additional packages" note you may see is for
> GApps/Magisk and can be ignored — the ROM itself is already installed.

### 6. First boot into LineageOS

Reboot to the system (from recovery: **Reboot → System**, or `adb reboot`).

The **first boot can take several minutes** (Android runs ART/dexopt on first
start). Wait before assuming it is stuck — a black screen or a long animation
is normal at this stage. When it finishes you will land on the LineageOS setup
wizard.

> If it ever reboots into recovery instead of the system, that means the other
> slot was selected — see *Restore / rollback* below, or simply pick
> **Reboot → System** again from recovery.

---

## Optional add-ons

Install **after** the ROM, via recovery sideload:

- **Google apps:** a matching Android 16 GApps package (e.g. MindTheGapps).
- **microG (no Google, no telemetry):** see **`microG/MICROG.md`**.
  Install the three microG APKs as normal apps. This ROM has native microG
  signature spoofing (name + signature based), so **no Xposed module and no
  system install are needed**. Do not use a Magisk system module for microG —
  a system install was tested and caused a boot loop on this ROM.
- **Root:** see **`Magisk/MAGISK.md`**. Root is applied by patching the
  boot image, **not** by recovery sideload.

None are bundled with the ROM. Test the base ROM first so failures can be
attributed. **Use either GApps or microG — never both.**

---

## Restore / rollback

The install activates the **other** slot and preserves the previous one as a
fallback. If the new build fails to boot, the bootloader can fall back on its
own.

To return fully to stock, use **Motorola Software Fix / Rescue** with the
official firmware package for this model. Have it downloaded and ready before
you start.

---

## Troubleshooting

Most of these are normal behaviours or easy fixes, not broken installs.

### The sideload counter stops at ~47%

**Normal.** The PC counter is only the file transfer and never reaches 100%.
The real install happens on the phone (`step 1/2`, then `step 2/2`). See
[step 5](#5-sideload-the-rom). Do not cancel or unplug.

### After the update the phone reboots back into recovery

**Normal for A/B.** Recovery restarts once, now on the newly-installed slot.
From recovery choose **Reboot → System** (or run `adb reboot`). That starts
LineageOS. It is not a boot loop.

### The phone is stuck on the LineageOS boot animation after installing a Magisk module

A Magisk module is a mount, not a disk change — **your data is safe**. Recover
with Magisk **safe mode**, which boots with all modules disabled:

1. Force a reboot (hold **Power** ~15 s), then boot while **holding
   Volume Down** until the home screen appears. Release when it boots.
2. The system starts with no modules. Remove the bad module:

   ```sh
   adb shell su -c "rm -rf /data/adb/modules/<module-id> /data/adb/modules_update/<module-id>"
   adb reboot
   ```

   (`su` may live in `/debug_ramdisk` in a temporary boot:
   `adb shell 'export PATH=/debug_ramdisk:$PATH; su -c "..."'`.)

If safe mode does not trigger, RAM-boot the stock boot image instead, which
boots with **no root and no modules at all**:

```sh
fastboot boot boot-odessa-20260915.img
```

### A camera app (GCam / LMC / etc.) installs but crashes on launch

Symptom in `logcat -b crash`:

```
SecurityException: Failed to find provider com.google.android.gsf.gservices
```

These camera ports need Google's **GServices provider**, which this ROM does not
ship (it has no Google services by default). Fix: install **microG**, which
provides the same authority — see `microG/MICROG.md`. After installing microG
Services, the app launches. (Alternatively use the built-in **Aperture**
camera, which needs nothing.)

### `fastboot flash boot_b ...magisk_patched.img` fails

Motorola's bootloader rejects modified boot images:

```
(bootloader) Preflash validation failed
```

This is a bootloader restriction, not a bad file. Install root another way —
Magisk app **Direct Install**, or write the image from recovery with `dd`. Both
are documented in `Magisk/MAGISK.md`.

### `adb devices` shows nothing / `unauthorized`

- Enable **USB debugging** in Developer options, and accept the on-screen
  *Allow USB debugging?* prompt.
- In Lineage Recovery it is **Advanced → Enable ADB**, then accept the prompt.
- Still nothing: `adb kill-server`, replug the cable, try another port (avoid
  hubs), or a different cable.

### Settings → Trust shows "Vendor: Out-of-date"

Accurate, not a bug. Motorola's last vendor image for this device is from 2022
(`2022-05-01`) and this ROM does not flash vendor firmware. The platform patch
is current.

### First boot is very slow / shows only an animation

Android runs ART/dexopt on first start; this can take several minutes. Wait
before assuming a failure.

---

## Build from source

The build is reproducible from public repositories. Sync LineageOS
`lineage-23.2`, then add this local manifest:

```xml
<!-- see odessa-local_manifest.xml in this release -->
```

Pinned revisions:

| Path | Repository | Revision |
|---|---|---|
| `device/motorola/odessa` | `ARLBR10/android_device_motorola_odessa` | `7195bc6ad25606d95a603845af8e659bdb924807` |
| `device/motorola/sm6150-common` | `ARLBR10/android_device_motorola_sm6150-common` | `f4088668c01cd0bec3a96520ac50823190fdd65a` |
| `kernel/motorola/sm6150` | `ARLBR10/android_kernel_motorola_sm6150` | `a31e2e9a5f76dcce51bdb7946f6b502ae5b423d5` |
| `hardware/motorola` | `LineageOS/android_hardware_motorola` | `ffd5182343fb63227308f0f8b268358e3bd2a3b6` |
| `hardware/qcom-caf/bootctrl` | `ARLBR10/android_hardware_qcom_bootctrl` | `9ccc4a788eede6830cbe0de57df3ccf373d4a002` |

Then:

```sh
source build/envsetup.sh
lunch lineage_odessa-bp4a-userdebug
mka bacon -j$(nproc)
```

Notes learned while building this release (may save you time):

- `repo init` needs `--git-lfs`, or run `git lfs pull` in each
  `external/chromium-webview/prebuilt/*` afterwards — otherwise `webview.apk`
  are 134-byte LFS pointers and the build fails at 82%.
- If the build fails generating `build-manifest.xml` with
  `OSError: ... Read-only file system`, the sandbox cannot write `$HOME`:
  run with `HOME="$PWD/out/.home"`.
- Vendor blobs were extracted from the stock Android 11 firmware
  (`RPAS31.Q2-59-17-4-5-5`) with missing files supplied from a same-platform
  Android 14 payload. They are not part of this repository.

---

## Credits

- **ARLBR10** — the `odessa` LineageOS 23.2 bring-up whose source this is built
  from, including the kernel BPF backport and the Motorola boot-control fix.
- **LineageOS** — the ROM and the `sm6150-common` platform base.
- Kernel: OpenELA 4.14.357.

---

## Known issues

- Verified Boot and dm-verity are disabled (bootloader limitation, see above).
- Signed with test-keys.
- Telephony, GNSS, FM and OTA have not been verified by the author.
- Vendor security patch reports 2022-05-01 (last firmware Motorola shipped for
  this device); Settings → Trust will show "Vendor: Out-of-date". This is
  accurate, not a defect.
- **Third-party camera apps (GCam / LMC / BSG) cannot record video.** Photos work
  fine and look better than stock, but any video recording attempt aborts.
  The Motorola Android 11 camera HAL's video usecase fails under the Android 16
  framework (the CSI decoder halt times out and the session tears down ~0.4 s
  after starting). Tested with LMC 8.4 R18, BSG 9.4 and BSG 9.6 — all the same.
  **Use the built-in Aperture app for video**; it works. This is a vendor-HAL
  limitation and is not fixable from the ROM side.

---

*Unofficial build. Not affiliated with or endorsed by LineageOS. Flash at your
own risk.*
