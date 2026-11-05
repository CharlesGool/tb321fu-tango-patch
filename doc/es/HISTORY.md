---
name: project-history-es
description: Registros históricos
metadata:
  version: "1.0.0"
  lang: "es"
---

# Registros históricos

## Multilingüe

[简体中文](../HISTORY.md) | [English](../en/HISTORY.md) | **Español**

## Documentación

- Descripción general del proyecto: [README](README.md)

- Justificación del diseño: [DESIGN](DESIGN.md)

- Estado del proyecto: [LOG](LOG.md)
- Registros históricos: [HISTORY](HISTORY.md)
- Historial de cambios: [CHANGELOG](CHANGELOG.md)

- Avisos de terceros: [THIRD_PARTY_NOTICES](THIRD_PARTY_NOTICES.md)

## Registros históricos

### Pruebas en dispositivo real

| Versión | Fecha | Síntoma | Causa y tratamiento |
| --- | --- | --- | --- |
| V68 | 2026-10 | Bucle de arranque en la primera pantalla, `reboot,boringssl-self-check-failed` | El programa de autocomprobación de BoringSSL de 32 bits del vendor de Lenovo no existe y tiene `reboot_on_failure`; no tiene relación con Tango; se cambió para asumir la autocomprobación del vendor |
| V77 | 2026-10 | Se detiene en la segunda pantalla, system_server falla cada 60 segundos | El `ro.zygote=zygote64_32` de system fue sobrescrito por el `zygote64` del vendor, el zygote de 32 bits no arrancó y `abilist32` ya estaba declarado |
| V78 | 2026-10 | Se detiene en la segunda pantalla, el zygote de 32 bits se reinicia cada 5 segundos | El `insmod` de init en `early-init` no cargó el módulo, y la comprobación CFI de las bibliotecas de 32 bits dio SIGILL |
| V79 | 2026-10 | Reinicio forzado del equipo a mitad de la animación de arranque | El dominio herald (entonces llamado omk) cargó el módulo con éxito en `post-fs-data`, y el zygote de 32 bits se reinició tras unos 8 segundos de ejecución; además se detectó que la imagen de arranque de 32 bits no coincidía con el GC CC |
| V80, V81 | 2026-10 | Arranca sin declarar el ABI de 32 bits | El módulo se puede cargar en `post-fs-data`; se cambió a cargarlo en ese momento |
| V82 | 2026-10 | Con protección de arranque, mismo síntoma que V79 | La protección retrocede a solo 64 bits en el siguiente arranque |
| V881 | 2026-10-31 | Reinicio del equipo unos 25 segundos después de ejecutarse el zygote de 32 bits | Durante el retroceso, el `init.rc` original lanzaba `zygote_secondary` y provocaba `updatable_crashing`; `enable_uffd_gc` solo en system no surtía efecto; no se borraba el marcador antiguo; `guard.txt` se sobrescribía. V882 lo corrigió todo y el servicio pasó a llamarse `tango_zygote32` |
| V882 | 2026-10-31 | `BUG` del kernel unos 28 segundos después de arrancar con 32 bits | console-ramoops apuntó a la autocomprobación de tareas de 32 bits de `sched-walt.ko` de Lenovo |
| V883 | 2026-10-31 | Los APK de 32 bits se ejecutan con normalidad | La protección de arranque escribe `panic_on_walt_bug=0`. Confirmado por el usuario |

V68 a V883 son los números de versión globales del proyecto predecesor de TB321FU-GSI-Mods, no los de este parche.

### Cambios de organización

- 2026-10-31: los 5 módulos pasaron de `mods/tango/` a `patches/tango/` de Mods.
- 2026-11-01: los 5 parches se fusionaron en un solo parche `required/general/tango` con 5 partes, versión del parche V1.0.
- Principios de 2026-11 (fecha sin verificar): el nombre en tiempo de ejecución pasó de omk a herald (`heraldctl`, dominio herald).
- 2026-11-05: se separó en el repositorio privado independiente tb321fu-tango. Ese mismo día el repositorio se renombró a tango-porting-guide y pasó a ser un tutorial general de portado, el parche se extrajo a este repositorio tb321fu-tango-patch y las notas de investigación quedaron en el repositorio del tutorial.
