# 4.2 Creando un Nuevo Paquete

Esta guía proporciona un checklist paso a paso para crear un nuevo paquete `luinuxscl/*` dentro del ecosistema de la plataforma AI Content. Seguir estos pasos asegura que el nuevo paquete se adhiera a la estructura, estándares y convenciones definidas desde el principio.

## Prerrequisitos

* Acceso a la terminal (Warp o similar).
* Git instalado y configurado.
* Composer instalado.
* PHP instalado (versión requerida por el proyecto, ej: 8.2+).
* Node.js y NPM instalados (si el paquete manejará assets JS/CSS).
* Acceso a GitHub (u otro proveedor Git) para crear el repositorio.

## Pasos para la Creación

1.  **Definir Propósito y Alcance:**
    * Clarifica la **responsabilidad única** del nuevo paquete. ¿Qué problema resuelve?
    * Verifica que esta funcionalidad no exista ya o pueda incorporarse lógicamente en un paquete existente.
    * Define los límites claros del paquete (qué hará y qué *no* hará).

2.  **Elegir Nombre del Paquete:**
    * Sigue la convención: `luinuxscl/laravel-<nombre-descriptivo-en-kebab-case>`.
    * Ejemplo: `luinuxscl/laravel-notifications-manager`.

3.  **Crear Repositorio Remoto:**
    * Crea un nuevo repositorio en GitHub (u otro proveedor) con el nombre elegido (ej: `laravel-notifications-manager`).
    * Añade una descripción breve.
    * Elígelo como Privado o Público según corresponda.
    * **Importante:** NO inicializar con README, .gitignore o licencia desde GitHub.
    * Copia la URL (SSH o HTTPS) del repositorio.

4.  **Clonar Repositorio Localmente:**
    * En tu terminal, navega a tu directorio de desarrollo.
    * Clona el repositorio: `git clone <URL_DEL_REPOSITORIO>`
    * Entra al directorio: `cd <nombre-del-repositorio>`

5.  **Inicializar Composer:**
    * Ejecuta: `composer init`
    * Sigue los pasos interactivos:
        * **Name:** `luinuxscl/laravel-<nombre-del-paquete>` (ej: `luinuxscl/laravel-notifications-manager`)
        * **Description:** Descripción clara.
        * **Author:** Tu nombre y correo.
        * **Minimum Stability:** `stable` (usualmente).
        * **Package Type:** `library` o `laravel-package`.
        * **License:** `proprietary` (o `MIT`, etc., según tu política).
        * **Define dependencies (require):** Empieza de forma mínima. Incluye siempre `php` y los componentes `illuminate/*` necesarios. Consulta `Dependencies.md` para la política.
            ```json
            "php": "^8.2",
            "illuminate/contracts": "^11.0|^12.0",
            "illuminate/support": "^11.0|^12.0",
            // Añadir otros como illuminate/database, illuminate/http si son necesarios
            ```
        * **Define dev-dependencies (require-dev):** Incluye herramientas de testing y calidad.
            ```json
            "laravel/pint": "^1.0", // O php-cs-fixer
            "orchestra/testbench": "^9.0|^10.0",
            "pestphp/pest": "^2.0", // O phpunit/phpunit
            "phpstan/phpstan": "^1.10" // O psalm
            ```
        * Confirma la generación.

6.  **Configurar Autoloading (PSR-4):**
    * Edita el archivo `composer.json` generado.
    * Asegúrate de que la sección `autoload` y `autoload-dev` estén configuradas correctamente (ajusta `PackageName` al nombre `StudlyCaps` de tu paquete, ej: `NotificationsManager`):
        ```json
        "autoload": {
            "psr-4": {
                "LuinuxScl\\PackageName\\": "src/"
            }
        },
        "autoload-dev": {
            "psr-4": {
                "LuinuxScl\\PackageName\\Tests\\": "tests/"
            }
        }
        ```
    * Ejecuta `composer dump-autoload`.

