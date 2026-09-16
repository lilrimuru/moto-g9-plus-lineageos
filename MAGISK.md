# Getting root with Magisk — Moto G9 Plus (`odessa`), LineageOS 23.2

This guide adds **Magisk v30.7** root to the ROM in this release.

> **Read first.**
> - Root is **optional** and not required for the ROM to work.
> - Root **breaks** Play Integrity, most banking apps, and some DRM playback.
> - Root means any app you grant it can modify the whole system. Only grant it
>   to things you trust.
> - **Never lock the bootloader** while a patched boot image is installed.
> - The files here are tied to **this exact ROM build**
>   (`lineage-23.2-20260915-UNOFFICIAL-odessa`). Do not mix them with another
>   build.

---

## Files used

> **Where the files are:** all command paths below are relative to the **release
> root** (the folder that contains `lineage-23.2-...zip`). This guide lives in
> `Magisk/`, but `boot-odessa-20260915.img` and the OTA/recovery images are in
> the release root, so run the commands from there.

| File | What it is |
|---|---|
| `Magisk/Magisk-v30.7.apk` | Official Magisk app (signed by John Wu) |
| `boot-odessa-20260915.img` | The **exact stock boot image** of this ROM — the base to patch, and your un-root file |
| `Magisk/magisk_patched-30.7-odessa-20260915.img` | Pre-patched boot image (verified on hardware) |

## Important: `fastboot flash boot` does **not** work on this phone

Motorola's bootloader refuses to write a modified boot image via fastboot:

```
fastboot flash boot_b magisk_patched-*.img
(bootloader) Preflash validation failed
FAILED (remote: '')
```

This is a Motorola bootloader restriction, **not** a problem with the image.
The image itself is fine — it boots with `fastboot boot` and gives root.

So root is installed one of two ways, both of which write the boot partition
**outside** of `fastboot flash`:

- **Method A — from the Magisk app (Direct Install).** Easiest. Requires being
  booted with root active (temporary boot, below).
- **Method B — write the image from recovery with `dd`.** Deterministic; this
  is the method that was verified on hardware for this release.

## Which slot am I on?

Root goes to the boot partition of the **currently active slot**.

```sh
fastboot getvar current-slot
```

Prints `a` or `b`. This release installs to slot `b`, so `boot_b` is expected.
Substitute your actual slot below.

---

## Step 1 — Get root temporarily and check it works (no write)

Put the phone in fastboot (Volume Down + Power) and load the patched image into
RAM:

```sh
fastboot boot magisk_patched-30.7-odessa-20260915.img
```

Nothing is written. Let Android boot, then confirm root:

```sh
adb shell 'export PATH=/debug_ramdisk:$PATH; su -c id'
```

Expected:

```
uid=0(root) gid=0(root) context=u:r:magisk:s0
```

The Magisk app should show **Installed** with version 30.7.

> In a temporary (`fastboot boot`) session, `su` lives in `/debug_ramdisk`.
> After a permanent install it is on `$PATH` as `/product/bin/su`.

If this step does **not** boot, stop here — reboot normally and nothing is
changed.

---

## Step 2 — Make it permanent

### Method A — Magisk app → Direct Install (easiest)

While still booted from Step 1:

1. Open the **Magisk** app.
2. Tap **Install** next to *Magisk*.
3. Choose **Direct Install (Recommended)**.
4. When it finishes, tap **Reboot**.

Magisk writes the patched image to the active boot partition itself. No PC
needed. This is also how you update Magisk later.

### Method B — write from recovery with `dd` (verified on hardware)

Use this if Method A does not work, or if you prefer a fully controlled write.

1. Reboot to fastboot and RAM-boot Lineage Recovery (no permanent write):

   ```sh
   fastboot boot lineage-23.2-20260915-UNOFFICIAL-odessa-recovery.img
   ```

2. In recovery: **Advanced → Enable ADB**. Confirm the host sees it:

   ```sh
   adb devices        # should show "recovery"
   ```

3. Push the patched image and write it to the active slot's boot partition:

   ```sh
   adb push magisk_patched-30.7-odessa-20260915.img /tmp/mp.img
   adb shell 'dd if=/tmp/mp.img of=/dev/block/bootdevice/by-name/boot_b bs=4096'
   adb shell 'sync'
   ```

   Replace `boot_b` with `boot_a` if you are on slot A.

4. Verify the write (the hash must equal the file's):

   ```sh
   sha256sum magisk_patched-30.7-odessa-20260915.img
   adb shell 'sha256sum /dev/block/bootdevice/by-name/boot_b'
   ```

5. Reboot:

   ```sh
   adb reboot
   ```

---

## Verifying root (permanent)

```sh
adb shell su -c id
```

Expected:

```
uid=0(root) gid=0(root) groups=0(root) context=u:r:magisk:s0
```

---

## Restoring the stock boot image (un-root)

Because `fastboot flash boot` is blocked, restore the same way you installed —
from recovery:

1. RAM-boot Lineage Recovery, **Enable ADB**.
2. Push and write the stock image:

   ```sh
   adb push boot-odessa-20260915.img /tmp/stock.img
   adb shell 'dd if=/tmp/stock.img of=/dev/block/bootdevice/by-name/boot_b bs=4096'
   adb shell 'sync'
   ```

3. `adb reboot`.

Your data is not affected. To remove the Magisk app too, uninstall it like any
app (or use *Uninstall → Complete uninstall* in the app before removing it).

---

## Updating Magisk later

Once root is permanent, the app offers **Install → Direct Install**. That
patches the running boot image in place and keeps root — no PC needed.

When you flash a **new ROM build**, root is lost with the old boot partition.
Repeat Step 1 + Step 2 with the new ROM's `boot.img`.

---

## Notes for `odessa`

- There is **no `init_boot`** partition — root lives in `boot`.
- The ROM's `vbmeta` has verification disabled (`--flags 3`, a Motorola
  bootloader requirement), so a patched boot image is not rejected **at boot**;
  only the fastboot *flash* path is blocked.
- File-based encryption is preserved (`KEEPVERITY=true`,
  `KEEPFORCEENCRYPT=true` were used while patching).
- Do **not** `adb sideload` the Magisk zip from recovery — Lineage Recovery
  verifies package signatures and will reject it. Use the methods above.
