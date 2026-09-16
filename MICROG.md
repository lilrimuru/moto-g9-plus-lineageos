# microG on LineageOS 23.2 (`odessa`)

Replace Google Play Services with **[microG](https://microg.org/)** — an
open-source, lightweight re-implementation with **no telemetry**.

---

## ⚠️ Important: use the APK method, not a Magisk system module

This release installs microG as **normal apps** (recommended, safe).

A **Magisk module that installed microG as system priv-app was tested and
caused a boot loop** on this ROM (`lineage-23.2-20260915`, Android 16). It was
recovered with Magisk safe mode and removed; the ROM was not damaged and no
data was lost. The module is **not included** in this release.

The good news: **you do not need a system install for microG to work** — see
the next section. The ONLY thing the system install added was the privileged
network-location provider. GPS/GNSS works without it.

---

## Why no Xposed / no rebuild is needed

normally microG needs *signature spoofing*: the system must report microG as if
it were signed with Google's key. Many ROMs need LSPosed + FakeGApps for that.

**This ROM does not.** LineageOS 23.2 contains native, microG-only signature
spoofing in
`frameworks/base/services/core/java/com/android/server/pm/ComputerEngine.java`:

- `isMicrogSigned()` accepts only packages named `com.google.android.gms` or
  `com.android.vending` that are **signed with microG's release key**;
- `generateFakeSignature()` substitutes the one allowed fake signature;
- it is active because this is a `userdebug` build, so the
  `isDebuggable()` check compiles to `true`.

**Verified two ways:**

| Check | Result |
|---|---|
| microG package names vs framework allowlist | `com.google.android.gms`, `com.android.vending` ✓ |
| microG apk signer vs `MICROG_REAL_SIGNATURE` | byte-identical ✓ |
| `fake-signature` metadata vs `MICROG_FAKE_SIGNATURE` | byte-identical ✓ |
| compiled `isDebuggable()` in this build's `libservices.core` | `ret i1 true` ✓ |

Crucially, the spoofing key is the **package name + signature**, so it applies
**whether microG is a system app or a normal app**. A system install is not
required.

---

## Install (recommended)

Requires only USB debugging, no root.

> Run these from the **release root**. The APKs live in the `microG/` subfolder.

```sh
adb install -r microG/microG-Services.apk
adb install -r microG/microG-Companion.apk
adb install -r --bypass-low-target-sdk-block microG/microG-GsfProxy.apk
```

Or copy the three APKs to the phone and tap them
(allow install from unknown sources).

| File | Package | What it is |
|---|---|---|
| `microG/microG-Services.apk` | `com.google.android.gms` | microG Services (GmsCore) 0.3.16 |
| `microG/microG-Companion.apk` | `com.android.vending` | microG Companion (FakeStore) 0.3.16 |
| `microG/microG-GsfProxy.apk` | `com.google.android.gsf` | Framework Proxy 0.1.0 (legacy C2DM push) |

> Never install real Google Play Services together with microG.
> Install microG **before** any app that depends on GMS.

---

## Configure

1. Open **microG Settings** (app drawer). If it does not appear, launch it with
   `adb shell am start -n com.google.android.gms/org.microg.gms.ui.SettingsActivity`.
2. **Self-Check** — tap every entry. You want **"Signature spoofing: supported"**.
3. **Account** → add a Google account only if you want Play Store / sign-in.
   Optional; microG works without one.
4. **Cloud Messaging** → enable for push notifications (FCM).
5. **Location** → enable *Location services*. Per-country network-location
   backends come from the microG F-Droid repo. (Without a system install,
   microG cannot act as the system network-location provider; the platform GNSS
   still provides GPS location to apps.)
6. Disable battery optimisation for **microG Services**.
7. Reboot once.

## Updating microG

Add the microG F-Droid repo in F-Droid and update normally:

<https://microg.org/fdroid/repo?fingerprint=9BD06727E62796C0130EB6DAB39B73157451582CBD138E86C468ACC395D14165>

## Apps without Google Play

- **F-Droid** — free/open-source apps: <https://f-droid.org>
- **Aurora Store** — anonymous Play Store client: <https://auroraoss.com>
- plain APKs via `adb install`

## Verify

```sh
adb shell pm list packages | grep -E 'com.google.android.gms|com.android.vending|com.google.android.gsf'
adb shell dumpsys package com.google.android.gms | grep versionName
```

The definitive test is microG's own **Self-Check**.

## Remove

```sh
adb uninstall com.google.android.gms
adb uninstall com.android.vending
adb uninstall com.google.android.gsf
```

## Caveats

- **Play Integrity / banking / DRM** still fail (unlocked bootloader +
  test-keys), with or without microG.
- Some apps that deeply integrate Play Services may not work:
  <https://github.com/microg/GmsCore/wiki/Problem-Apps>
- Push works for most apps but can be less reliable than real GMS.
- **Network location** is the one feature lost without a system install.

---

## If you ever experiment with a system install

A system (Magisk) install can boot-loop on this ROM. Recovery:

1. Hold **Volume Down** during boot → Magisk **safe mode** (all modules
   disabled). The system boots normally.
2. With root: `su -c "rm -rf /data/adb/modules/<module-id> /data/adb/modules_update/<module-id>"`
3. Reboot.

Your data is never touched by a Magisk module; it is only a mount.