7.  **Configurar Descubrimiento de Paquete Laravel:**
    * Añade la sección `extra.laravel` a `composer.json` para que Laravel pueda descubrir automáticamente el Service Provider principal (ajusta `PackageName`):
        ```json
        "extra": {
            "laravel": {
                "providers": [
                    "LuinuxScl\\PackageName\\PackageNameServiceProvider"
                ]
            }
        }
        ```

8.  **Crear Estructura de Directorios Base:**
    * Crea los directorios necesarios según `Structure.md` (`src`, `tests`, `config`, `database/migrations`, etc.).
    * Usa `mkdir` y `touch`. Añade archivos `.gitkeep` a directorios que estarán vacíos inicialmente pero que quieres que Git rastree (ej: `touch config/.gitkeep database/migrations/.gitkeep`).
    * Ejemplo: `mkdir -p src config database/migrations database/factories tests/Unit tests/Feature resources/lang/en resources/views`

9.  **Crear Service Provider Básico:**
    * Crea el archivo `src/PackageNameServiceProvider.php` (ej: `src/NotificationsManagerServiceProvider.php`).
    * Extiende `Illuminate\Support\ServiceProvider`.
    * Implementa métodos `register()` (para bindings en el contenedor) y `boot()` (para publicar assets, registrar rutas, comandos, etc.). Deja los métodos vacíos inicialmente si no necesitas nada aún.

10. **Añadir Archivos Base del Repositorio:**
    * Crea los archivos raíz: `README.md`, `CHANGELOG.md`, `LICENSE.md`, `.gitignore`, `phpunit.xml` (o `pest.xml`), `AI_CONTEXT.md`.
    * Puedes copiar plantillas desde `luinuxscl/ai-content-dev-docs/templates/` si existen.
    * Añade contenido básico al `README.md` (propósito, instalación mínima) y `AI_CONTEXT.md`.
    * Configura un `.gitignore` apropiado para paquetes Laravel (ignorar `vendor/`, `.env`, `storage/`, etc.).

11. **Configurar Entorno de Pruebas:**
    * Crea `tests/TestCase.php`. Haz que extienda `Orchestra\Testbench\TestCase`.
    * Sobrescribe el método `getPackageProviders($app)` para que devuelva tu `PackageNameServiceProvider::class`.
    * Configura cualquier otro detalle necesario para que las pruebas funcionen en el entorno de Testbench (migraciones, variables de entorno de prueba).
    * Crea un test simple de ejemplo en `tests/Unit` o `tests/Feature` para verificar que la configuración funciona.

12. **Configurar Herramientas de Desarrollo:**
    * Añade archivos de configuración para las herramientas de calidad si es necesario (ej: `pint.json`, `phpstan.neon`).
    * Si usas JS/CSS, crea `package.json` (`npm init -y`), instala dependencias (`npm install`) y añade scripts para linting, formateo y compilación en la sección `"scripts"`.

13. **Commit Inicial:**
    * Añade todos los archivos creados: `git add .`
    * Realiza el commit: `git commit -m "feat: Initial package setup for LuinuxScl/PackageName"`
    * Sube la rama principal: `git push origin main` (o la rama por defecto).

14. **Iniciar Desarrollo (Rama `develop`):**
    * Crea y cambia a la rama `develop`: `git checkout -b develop`
    * Sube la rama `develop`: `git push -u origin develop`
    * ¡Empieza a desarrollar tu paquete siguiendo el flujo de trabajo definido! (`feature/*`, etc.).

## Post-Creación

* Considera añadir el repositorio `luinuxscl/ai-content-dev-docs` como **submódulo Git** dentro de tu nuevo paquete para tener acceso fácil a la documentación de estándares: `git submodule add <URL_docs_repo> docs`.
* Configura el pipeline de **CI/CD (GitHub Actions)** para tu nuevo repositorio, idealmente reutilizando workflows compartidos para tests, linters, etc.