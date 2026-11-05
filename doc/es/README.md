# tb321fu-tango-patch

<!-- Descomenta la siguiente línea cuando el proyecto tenga un icono -->
<!-- <img align="right" src="../resources/logo.png" width="160" alt="tb321fu-tango-patch"> -->

[简体中文](../../README.md) | [English](../en/README.md) | **Español**

La CPU de la Legion Y700 de tercera generación (TB321FU) no tiene AArch32, por lo que tras flashear una GSI no puede ejecutar APK de 32 bits; este proyecto es un parche de TB321FU-GSI-Mods que integra el traductor binario Tango en la imagen system para ejecutar APK de 32 bits sin root.

[![License](https://img.shields.io/badge/License-GPL--3.0--or--later-orange)](../../LICENSE)

## Documentación

- Descripción general del proyecto: [README](README.md)

- Justificación del diseño: [DESIGN](DESIGN.md)

- Estado del proyecto: [LOG](LOG.md)
- Registros históricos: [HISTORY](HISTORY.md)
- Historial de cambios: [CHANGELOG](CHANGELOG.md)

- Avisos de terceros: [THIRD_PARTY_NOTICES](THIRD_PARTY_NOTICES.md)

## Introducción

- [Tango](https://www.amanieusystems.com/) traduce programas ARM de 32 bits a arm64 para ejecutarlos; los APK de 32 bits se pueden instalar y ejecutar sin root.
- El parche `tango` consta de 5 partes: el traductor y el registro de `binfmt_misc`, la política SELinux, el módulo de kernel `tango32`, las bibliotecas GPU de 32 bits, y el zygote de 32 bits con la protección de arranque. La descripción de cada parte está en [patch/README.md](../../patch/README.md).
- Resuelve el bucle de arranque y los reinicios del equipo que aparecían al declarar el ABI de 32 bits en el vendor de Lenovo; la causa se explica en las [notas de investigación](https://github.com/CharlesGool/tango-porting-guide/blob/main/doc/research.md).
- V883 (2026-10-31) superó las pruebas en dispositivo real. Los problemas conocidos están en [LOG](LOG.md#errores).

El método de portado independiente de un dispositivo concreto está en [tango-porting-guide](https://github.com/CharlesGool/tango-porting-guide); este repositorio es su implementación de referencia.

Este repositorio es la única fuente de este parche. `patches/required/general/tango/` de [TB321FU-GSI-Mods](https://github.com/CharlesGool/TB321FU-GSI-Mods) es una copia sincronizada.

## Requisitos

- Legion Y700 de tercera generación (TB321FU), vendor original de Lenovo con Android 15, kernel de boot GKI `6.1.112-android14-11-gd03cb1d7abcd-ab12592111`
- Construir la imagen system con [TB321FU-GSI-Mods](https://github.com/CharlesGool/TB321FU-GSI-Mods) y habilitar el parche [herald-core](https://github.com/CharlesGool/herald-core)
- El usuario debe aportar lo siguiente; **MUST NOT** subirse al repositorio ni incluirse en el paquete de flasheo distribuido:
  - El paquete de integración Tango v2.1.0 (necesita `prebuilts/tango_translator`), que se obtiene de Amanieu Systems cumpliendo su EULA
  - `tango32.ko` compilado para el kernel anterior; la forma de compilarlo está en [kernel-module](../../patch/parts/003.kernel-module/README.md#编译-tango32ko)
  - Las 30 bibliotecas de 32 bits de `vendor/lib` de xiaomi.eu OS3.0.304.0 del Xiaomi 14 Ultra; la lista de archivos está en [manifest.sha256](../../patch/parts/004.vendor-lib32/manifest.sha256)
  - La imagen vendor original

## Instalación

Este proyecto no se instala por separado; TB321FU-GSI-Mods lo integra en la imagen system durante la construcción.

### Instalación rápida

TB321FU-GSI-Mods ya incluye una copia sincronizada, así que la construcción por defecto contiene este parche. Coloque las entradas anteriores en las ubicaciones por defecto del repositorio Mods y construya:

```bash
make 9008 NOTE="tango"
```

Las ubicaciones por defecto son `private/tango/pkg/`, `private/tango/tango32.ko` y `private/xiaomi-vendor-lib32/` del repositorio Mods.

### Instalación normal

Sustituya la copia de Mods por la versión de este repositorio y construya de nuevo:

```bash
rsync -a --delete patch/ <TB321FU-GSI-Mods>/patches/required/general/tango/
```

## Uso

### Parámetros

Los parámetros se definen en `devices/<dispositivo>/conf.d/tango.conf` de Mods; una variable de entorno con el mismo nombre tiene prioridad.

| Parámetro | Significado |
| --- | --- |
| `TANGO_PKG` | Directorio del paquete de integración Tango |
| `TANGO32_KO` | Ruta de `tango32.ko` |
| `TANGO_KERNEL_RELEASE` | Cadena de versión del kernel de destino; en la construcción se compara con el `vermagic` del .ko |
| `XIAOMI_LIB32_DIR` | Directorio de las bibliotecas de 32 bits, con la misma estructura que `/vendor/lib` |
| `STOCK_VENDOR_IMG` | Imagen vendor original (ya definida en el `device.conf` de Mods) |
| `TANGO_DEBUG` | Nivel de registro del traductor: `off`, `info` o `debug`; si no es `off`, se escribe en `/metadata/tango_log` |

### Comprobación y resolución de problemas

```bash
adb shell heraldctl tango status        # módulo, ABI de 32 bits, si hay retroceso
adb shell heraldctl tango logs          # exporta los registros a /data/local/tmp/tango_log
adb pull /data/local/tmp/tango_log
```

Para otros comandos y la protección de arranque, consulte [zygote32](../../patch/parts/005.zygote32/README.md#heraldctl-命令).

## Actualización

Nada por ahora.

## Desinstalación

### Desinstalación rápida

Desactiva la traducción de inmediato sin flashear y se restablece al reiniciar (el ABI de 32 bits sigue declarado, pero los APK de 32 bits no se pueden ejecutar):

```bash
adb shell setprop tango.enabled 0
```

### Desinstalación completa

Quite este parche en Mods, construya de nuevo y flashee:

```bash
make 9008 WITHOUT=tango NOTE="sin 32 bits"
```

## Agradecimientos

- [Tango](https://www.amanieusystems.com/) de Amanieu Systems: origen del traductor, del módulo de kernel `tango32` y de la política SELinux
- La integración oficial de Tango en el Xiaomi 14 Ultra y el OnePlus 12: referencia para el zygote de 32 bits y el tratamiento del GC de ART

## Licencia

Este proyecto se publica bajo la GNU General Public License v3.0 o posterior, identificador SPDX `GPL-3.0-or-later`; el texto completo está en [LICENSE](../../LICENSE). Tango, el módulo `tango32` y las bibliotecas de Xiaomi no se distribuyen con este proyecto; sus licencias respectivas están en [THIRD_PARTY_NOTICES](THIRD_PARTY_NOTICES.md).
