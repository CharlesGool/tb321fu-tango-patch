---
name: project-log-es
description: Errores, limitaciones, decisiones y traspaso del proyecto
metadata:
  version: "1.0.0"
  lang: "es"
---

# Registro

## Multilingüe

[简体中文](../LOG.md) | [English](../en/LOG.md) | **Español**

## Documentación

- Descripción general del proyecto: [README](README.md)

- Justificación del diseño: [DESIGN](DESIGN.md)

- Estado del proyecto: [LOG](LOG.md)
- Registros históricos: [HISTORY](HISTORY.md)
- Historial de cambios: [CHANGELOG](CHANGELOG.md)

- Avisos de terceros: [THIRD_PARTY_NOTICES](THIRD_PARTY_NOTICES.md)

## Registros

- [Registros históricos](HISTORY.md)
- [Historial de cambios](CHANGELOG.md)

## Errores

- [ ] Con el valor por defecto `ENFORCE=0`, `tango-guard.sh` borra el marcador `fallback` al arrancar, por lo que el marcador de retroceso escrito por `heraldctl tango off` no surte efecto.
- [ ] Tango v2.1.0 en QEMU se cuelga de forma ocasional cuando un programa de 32 hilos toma la ruta fatal, o informa SIGABRT en lugar de SIGSEGV (v2.0.7 también); no se ha observado en dispositivo real.
- [ ] `panic_on_walt_bug=0` desactiva también el resto de las comprobaciones de BUG de WALT de Lenovo.
- [ ] La compatibilidad del Adreno de 32 bits `V@0762.4` con el `V@0762.28` de 64 bits del dispositivo y con el KGSL del kernel no se ha verificado por separado.

## Limitaciones

- No se puede iniciar el zygote de 32 bits bajo demanda ni pretraducir: ambas cosas requieren modificar el framework o el código fuente de installd, algo que una GSI binaria no permite.
- No se puede distribuir un paquete de flasheo completo: el EULA de Tango prohíbe copiar y redistribuir, y las bibliotecas de Xiaomi son archivos propietarios.
- `tango32.ko` solo sirve para un kernel con exactamente la misma cadena de versión, y **MUST** recompilarse al cambiar el boot original.

## Decisiones

| Fecha | Decisión | Estado | Motivo |
| --- | --- | --- | --- |
| 2026-10-31 | No modificar `ro.zygote`; incluir un servicio zygote de 32 bits propio | Aceptada | El `ro.zygote` del vendor sobrescribe el valor de system, y el rc original introduciría la autocomprobación de 32 bits y el reinicio en cadena. |
| 2026-10-31 | `ro.dalvik.vm.enable_uffd_gc=false` | Aceptada | Tango no admite userfaultfd, así que el ART de 32 bits solo puede usar el GC CC. |
| 2026-10-31 | La protección de arranque escribe `panic_on_walt_bug=0` para corregir el reinicio del equipo | Aceptada | Modificar `sched-walt.ko` exigiría reempaquetar `vendor_boot`; el sysctl ya se verificó como eficaz en dispositivo real. |
| 2026-10-31 | Cambiar a las bibliotecas GPU de 32 bits del OnePlus 12 | Rechazada | La causa real era la autocomprobación de WALT; las bibliotecas actuales funcionan bien en dispositivo real. |
| 2026-11-01 | Fusionar 5 parches independientes en un solo parche con 5 partes | Aceptada | Las partes deben usarse juntas por su funcionalidad. |
| 2026-11-05 | Separar en un repositorio independiente, como única fuente del parche, y Mods conserva una copia sincronizada | Aceptada | Coincide con la organización de herald-core y facilita el mantenimiento y la publicación por separado. |
| 2026-11-05 | Parche y tutorial general de portado en dos repositorios públicos, con las notas de investigación en el repositorio del tutorial | Aceptada | El tutorial es para todos los dispositivos; el parche solo es válido para la TB321FU. |

## Traspaso

Consulte el traspaso vigente en el [LOG.md en chino simplificado](../LOG.md#交接).
