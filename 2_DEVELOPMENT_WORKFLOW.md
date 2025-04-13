# 2. Flujo de Trabajo de Desarrollo

Este documento describe el flujo de trabajo, los procesos y las mejores prácticas que deben seguirse para el desarrollo consistente y de alta calidad de **todos** los paquetes dentro del ecosistema `luinuxscl/*` de la plataforma AI Content. La adherencia a este flujo es crucial dada la naturaleza multi-paquete de la arquitectura.

## 2.1. Principios Generales

* **Modularidad y Responsabilidad Única:** Cada paquete se enfoca en su dominio específico.
* **Consistencia:** Seguir los estándares de código, estructura y procesos definidos aquí y en `luinuxscl/ai-content-dev-docs`.
* **Comunicación Clara:** Uso de Issues, PRs descriptivos y documentación actualizada.
* **Calidad:** Énfasis en pruebas automatizadas y revisiones de código.

## 2.2. Ciclo de Desarrollo

Se sigue un ciclo iterativo estándar:

1.  **Planificación:**
    * Definición de historias de usuario, requisitos o bugs (usando GitHub Issues en el repositorio del paquete correspondiente).
    * Asignación de tareas.
    * Establecimiento de criterios de aceptación claros.
2.  **Implementación:**
    * Crear una rama `feature/*` o `bugfix/*` desde `develop`.
    * Desarrollo (preferiblemente TDD cuando aplique).
    * **Commits Atómicos:** Realizar commits pequeños y bien descritos (ver [Conventional Commits](https://www.conventionalcommits.org/)).
    * **Actualización Continua:** Mantener actualizada la documentación interna (DocBlocks), el `README.md` del paquete y el `AI_CONTEXT.md`.
    * **Refactoring Continuo:** Mejorar el código existente sin alterar su comportamiento externo.
3.  **Pruebas:**
    * Escribir **Unit Tests** para lógica interna (cobertura mínima >85%).
    * Escribir **Feature Tests** para probar la funcionalidad del paquete desde fuera (ej: llamando a sus servicios principales).
    * **Integración:** Asegurar que las interacciones con otros paquetes `luinuxscl/*` se basen en interfaces/eventos definidos. Las pruebas de integración *entre* paquetes se realizan principalmente en la capa de la aplicación (`luinuxscl/ai-content`). Ver sección `Testing_Strategy.md`.
4.  **Pull Request y Revisión:** (Ver sección 2.4).
5.  **Merge:** Una vez aprobado y con CI en verde, hacer merge a `develop`.
6.  **Release:** (Ver sección 2.7).

## 2.3. Gestión de Código Fuente (Git)

* **Un Repositorio por Paquete:** Cada paquete (`luinuxscl/laravel-workspaces`, etc.) y la documentación (`luinuxscl/ai-content-dev-docs`) viven en su propio repositorio Git.
* **Branching Model:** Se utiliza un flujo similar a GitFlow:
    * `main`: Contiene el código de las releases estables publicadas (tags). No se hace commit directo.
    * `develop`: Rama principal de desarrollo. Contiene la última versión en desarrollo, integrando features y bugfixes. Es la base para las ramas `feature/*` y `bugfix/*`.
    * `feature/<nombre-feature>`: Ramas para desarrollar nuevas funcionalidades. Se crean desde `develop` y se mergean de vuelta a `develop` vía PR.
    * `bugfix/<nombre-bugfix>`: Ramas para corregir errores en `develop`. Se crean desde `develop` y se mergean de vuelta a `develop` vía PR.
    * `release/<version>`: Ramas para preparar una nueva release (ej: `release/v1.2.0`). Se crean desde `develop`. Aquí se realizan los últimos ajustes, actualización de `CHANGELOG.md` y versionado. Se mergean a `main` (y se tagea) y también de vuelta a `develop`.

## 2.4. Pull Requests (PRs)

* **Alcance:** Un PR debe enfocarse idealmente en una única tarea (feature o bugfix) y afectar a **un solo paquete**. Si un cambio requiere modificaciones en múltiples paquetes, se deben crear **PRs separados y enlazados** (uno por cada repositorio afectado).
* **Base:** Los PRs de `feature/*` y `bugfix/*` siempre deben apuntar a la rama `develop`. Los PRs de `release/*` apuntan a `main` y `develop`.
* **Requisitos para Merge:**
    1.  Descripción detallada (usando la plantilla definida en `templates/PULL_REQUEST_TEMPLATE.md` si existe).
    2.  Enlace al Issue(s) de GitHub correspondiente(s).
    3.  Todos los tests automatizados (unit, feature) pasando en CI.
    4.  Cobertura de código mínima alcanzada (>85%).
    5.  Adherencia a los estándares de código (verificado por linters/static analysis en CI).
    6.  Documentación interna (DocBlocks), `README.md` y `AI_CONTEXT.md` actualizados.
    7.  Aprobación de al menos un miembro del equipo designado.
* **Revisiones de Código:** Se espera una revisión constructiva enfocada en la lógica, arquitectura, legibilidad, seguridad y adherencia a estándares.

## 2.5. Gestión de Dependencias (Inter-Paquete)

* Las dependencias entre paquetes `luinuxscl/*` deben definirse en `composer.json` usando **Versionado Semántico** compatible (ej: `"luinuxscl/laravel-workspaces": "^1.0"`).
* Actualizar las dependencias internas es parte del proceso de desarrollo y release de cada paquete.
* **Evitar dependencias circulares** a toda costa. Si surge la necesidad, reconsiderar la arquitectura o usar Eventos.

## 2.6. Versioning y Releases (Paquetes)

* **Versionado Semántico (SemVer):** Obligatorio seguir el formato `X.Y.Z` (Mayor.Menor.Parche).
    * `X (Mayor)`: Cambios incompatibles con versiones anteriores (breaking changes).
    * `Y (Menor)`: Nuevas funcionalidades compatibles con versiones anteriores.
    * `Z (Parche)`: Correcciones de errores compatibles con versiones anteriores.
* **Proceso de Release (por Paquete):**
    1.  Crear rama `release/vX.Y.Z` desde `develop`.
    2.  Realizar ajustes finales (si los hubiera).
    3.  Actualizar `CHANGELOG.md` detallando los cambios desde la última versión.
    4.  Actualizar el número de versión en `composer.json` (si aplica).
    5.  Crear PR de `release/vX.Y.Z` hacia `main` y `develop`.
    6.  Una vez aprobado y mergeado a `main`: Crear un **tag Git** (ej: `git tag vX.Y.Z`).
    7.  Hacer push del tag (`git push origin vX.Y.Z`).
    8.  (Opcional) Publicar en Packagist/Satis si son paquetes públicos/privados distribuidos.
* **Breaking Changes:** Deben minimizarse. Si son necesarios, requieren una **nueva versión Mayor (X)**, documentación clara en el `CHANGELOG.md` sobre cómo migrar, y comunicación al equipo para coordinar actualizaciones en paquetes dependientes.

## 2.7. Testing

* Consultar `PACKAGE_DEVELOPMENT_GUIDE/Testing_Strategy.md` para detalles.
* **Unit & Feature Tests:** Responsabilidad de cada paquete individual. Deben ejecutarse en CI para cada PR.
* **Integration Tests:** Pruebas que verifican la correcta interacción entre varios paquetes. Se recomienda ejecutarlas principalmente dentro del contexto de la aplicación integradora (`luinuxscl/ai-content`) para simular el entorno real.

## 2.8. Estándares y Calidad de Código

* Consultar el directorio `3_CODING_STANDARDS/` para reglas específicas (PSR-12, Naming, etc.).
* Se utilizarán herramientas automatizadas en **CI (GitHub Actions)** para verificar estos estándares en cada PR (PHP CS Fixer, ESLint, PHPStan/Psalm).