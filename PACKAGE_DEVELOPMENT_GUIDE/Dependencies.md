# 4.3 Gestión de Dependencias

Este documento establece las directrices y políticas para gestionar las dependencias (secciones `require` y `require-dev` en `composer.json`) dentro de todos los paquetes `luinuxscl/*`. Una gestión adecuada de dependencias es crucial para asegurar la estabilidad, compatibilidad, mantenibilidad y seguridad del ecosistema de paquetes y de las aplicaciones que los consuman.

## 1. Principios Generales

* **Minimizar Dependencias:** Incluir únicamente las dependencias (tanto externas como internas de `illuminate/*` o `luinuxscl/*`) que sean **estrictamente necesarias** para la funcionalidad principal del paquete. Evitar añadir librerías por conveniencia si la funcionalidad puede lograrse razonablemente con código propio o funcionalidades existentes de Laravel.
* **Preferir Componentes `illuminate/*`:** Para funcionalidades del framework Laravel, depender siempre de los paquetes específicos `illuminate/*` requeridos, en lugar del metapaquete `laravel/framework`.
* **Versiones Compatibles (SemVer):** Utilizar el operador `^` (caret) para las restricciones de versión siempre que sea posible, asumiendo que las dependencias siguen el Versionado Semántico (SemVer). Esto permite actualizaciones menores y de parches sin intervención manual. Para dependencias críticas como `illuminate/*`, especificar rangos compatibles con las versiones de Laravel soportadas (ej: `^11.0|^12.0`).
* **Seguridad y Mantenimiento:** Evaluar la reputación, actividad de mantenimiento y vulnerabilidades conocidas de las dependencias externas antes de incluirlas.

## 2. Dependencias de Laravel (`illuminate/*`)

* **NUNCA incluir `"laravel/framework"`** en la sección `require`. Esto introduce dependencias redundantes y potenciales conflictos de versión cuando el paquete se instala en una aplicación Laravel existente.
* **Incluir solo los componentes `illuminate/*` específicos** que el paquete utiliza directamente. Ejemplos comunes:
    * `illuminate/support`: Para Service Providers, Collections, Helpers, Facades base, etc. (casi siempre necesario).
    * `illuminate/database`: Si se usa Eloquent, Query Builder, Migraciones, Seeders, Factories.
    * `illuminate/contracts`: Si se implementan o dependen de interfaces centrales de Laravel.
    * `illuminate/http`: Si se interactúa directamente con `Request` / `Response`.
    * `illuminate/console`: Si el paquete define comandos Artisan.
    * `illuminate/queue`: Si el paquete define Jobs para colas.
    * `illuminate/events`: Si se despachan o escuchan eventos del bus de Laravel.
    * `illuminate/filesystem`: Si se interactúa con discos de almacenamiento.
    * `illuminate/validation`: Si se definen Reglas de Validación personalizadas.
    * `illuminate/routing`: Si se necesita interactuar con el Router directamente.
* **Especificar Rangos de Versión Compatibles:** Usar la sintaxis `|` (pipe) para indicar compatibilidad con múltiples versiones mayores de Laravel soportadas por el ecosistema.
    ```json
    "require": {
        "php": "^8.2",
        "illuminate/support": "^11.0|^12.0",
        "illuminate/database": "^11.0|^12.0",
        "illuminate/contracts": "^11.0|^12.0"
    }
    ```

## 3. Dependencias Internas (Otros Paquetes `luinuxscl/*`)

* Si un paquete `luinuxscl/paquete-a` requiere funcionalidad de `luinuxscl/paquete-b`:
    * Añadir `"luinuxscl/paquete-b"` a la sección `require` de `composer.json` en `paquete-a`.
    * Usar el operador `^` para la restricción de versión, confiando en que todos los paquetes `luinuxscl/*` siguen SemVer estrictamente (ej: `"luinuxscl/laravel-workspaces": "^1.0"`).
    * Asegurar que la dependencia es necesaria y que la interacción se realiza preferentemente a través de **Interfaces o Eventos** definidos en `paquete-b` para mantener bajo acoplamiento (ver `Communication_Between_Packages.md`).
    * **Evitar dependencias circulares**. Si parece necesaria, es un indicador de que la arquitectura o la distribución de responsabilidades necesita revisión.

## 4. Dependencias Externas (Third-Party)

* **Evaluar Necesidad:** Antes de añadir una librería externa, considerar si su funcionalidad es esencial y si no puede ser implementada de forma razonable internamente o con herramientas ya disponibles en Laravel.
* **Investigar la Librería:**
    * **Popularidad y Mantenimiento:** Preferir librerías activamente mantenidas, con una comunidad activa y buena documentación. Revisar la frecuencia de commits, issues abiertos/cerrados, y la versión estable más reciente.
    * **Seguridad:** Usar `composer audit` regularmente. Estar al tanto de vulnerabilidades conocidas.
    * **Licencia:** Asegurarse de que la licencia de la librería (ej: MIT, Apache 2.0) sea compatible con las políticas del proyecto `luinuxscl/*`. Evitar licencias restrictivas (ej: GPL, AGPL) si se planea distribución o uso en contextos que puedan entrar en conflicto.
* **Restricción de Versión:** Usar `^` para permitir actualizaciones compatibles según SemVer. Evitar fijar versiones exactas (`1.2.3`) a menos que haya una razón de incompatibilidad muy específica y documentada.
* **Evitar Dependencias ya Provistas por el Entorno Base:** No añadir dependencias en `require` que se sabe que ya forman parte del **Starter Kit Personalizado** base sobre el cual se instalarán los paquetes (ej: `spatie/laravel-permission`, `livewire/livewire`). La aplicación final ya las proveerá. Añadirlas aquí puede causar conflictos de versión. *(Sí pueden estar en `require-dev` si son necesarias para las pruebas del paquete)*.

## 5. Dependencias de Desarrollo (`require-dev`)

* Incluir **únicamente** herramientas necesarias para el desarrollo, testing y análisis de calidad **del propio paquete**.
* Ejemplos comunes: `phpunit/phpunit`, `orchestra/testbench`, `pestphp/pest`, `laravel/pint`, `phpstan/phpstan`, `mockery/mockery`, `fakerphp/faker`.
* Estas dependencias no se instalan cuando el paquete es usado como dependencia en una aplicación final.

## 6. Revisión y Actualización

* Revisar periódicamente las dependencias desactualizadas usando `composer outdated`.
* Gestionar las actualizaciones de dependencias de forma controlada, especialmente las actualizaciones mayores (breaking changes), probando la compatibilidad exhaustivamente.
* Mantener las restricciones de versión de PHP y `illuminate/*` alineadas con las versiones soportadas por el ecosistema general.