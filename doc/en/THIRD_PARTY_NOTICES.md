---
name: project-third-party-notices-en
description: Third-party attribution and compliance notices
metadata:
  version: "1.0.0"
  lang: "en"
---

# Third-Party Notices

## Multi-language

[简体中文](../THIRD_PARTY_NOTICES.md) | **English** | [Español](../es/THIRD_PARTY_NOTICES.md)

## Documentation

- Project overview: [README](README.md)

- Design rationale: [DESIGN](DESIGN.md)

- Project status: [LOG](LOG.md)
- Historical records: [HISTORY](HISTORY.md)
- Version changelog: [CHANGELOG](CHANGELOG.md)

- Third-party notices: [THIRD_PARTY_NOTICES](THIRD_PARTY_NOTICES.md)

## Third-Party Notices

### Components

| Component | Version or hash | Upstream URL | License | Usage | Copyright holder | Release obligations | Verified on |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Tango Binary Translator (`tango_translator`) | v2.1.0 | https://www.amanieusystems.com/ | Proprietary (Tango EULA) | Build input `TANGO_PKG`, not in the repository | Amanieu Systems Ltd | **MUST NOT** be copied, redistributed or used commercially; users obtain it themselves and accept the EULA | 2026-11-05 |
| Tango integration files (`binfmt_misc` registration string, `tango.te`, `te_macros`) | v2.1.0 integration package | https://www.amanieusystems.com/ | Not separately stated; unclear whether covered by the EULA | Rewritten into `patch/parts/001.core/files/tango-binfmt.rc` and `patch/parts/002.sepolicy/tango.cil` | Amanieu Systems Ltd | Unclear, see Compliance Review | 2026-11-05 |
| `tango32` kernel module | v2.1.0 integration package | https://www.amanieusystems.com/ | GPL-2.0 (source file SPDX tags) | Both the source and the built `tango32.ko` are build inputs, not in the repository | Amanieu Systems Ltd | The corresponding source must be offered when distributing an image containing `tango32.ko` | 2026-11-05 |
| Xiaomi 14 Ultra 32-bit GPU and gralloc libraries | xiaomi.eu OS3.0.304.0, 30 files, hashes in `patch/parts/004.vendor-lib32/manifest.sha256` | Official Xiaomi firmware | Proprietary | Build input `XIAOMI_LIB32_DIR`; the repository contains only file names and SHA-256 | Xiaomi, Qualcomm | **MUST NOT** enter the repository or distributed flashing packages | 2026-11-05 |
| `zygote_secondary` service definition from AOSP `init.zygote64_32.rc` | Android 15 | https://android.googlesource.com/platform/system/core/ | Apache-2.0 | Modified and placed in `patch/parts/005.zygote32/files/tango-zygote32.rc` | The Android Open Source Project | Keep the license text and mark the modifications | 2026-11-05 |

### License Texts

- Tango EULA: shipped with the Tango integration package (`EULA.txt`); not included in this repository.
- GPL-2.0: shipped with the `kernel-module/` source of the Tango integration package; this repository does not distribute that component.
- Apache-2.0: [third_party/aosp/LICENSE](../../third_party/aosp/LICENSE).

### Source Code Offer

This repository does not distribute `tango32.ko`. When distributing an image containing `tango32.ko`, users must provide, under GPL-2.0, the `kernel-module/` source from the Tango integration package and the [build instructions](../../patch/parts/003.kernel-module/README.md#编译-tango32ko).

### Compliance Review

- `tango32` (GPL-2.0) is a separate loadable kernel module that is not linked with this project's GPL-3.0 code, and this repository contains neither its source nor its binary.
- The registration string in `tango-binfmt.rc` and `tango.cil` are rewritten from the Tango integration package. The package ships only the translator-oriented EULA and does not state a license for these integration files; whether they belong to the "Software" in the EULA is unclear. Before public release, it is recommended to confirm with Amanieu Systems (info@amanieusystems.com) or seek professional advice.
- The scope of the Tango EULA is personal use on a Raspberry Pi 3 and on ARMv8 devices controlled by the user; this project is for users to use on their own devices only.
