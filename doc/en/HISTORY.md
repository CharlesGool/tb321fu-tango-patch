---
name: project-history-en
description: Historical Records
metadata:
  version: "1.0.0"
  lang: "en"
---

# Historical Records

## Multi-language

[简体中文](../HISTORY.md) | **English** | [Español](../es/HISTORY.md)

## Documentation

- Project overview: [README](README.md)

- Design rationale: [DESIGN](DESIGN.md)

- Project status: [LOG](LOG.md)
- Historical records: [HISTORY](HISTORY.md)
- Version changelog: [CHANGELOG](CHANGELOG.md)

- Third-party notices: [THIRD_PARTY_NOTICES](THIRD_PARTY_NOTICES.md)

## Historical Records

### Real-Device Test History

| Version | Date | Symptom | Cause and fix |
| --- | --- | --- | --- |
| V68 | 2026-10 | Boot loop stuck on the first screen, `reboot,boringssl-self-check-failed` | Lenovo vendor's 32-bit BoringSSL self-check program does not exist and is `reboot_on_failure`; unrelated to Tango; changed to take over the vendor self-check |
| V77 | 2026-10 | Stuck on the second screen, system_server crashes every 60 seconds | The system's `ro.zygote=zygote64_32` was overridden by the vendor's `zygote64`, so the 32-bit zygote did not start while `abilist32` was already declared |
| V78 | 2026-10 | Stuck on the second screen, the 32-bit zygote restarts every 5 seconds | The `insmod` by init in `early-init` did not load successfully, and the CFI check in the 32-bit libraries raised SIGILL |
| V79 | 2026-10 | Whole-device hard reboot during the boot animation | The herald domain (then called omk) loaded the module successfully in `post-fs-data`, and the device rebooted about 8 seconds after the 32-bit zygote started; also found that the 32-bit boot image did not match CC GC |
| V80, V81 | 2026-10 | Boots when the 32-bit ABI is not declared | The module can be loaded in `post-fs-data`; changed to load at that point |
| V82 | 2026-10 | With the boot guard, same symptom as V79 | The guard falls back to pure 64-bit on the next boot |
| V881 | 2026-10-31 | Whole-device reboot about 25 seconds after the 32-bit zygote ran | During fallback, the stock `init.rc` started `zygote_secondary`, triggering `updatable_crashing`; `enable_uffd_gc` had no effect when changed only in system; old markers were not cleared; `guard.txt` was overwritten. V882 fixed all of these and renamed the service to `tango_zygote32` |
| V882 | 2026-10-31 | Kernel `BUG` about 28 seconds after booting with 32-bit | console-ramoops pointed to the 32-bit task self-check in Lenovo `sched-walt.ko` |
| V883 | 2026-10-31 | 32-bit APKs run normally | The boot guard writes `panic_on_walt_bug=0`. Confirmed by the user |

V68 to V883 are the overall version numbers of TB321FU-GSI-Mods' predecessor project, not version numbers of this patch.

### Organizational Changes

- 2026-10-31: The 5 modules moved from `mods/tango/` to `patches/tango/` in Mods.
- 2026-11-01: The 5 patches were merged into the 5 parts of one patch, `required/general/tango`, patch version V1.0.
- Early 2026-11 (date not verified): Runtime naming changed from omk to herald (`heraldctl`, herald domain).
- 2026-11-05: Split into a standalone private repository, tb321fu-tango. The same day that repository was renamed tango-porting-guide and turned into a general porting tutorial; the patch was split out into this repository, tb321fu-tango-patch, and the research notes stayed in the tutorial repository.
