---
name: project-log-en
description: Project bugs, limitations, decisions, and handoff
metadata:
  version: "1.0.0"
  lang: "en"
---

# Log

## Multi-language

[简体中文](../LOG.md) | **English** | [Español](../es/LOG.md)

## Documentation

- Project overview: [README](README.md)

- Design rationale: [DESIGN](DESIGN.md)

- Project status: [LOG](LOG.md)
- Historical records: [HISTORY](HISTORY.md)
- Version changelog: [CHANGELOG](CHANGELOG.md)

- Third-party notices: [THIRD_PARTY_NOTICES](THIRD_PARTY_NOTICES.md)

## Records

- [History](HISTORY.md)
- [Changelog](CHANGELOG.md)

## Bugs

- [ ] With the default `ENFORCE=0`, `tango-guard.sh` deletes the `fallback` marker at boot, so the fallback marker written by `heraldctl tango off` has no effect.
- [ ] Tango v2.1.0 on QEMU occasionally hangs when a 32-thread program takes the fatal path, or misreports SIGSEGV as SIGABRT (v2.0.7 too); not observed on a real device.
- [ ] `panic_on_walt_bug=0` also disables the other BUG checks of Lenovo WALT.
- [ ] The compatibility of the 32-bit Adreno `V@0762.4` with the device's 64-bit `V@0762.28` and the kernel KGSL has not been verified separately.

## Limitations

- The 32-bit zygote cannot be started on demand and pre-translation is not possible: both need changes to the framework or installd source, which a binary GSI cannot do.
- A complete flashing package cannot be distributed: the Tango EULA forbids copying and redistribution, and Xiaomi's libraries are proprietary files.
- `tango32.ko` works only with a kernel whose release string is identical; it **MUST** be rebuilt after switching the stock boot.

## Decisions

| Date | Decision | Status | Reason |
| --- | --- | --- | --- |
| 2026-10-31 | Do not change `ro.zygote`; ship our own 32-bit zygote service | Accepted | The vendor's `ro.zygote` overrides the system's value, and the stock rc would bring in the 32-bit self-check and the linked restart. |
| 2026-10-31 | `ro.dalvik.vm.enable_uffd_gc=false` | Accepted | Tango does not support userfaultfd, so 32-bit ART can only use CC GC. |
| 2026-10-31 | The boot guard writes `panic_on_walt_bug=0` to fix the whole-device reboot | Accepted | Changing `sched-walt.ko` requires repacking `vendor_boot`; the sysctl has been verified on a real device. |
| 2026-10-31 | Switch to OnePlus 12's 32-bit GPU libraries | Rejected | The real cause was the WALT self-check; the current libraries work fine on the real device. |
| 2026-11-01 | Merge 5 independent patches into 5 parts of one patch | Accepted | The parts must be used together functionally. |
| 2026-11-05 | Split into a standalone repository as the only source of the patch; Mods keeps a synchronized copy | Accepted | Consistent with how herald-core is organized; easier to maintain and release separately. |
| 2026-11-05 | Keep the patch and the general porting tutorial as two public repositories, with the research notes in the tutorial repository | Accepted | The tutorial targets all devices, while the patch applies only to TB321FU. |

## Handoff

The current handoff is maintained in the [Simplified Chinese LOG.md](../LOG.md#交接).
