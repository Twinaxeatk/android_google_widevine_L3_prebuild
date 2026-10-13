# Google Widevine L3 for Android 13 (prebuilt)

> **The Widevine binaries in this repository are Google LLC's proprietary
> software, not licensed by this repository.** Read [LICENSE.md](LICENSE.md)
> before you download, build or install them. It sets out what you may and
> may not do with them, and that you are responsible for your own use.

Google's Android Widevine CDM, taken unmodified from official Google Android
13 vendor images, packaged for an AOSP 13 tree and for manual installation on
an Android 13 container such as FydeOS fyDroid.

It is a **software (L3)** Widevine. It provisions online with Google and is
accepted by services that refuse a browser-derived CDM, Netflix included.

## Branches

This branch holds no binaries. Pick the branch for your Android version and
CPU architecture; each one carries the prebuilts, `Android.bp`, `widevine.mk`,
sepolicy, and its own README with provenance, checksums and install steps.

| Branch | Android | Arch |
|---|---|---|
| [`android-13_amd64`](../../tree/android-13_amd64) | 13 | x86_64 |
| [`android-13_arm64`](../../tree/android-13_arm64) | 13 | arm64 |

```sh
git clone -b android-13_amd64 https://github.com/yunshansimon/android_google_widevine_L3_prebuild.git
```

## What every branch ships, and what it leaves out

| File | Role |
|---|---|
| `libwvaidl.so` | The Widevine CDM, AIDL `android.hardware.drm.IDrmFactory/widevine` V1, with a built-in L3 |
| `android.hardware.drm-service-lazy.widevine` + `.rc` | The lazy HAL service that hosts it |

`liboemcrypto.so`, the hardware (L1) OEMCrypto shim, is **deliberately left
out**. `libwvaidl.so` loads it with `dlopen`. When it is present, every CDM
instance waits 10 s for a hardware OEMCrypto service that a generic Android
device does not have, so an app like Netflix takes ~35 s to start. When it is absent, the CDM logs
`Could not load liboemcrypto.so. Falling back to L3` and continues at once.
Without it, nothing links against device-specific libraries; the service needs
only stock Android 13 libraries.

## Expected behaviour

```
WVCdm: Could not load liboemcrypto.so. Falling back to L3.
MediaDrm: vendor=Google securityLevel=L3
```

The first `openSession` from an app throws `NotProvisionedException`. That is
normal: the app then calls `getProvisionRequest` and provisions with Google.
