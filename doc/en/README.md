# tb321fu-tango-patch

<!-- Uncomment the next line when the project has an icon -->
<!-- <img align="right" src="../resources/logo.png" width="160" alt="tb321fu-tango-patch"> -->

[简体中文](../../README.md) | **English** | [Español](../es/README.md)

The CPU of the Legion Y700 Gen 3 (TB321FU) has no AArch32, so 32-bit APKs cannot run after flashing a GSI; this project is a TB321FU-GSI-Mods patch that integrates the Tango binary translator into the system image, so 32-bit APKs run without root.

[![License](https://img.shields.io/badge/License-GPL--3.0--or--later-orange)](../../LICENSE)

## Documentation

- Project overview: [README](README.md)

- Design rationale: [DESIGN](DESIGN.md)

- Project status: [LOG](LOG.md)
- Historical records: [HISTORY](HISTORY.md)
- Version changelog: [CHANGELOG](CHANGELOG.md)

- Third-party notices: [THIRD_PARTY_NOTICES](THIRD_PARTY_NOTICES.md)

## Introduction

- [Tango](https://www.amanieusystems.com/) translates 32-bit ARM programs to arm64 for execution, so 32-bit APKs can be installed and run without root.
- The `tango` patch has 5 parts: the translator and `binfmt_misc` registration, the SELinux policy, the `tango32` kernel module, the 32-bit GPU libraries, and the 32-bit zygote with the boot guard. See [patch/README.md](../../patch/README.md) for a description of each part.
- It fixes the boot loop and whole-device reboot that occur after the Lenovo vendor image declares a 32-bit ABI; for the cause, see the [research notes](https://github.com/CharlesGool/tango-porting-guide/blob/main/doc/research.md).
- V883 (2026-10-31) passed testing on a real device. For known issues, see [LOG](LOG.md#bugs).

For a device-independent porting method, see [tango-porting-guide](https://github.com/CharlesGool/tango-porting-guide); this repository is its reference implementation.

This repository is the only source of the patch. The `patches/required/general/tango/` directory of [TB321FU-GSI-Mods](https://github.com/CharlesGool/TB321FU-GSI-Mods) is a synchronized copy.

## Requirements

- Legion Y700 Gen 3 (TB321FU), stock Lenovo Android 15 vendor, boot kernel GKI `6.1.112-android14-11-gd03cb1d7abcd-ab12592111`
- Build the system image with [TB321FU-GSI-Mods](https://github.com/CharlesGool/TB321FU-GSI-Mods) and enable the [herald-core](https://github.com/CharlesGool/herald-core) patch
- Supplied by the user; **MUST NOT** be committed to the repository or included in a distributed flashing package:
  - The Tango v2.1.0 integration package (needs `prebuilts/tango_translator`), obtained from Amanieu Systems under its EULA
  - A `tango32.ko` built against the kernel above; for how to build it, see [kernel-module](../../patch/parts/003.kernel-module/README.md#编译-tango32ko)
  - The 30 32-bit libraries from the Xiaomi 14 Ultra xiaomi.eu OS3.0.304.0 `vendor/lib`; for the file list, see [manifest.sha256](../../patch/parts/004.vendor-lib32/manifest.sha256)
  - The stock vendor image

## Install

This project is not installed on its own; TB321FU-GSI-Mods bakes it into the system image at build time.

### Quick Install

TB321FU-GSI-Mods already ships a synchronized copy, so the default build includes this patch. Put the inputs above in the default locations of the Mods repository, then build:

```bash
make 9008 NOTE="tango"
```

The default locations are `private/tango/pkg/`, `private/tango/tango32.ko` and `private/xiaomi-vendor-lib32/` in the Mods repository.

### Normal Install

Clone this repository next to Mods (same parent directory), sync the copy from Mods, then build. For another location, set `TANGO_PATCH_DIR`:

```bash
cd <TB321FU-GSI-Mods>
make sync-tango
```

## Usage

### Parameters

Set the parameters in `devices/<device>/conf.d/tango.conf` of Mods; an environment variable with the same name takes precedence.

| Parameter | Meaning |
| --- | --- |
| `TANGO_PKG` | Tango integration package directory |
| `TANGO32_KO` | Path of `tango32.ko` |
| `TANGO_KERNEL_RELEASE` | Target kernel release string; checked against the `vermagic` of the .ko at build time |
| `XIAOMI_LIB32_DIR` | 32-bit library directory, same layout as `/vendor/lib` |
| `STOCK_VENDOR_IMG` | Stock vendor image (already set in `device.conf` of Mods) |
| `TANGO_DEBUG` | Translator log level `off`, `info` or `debug`; when not `off`, logs are written to `/metadata/tango_log` |

### Checking and Troubleshooting

```bash
adb shell heraldctl tango status        # module, 32-bit ABI, whether fallback is active
adb shell heraldctl tango logs          # export logs to /data/local/tmp/tango_log
adb pull /data/local/tmp/tango_log
```

For other commands and the boot guard, see [zygote32](../../patch/parts/005.zygote32/README.md#heraldctl-命令).

## Upgrade

None yet.

## Uninstall

### Quick Uninstall

Turns translation off immediately without reflashing, and restores it after a reboot (the 32-bit ABI is still declared, so 32-bit APKs cannot run):

```bash
adb shell setprop tango.enabled 0
```

### Full Uninstall

Remove this patch in Mods, rebuild and reflash:

```bash
make 9008 WITHOUT=tango NOTE="不带 32 位"
```

## Acknowledgements

- [Tango](https://www.amanieusystems.com/) by Amanieu Systems: source of the translator, the `tango32` kernel module and the SELinux policy
- The official Tango integrations of the Xiaomi 14 Ultra and OnePlus 12: reference for the 32-bit zygote and ART GC approach

## License

This project is licensed under the GNU General Public License v3.0 or later, SPDX identifier `GPL-3.0-or-later`; the full text is in [LICENSE](../../LICENSE). Tango, the `tango32` module and Xiaomi's libraries are not distributed with this project; for their licenses, see [THIRD_PARTY_NOTICES](THIRD_PARTY_NOTICES.md).
