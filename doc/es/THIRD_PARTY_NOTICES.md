---
name: project-third-party-notices-es
description: Atribuciones y avisos de cumplimiento de terceros
metadata:
  version: "1.0.0"
  lang: "es"
---

# Avisos de terceros

## Multilingüe

[简体中文](../THIRD_PARTY_NOTICES.md) | [English](../en/THIRD_PARTY_NOTICES.md) | **Español**

## Documentación

- Descripción general del proyecto: [README](README.md)

- Justificación del diseño: [DESIGN](DESIGN.md)

- Estado del proyecto: [LOG](LOG.md)
- Registros históricos: [HISTORY](HISTORY.md)
- Historial de cambios: [CHANGELOG](CHANGELOG.md)

- Avisos de terceros: [THIRD_PARTY_NOTICES](THIRD_PARTY_NOTICES.md)

## Avisos de terceros

### Componentes

| Componente | Versión o hash | URL de origen | Licencia | Uso | Titular de los derechos | Obligaciones de distribución | Verificado el |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Tango Binary Translator (`tango_translator`) | v2.1.0 | https://www.amanieusystems.com/ | Propietaria (EULA de Tango) | Entrada de construcción `TANGO_PKG`, no está en el repositorio | Amanieu Systems Ltd | **MUST NOT** copiar, redistribuir ni usar con fines comerciales; el usuario lo obtiene por su cuenta y acepta el EULA | 2026-11-05 |
| Archivos de integración de Tango (cadena de registro de `binfmt_misc`, `tango.te`, `te_macros`) | Paquete de integración v2.1.0 | https://www.amanieusystems.com/ | No declarada por separado; no está claro si les aplica el EULA | Reescritos e incorporados en `patch/parts/001.core/files/tango-binfmt.rc` y `patch/parts/002.sepolicy/tango.cil` | Amanieu Systems Ltd | Incierta, véase la revisión de cumplimiento | 2026-11-05 |
| Módulo de kernel `tango32` | Paquete de integración v2.1.0 | https://www.amanieusystems.com/ | GPL-2.0 (marca SPDX en los archivos fuente) | Tanto el código fuente como el `tango32.ko` compilado son entradas de construcción, no están en el repositorio | Amanieu Systems Ltd | Al distribuir una imagen con `tango32.ko` hay que ofrecer el código fuente correspondiente | 2026-11-05 |
| Bibliotecas GPU y gralloc de 32 bits del Xiaomi 14 Ultra | xiaomi.eu OS3.0.304.0, 30 archivos, hashes en `patch/parts/004.vendor-lib32/manifest.sha256` | Firmware oficial de Xiaomi | Propietaria | Entrada de construcción `XIAOMI_LIB32_DIR`, el repositorio solo contiene nombres de archivo y SHA-256 | Xiaomi, Qualcomm | **MUST NOT** entrar en el repositorio ni en el paquete de flasheo distribuido | 2026-11-05 |
| Definición del servicio `zygote_secondary` de `init.zygote64_32.rc` de AOSP | Android 15 | https://android.googlesource.com/platform/system/core/ | Apache-2.0 | Modificada e incorporada en `patch/parts/005.zygote32/files/tango-zygote32.rc` | The Android Open Source Project | Conservar el texto de la licencia e indicar las modificaciones | 2026-11-05 |

### Textos de licencia

- EULA de Tango: se entrega con el paquete de integración Tango (`EULA.txt`); este repositorio no lo incluye.
- GPL-2.0: se entrega con el código fuente de `kernel-module/` del paquete de integración Tango; este repositorio no distribuye ese componente.
- Apache-2.0: [third_party/aosp/LICENSE](../../third_party/aosp/LICENSE).

### Oferta de código fuente

Este repositorio no distribuye `tango32.ko`. Quien distribuya una imagen que contenga `tango32.ko` debe ofrecer, conforme a GPL-2.0, el código fuente de `kernel-module/` del paquete de integración Tango y el [método de compilación](../../patch/parts/003.kernel-module/README.md#编译-tango32ko).

### Revisión de cumplimiento

- `tango32` (GPL-2.0) es un módulo de kernel cargable independiente, no se enlaza con el código GPL-3.0 de este proyecto, y este repositorio tampoco incluye su código fuente ni sus binarios.
- La cadena de registro de `tango-binfmt.rc` y `tango.cil` son reescrituras de archivos del paquete de integración Tango. El paquete solo incluye el EULA dirigido al traductor y no declara una licencia aparte para estos archivos de integración; no está claro si forman parte del "Software" del EULA. Antes de la publicación se recomienda confirmarlo con Amanieu Systems (info@amanieusystems.com) o buscar asesoría profesional.
- El alcance de la licencia del EULA de Tango es el uso personal en Raspberry Pi 3 y en dispositivos ARMv8 controlados por el usuario; este proyecto es solo para que el usuario lo use en su propio dispositivo.
