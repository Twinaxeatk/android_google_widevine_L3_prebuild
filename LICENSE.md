# License and terms of use

**Read this before downloading, building or installing anything from this
repository.** The files fall into two groups with very different terms.

## 1. Google Widevine binaries: proprietary, not licensed by this repository

These files are proprietary software of **Google LLC**:

| File | Owner |
|---|---|
| `prebuilts/x86_64/lib64/libwvaidl.so` | Google LLC |
| `prebuilts/x86_64/bin/hw/android.hardware.drm-service-lazy.widevine` | Google LLC |
| `prebuilts/etc/init/android.hardware.drm-service-lazy.widevine.rc` | Google LLC |

Widevine is a trademark of Google LLC. All rights in these files remain with
Google LLC.

- They are **not open-source software**, and no open-source license applies to
  them, including the license of section 2.
- The owner of this repository **is not the copyright holder and cannot grant
  you any license** to them. Nothing in this repository grants, transfers or
  implies any right to use, copy, modify or redistribute them.
- They were extracted, unmodified, from an official Google ChromeOS recovery
  image (see [README.md](README.md#provenance)). That image ships them
  **without a separate license text**: its open-source notice
  (`system/etc/NOTICE.html.gz`) does not cover them, and the vendor partition
  carries no notice of its own. Any right to use them comes only from Google,
  under the terms that govern the product they were distributed with: the
  [Google Terms of Service](https://policies.google.com/terms) and the
  additional terms for Google ChromeOS. Read those terms yourself; this file
  does not summarise or replace them.
- Google licenses Widevine to device makers under separate agreements. A
  device running these files is **not** a Widevine-licensed device, and
  content services may refuse it at any time.

### Your responsibilities if you use them

By using these files you accept that **you alone** are responsible for making
sure your use is permitted by Google and by the law where you are. In
particular:

- Use them only on a device you own, for your own private, non-commercial
  playback of content you are entitled to watch.
- Do **not** redistribute them, bundle them into a product or image you
  distribute, sell them, or offer them for download elsewhere.
- Do **not** modify, decompile, disassemble or reverse engineer them, and do
  not use them to circumvent, weaken or study the protection of any content.
- Follow the terms of every content service you use with them. Using them may
  be against a service's terms even where it technically works.
- If Google or a rights holder objects, stop using them and delete them.

### No warranty

These files are provided **as is**, with no warranty of any kind, and with no
support from Google or from the owner of this repository. You use them at your
own risk.

### Removal

This repository will remove the Google files on request from Google LLC or a
rights holder. Open an issue on this repository, or contact the repository
owner through GitHub.

## 2. Build files written for this repository

These files contain no Google code and were written for this repository:

- `Android.bp`
- `widevine.mk`
- `sepolicy/file_contexts`
- `README.md`
- `LICENSE.md`

They are licensed under the
[Apache License, Version 2.0](https://www.apache.org/licenses/LICENSE-2.0).

`prebuilts/etc/vintf/manifest_android.hardware.drm-service.widevine.xml`
comes from the same image but is Android Open Source Project material; it
carries its own Apache License 2.0 header, which applies.

---

This file states facts about where the binaries came from and who owns them.
It is not legal advice and does not create any right to use the Google files.
