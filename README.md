# Google Widevine L3 for Android 13 (prebuilt)

> **The Widevine binaries here are Google LLC's proprietary software, not
> licensed by this repository.** Read [LICENSE.md](LICENSE.md) before you
> download, build or install them. It sets out what you may and may not do
> with them, and that you are responsible for your own use.

Google's Android Widevine CDM, taken unmodified from an official Google
Android 13 vendor image, packaged for an AOSP 13 tree and for manual
installation on an Android 13 arm64 container such as FydeOS fyDroid.

It is a **software (L3)** Widevine. It provisions online with Google and is
accepted by services that refuse a browser-derived CDM, Netflix included.

## Provenance

| | |
|---|---|
| Android | 13 (SDK 33), arm64 |
| CDM | built-in `OEMCrypto Level3 Code`, `Level3 Library 28924 Feb  2 2023` |

| File | md5 |
|---|---|
| `prebuilts/arm64/lib64/libwvaidl.so` | `78bcd506710388925ec2cffe100ebd49` |
| `prebuilts/arm64/bin/hw/android.hardware.drm-service-lazy.widevine` | `720c5cc69e889ad9722360834923f081` |
| `prebuilts/etc/init/android.hardware.drm-service-lazy.widevine.rc` | `7733ac212c6dbe5343b4834949bfa8d4` |
| `prebuilts/etc/vintf/manifest_android.hardware.drm-service.widevine.xml` | `c91afcc24a3ba9ed3c136707d24d3836` |

## What is deliberately left out

`liboemcrypto.so`, the hardware (L1) OEMCrypto shim. `libwvaidl.so` loads it
with `dlopen`. When it is present, every CDM instance waits 10 s for a
hardware OEMCrypto service that a generic Android device does not have, so an
app like Netflix takes ~35 s to start. When it is absent, the CDM logs
`Could not load liboemcrypto.so. Falling back to L3` and continues at once.

Without the shim, nothing here links against device-specific libraries; the
service needs only stock Android 13 libraries
(`android.hardware.drm-V1-ndk`, `libbinder_ndk`, `libbase`, `libutils`, ...).

## Build it into an image

Clone into the tree, e.g. `vendor/google/widevine-l3`, then in the device
makefile:

```make
$(call inherit-product-if-exists, vendor/google/widevine-l3/widevine.mk)
BOARD_VENDOR_SEPOLICY_DIRS += vendor/google/widevine-l3/sepolicy
```

- `sepolicy/file_contexts` only labels the binary. The `hal_drm_widevine`
  domain is not in AOSP; the device sepolicy must define it (it needs
  `hal_server_domain(hal_drm_widevine, hal_drm)` and write access to
  `/data/vendor/mediadrm`).
- Only one service may own `IDrmFactory/widevine`. Remove any other Widevine
  HAL from `PRODUCT_PACKAGES` and its VINTF entry, or VINTF assembly fails on
  the duplicate instance.
- Modules are enabled for arm64 only. The binaries are ARMv8.0 code and do
  not use ARMv8.1+ instructions such as LSE atomics.

## Install on a running FydeOS fyDroid device

Writes go to the Android vendor overlay on the stateful partition, so they
survive reboots but not an Android image update or an overlay reset. Run as
root on FydeOS (not inside Android):

```sh
A=/mnt/stateful_partition/unencrypted/android
V=$A/vendor_rw
stop ui
install -o 655360 -g 655360 -m 0644 prebuilts/arm64/lib64/libwvaidl.so $V/lib64/libwvaidl.so
chcon u:object_r:vendor_file:s0 $V/lib64/libwvaidl.so
install -o 655360 -g 657360 -m 0755 prebuilts/arm64/bin/hw/android.hardware.drm-service-lazy.widevine $V/bin/hw/
chcon u:object_r:hal_drm_widevine_exec:s0 $V/bin/hw/android.hardware.drm-service-lazy.widevine
install -o 655360 -g 655360 -m 0644 prebuilts/etc/init/android.hardware.drm-service-lazy.widevine.rc $V/etc/init/
chcon u:object_r:vendor_configs_file:s0 $V/etc/init/android.hardware.drm-service-lazy.widevine.rc
# fyDroid's own Widevine HAL declares the same AIDL instance; hide it.
rm -f $V/etc/init/android.hardware.drm-service.widevine.rc
start ui
```

The VINTF fragment is not needed there: fyDroid's manifest already declares
`IDrmFactory/widevine` V1.

To uninstall, delete the same four paths from `$A/vendor_up` (the last one is
an overlay whiteout) and reboot.

## Expected behaviour

```
WVCdm: Could not load liboemcrypto.so. Falling back to L3.
MediaDrm: vendor=Google securityLevel=L3
```

The first `openSession` from an app throws `NotProvisionedException`. That is
normal: the app then calls `getProvisionRequest` and provisions with Google.
