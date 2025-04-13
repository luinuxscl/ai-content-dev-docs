# 4.6 Versionado y Lanzamiento de Paquetes (Versioning & Releases)

Este documento define el proceso estricto para asignar versiones y lanzar (release) nuevas actualizaciones de los paquetes individuales `luinuxscl/*`. Seguir este proceso es fundamental para gestionar dependencias, comunicar cambios efectivamente a través de los `CHANGELOG.md`, y mantener la estabilidad del ecosistema completo.

## 1. Versionado Semántico (SemVer 2.0.0)

* **Obligatorio:** Todos los paquetes `luinuxscl/*` **DEBEN** adherirse estrictamente a la especificación de Versionado Semántico 2.0.0. Puedes consultar la especificación completa en [https://semver.org/lang/es/](https://semver.org/lang/es/).
* **Formato:** La versión de un paquete se expresa como `X.Y.Z`:
    * **X (Mayor/Major):** Se incrementa solo para cambios **incompatibles** con la API pública (breaking changes). Al incrementar X, Y y Z se resetean a 0.
    * **Y (Menor/Minor):** Se incrementa al añadir **nueva funcionalidad** de manera **compatible** con la versión anterior. Al incrementar Y, Z se resetea a 0.
    * **Z (Parche/Patch):** Se incrementa para **correcciones de errores** (bug fixes) **compatibles** con la versión anterior.
* **Versión Inicial:** Los paquetes en desarrollo inicial comienzan en `0.1.0`. La versión `1.0.0` se asigna cuando el paquete alcanza una API pública considerada estable. Versiones `0.x.y` indican que la API pública aún puede tener breaking changes en versiones menores.
* **API Pública del Paquete:** Es crucial tener una idea clara de qué constituye la API pública de cada paquete para versionar correctamente. Esto incluye, pero no se limita a:
    * Firmas de métodos públicos en Servicios clave.
    * Interfaces públicas definidas en `Contracts/`.
    * Clases de Eventos públicos y la estructura de su payload.
    * Clases DTO públicas utilizadas en la comunicación.
    * Comandos de Artisan y sus argumentos/opciones.
    * Rutas API (si las define el paquete).
    * Estructura de archivos de configuración publicados.
    * Estructura de tablas de base de datos y migraciones (los cambios pueden requerir versiones mayores si afectan a datos existentes de forma incompatible).

## 2. Registro de Cambios (`CHANGELOG.md`)

* **Obligatorio:** Cada paquete **DEBE** tener un archivo `CHANGELOG.md` en su directorio raíz.
* **Formato:** Utilizar el formato especificado en [Keep a Changelog](https://keepachangelog.com/es/1.0.0/).
* **Contenido:** Documentar todos los cambios significativos para cada versión, agrupados bajo las categorías: `Added` (nuevo), `Changed` (cambios), `Deprecated` (obsoleto), `Removed` (eliminado), `Fixed` (corregido), `Security` (seguridad). Mantener una sección `[Unreleased]` para los cambios en desarrollo.
* **Actualización:** Es responsabilidad del desarrollador añadir entradas al `CHANGELOG.md` como parte de sus Pull Requests. El responsable del release revisará y finalizará el changelog antes de etiquetar la versión.

## 3. Proceso Detallado de Release de un Paquete

Este proceso se ejecuta cuando se decide que los cambios acumulados en la rama `develop` están listos para una nueva versión:

1.  **Determinar Nueva Versión:** Basándose en los cambios acumulados en `develop` desde el último tag y siguiendo SemVer, decidir el número de la nueva versión (ej: `v1.2.3`, `v1.3.0`, `v2.0.0`).
2.  **Crear Rama Release:** Desde la rama `develop` actualizada:
    ```bash
    git checkout -b release/vX.Y.Z develop
    ```
3.  **Finalizar `CHANGELOG.md`:** Mover las entradas de `[Unreleased]` a una nueva sección `[vX.Y.Z] - YYYY-MM-DD`. Asegurarse de que todos los cambios estén bien descritos.
4.  **Actualizar Versión (si aplica):** Si el paquete expone su versión en algún lugar (ej: una constante en el ServiceProvider), actualizarla.
5.  **Commit de Preparación:** Realizar un commit con los cambios del changelog y versión:
    ```bash
    git commit -am "chore: Prepare release vX.Y.Z"
    ```
6.  **Merge a `main`:**
    * Crear un Pull Request desde `release/vX.Y.Z` hacia `main`.
    * Realizar una última revisión.
    * Hacer merge del PR a `main`.
7.  **Crear Tag en `main`:**
    * Cambiar a la rama `main`: `git checkout main`
    * Asegurarse de tener la última versión: `git pull origin main`
    * Crear el tag Git anotado: `git tag -a vX.Y.Z -m "Release version vX.Y.Z"`
    * Empujar el tag al repositorio remoto: `git push origin vX.Y.Z`
8.  **Merge de Vuelta a `develop`:**
    * Crear un Pull Request desde `release/vX.Y.Z` (o desde `main` ahora que tiene el merge) hacia `develop`. Esto asegura que los cambios hechos durante el release (como el `CHANGELOG.md` finalizado) vuelvan a la línea principal de desarrollo.
    * Hacer merge del PR a `develop`.
9.  **Limpieza (Opcional):** Eliminar la rama `release/vX.Y.Z` local y remotamente si ya no es necesaria.
10. **Publicación Externa (Si aplica):** Si el paquete se distribuye vía Packagist (público) o Satis/Artifactory (privado), el push del tag generalmente activará la actualización del paquete en el repositorio correspondiente.

## 4. Manejo de Cambios Incompatibles (Breaking Changes - Versión Mayor)

* **Minimizar:** Los breaking changes deben evitarse tanto como sea posible, especialmente en paquetes base muy utilizados por otros.
* **Justificación y Planificación:** Deben estar bien justificados y planificados. Discutir el cambio con los equipos afectados antes de implementarlo.
* **Comunicación Clara:** Notificar con antelación sobre el próximo release mayor y los cambios que introduce.
* **Documentación Exhaustiva:** Proveer una **guía de migración** detallada en el `CHANGELOG.md` y/o `README.md` explicando qué cambió, por qué, y cómo deben los consumidores adaptar su código.
* **Incremento de Versión Mayor:** Incrementar `X` y resetear `Y.Z` a `0` (ej: `v1.7.2` -> `v2.0.0`).
* **Actualización Coordinada:** Los paquetes consumidores **DEBEN** actualizar explícitamente su dependencia en `composer.json` a la nueva versión mayor (ej: `^2.0`) y realizar los cambios necesarios siguiendo la guía de migración. Este proceso requiere coordinación entre equipos.

## 5. Dependencias y Compatibilidad en Releases

* Al preparar una release `vX.Y.Z` de un paquete `A`, sus pruebas automatizadas **DEBEN** pasar usando las versiones de sus dependencias (incluyendo otros paquetes `luinuxscl/*` y `illuminate/*`) tal como están definidas en su `composer.json` (respetando los rangos `^` o `|`).
* Si durante el desarrollo de la versión `vX.Y.Z` de `A`, se requiere una **nueva funcionalidad** introducida en una versión específica de un paquete `B` (ej: `v1.3.0`), entonces `A` **DEBE** actualizar su requisito en `composer.json` a `"luinuxscl/paquete-b": "^1.3"` (o superior compatible) *antes* de lanzar su propia versión `vX.Y.Z`.

## 6. Releases de la Aplicación Principal (`luinuxscl/ai-content`)

* La aplicación principal `luinuxscl/ai-content` sigue su propio ciclo de versionado y release/despliegue.
* El archivo `composer.json` de `ai-content` define las versiones específicas (o rangos `^`) de todos los paquetes `luinuxscl/*` que integra.
* Para lanzar una nueva versión de `ai-content`, se deben realizar los siguientes pasos (aproximadamente):
    1. Decidir qué versiones de los paquetes `luinuxscl/*` se incluirán.
    2. Actualizar el `composer.json` de `ai-content` con esas versiones.
    3. Ejecutar `composer update luinuxscl/*`.
    4. Realizar **pruebas de integración exhaustivas** de la aplicación completa.
    5. Seguir el proceso de release/despliegue específico de la aplicación `ai-content`.