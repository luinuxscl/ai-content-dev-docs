# 4.5 Estrategia de Pruebas (Testing)

Este documento detalla la estrategia y las expectativas para las pruebas automatizadas en todos los paquetes `luinuxscl/*`. Un conjunto robusto de pruebas es fundamental para garantizar la calidad del código, prevenir regresiones, facilitar el refactoring y asegurar la correcta integración dentro del ecosistema.

## 1. Filosofía de Testing

* **Pruebas como Requisito:** Escribir pruebas automatizadas no es opcional, es una parte integral del desarrollo. Todo nuevo feature o bugfix debe ir acompañado de pruebas relevantes.
* **Pirámide de Pruebas (Adaptada):** Nos basamos en la pirámide de pruebas:
    * **Base Amplia (Unit Tests):** La mayoría de las pruebas deben ser unitarias, rápidas y aisladas.
    * **Capa Media (Feature Tests):** Pruebas que verifican la funcionalidad del paquete interactuando con sus componentes internos y BBDD (de prueba).
    * **Punta Estrecha (Integration Tests):** Pruebas selectivas que verifican la interacción *entre* paquetes, realizadas principalmente en la aplicación integradora.
* **Cobertura de Código:** Se establece un objetivo mínimo de **85% de cobertura de código** (medido por líneas ejecutadas) para cada paquete. Sin embargo, la calidad y relevancia de las pruebas es más importante que el número en sí. La cobertura debe ser significativa.
* **Pruebas en CI:** Todas las suites de pruebas (Unit y Feature) **DEBEN** ejecutarse y pasar en el pipeline de Integración Continua (CI) para cada Pull Request antes de poder ser mergeado a `develop`.

## 2. Tipos de Pruebas Requeridas por Paquete

Cada paquete funcional `luinuxscl/*` debe incluir, como mínimo:

* **Pruebas Unitarias (Unit Tests):**
    * **Ubicación:** `tests/Unit/`
    * **Propósito:** Verificar unidades de código aisladas (clases individuales, métodos específicos) sin interactuar con dependencias externas como bases de datos, sistema de archivos, APIs externas u otros paquetes `luinuxscl/*`.
    * **Enfoque:** Probar lógica de negocio pura, algoritmos, cálculos, validaciones simples, casos borde, manejo de excepciones internas.
    * **Aislamiento:** Utilizar **Mocks y Stubs** (ej: con Mockery o las herramientas de PHPUnit/Pest) para simular dependencias externas o colaboradoras.
    * **Cobertura:** Deben formar la base de la cobertura del código (>85%). Son rápidas de ejecutar.

* **Pruebas de Funcionalidad (Feature Tests):**
    * **Ubicación:** `tests/Feature/`
    * **Propósito:** Probar las funcionalidades principales del paquete desde una perspectiva de "caja negra" o "caja gris", interactuando con varios componentes *dentro* del mismo paquete. Simulan cómo se usaría el paquete desde el exterior (ej: desde un controlador, comando o servicio).
    * **Enfoque:** Probar flujos completos dentro del paquete (ej: crear y configurar un Workspace a través del `WorkspaceService`), interacciones entre Servicios y Modelos del paquete, validación de Form Requests (si aplica), eventos despachados por el paquete, rutas de API o demo (si el paquete las define).
    * **Entorno:** Se ejecutan dentro de un entorno Laravel simulado (usando **Orchestra Testbench**). Pueden interactuar con una base de datos de prueba (preferiblemente SQLite en memoria) utilizando las migraciones y factories del paquete.
    * **Aislamiento (Inter-Paquete):** Aunque interactúan con la BBDD, estas pruebas **DEBEN** seguir aisladas de la lógica de *otros paquetes `luinuxscl/*`*. Si un servicio del paquete A necesita interactuar con una interfaz provista por el paquete B, esa interfaz debe ser **mockeada** en el test de A.

## 3. Pruebas de Integración (Integration Tests)

* **Propósito:** Verificar explícitamente que **múltiples paquetes `luinuxscl/*` interactúan correctamente** entre sí en flujos de trabajo realistas, respetando los contratos (Interfaces y Eventos).
* **Ubicación Principal:** Estas pruebas residen y se ejecutan fundamentalmente en el repositorio de la **aplicación integradora (`luinuxscl/ai-content`)**. Esto permite probar los flujos completos tal como los experimentará el usuario final, usando las configuraciones y bindings reales de la aplicación.
* **Enfoque:** Probar escenarios que cruzan los límites de los paquetes. Ejemplos:
    * Crear un `Workspace` (`laravel-workspaces`) y verificar que un Listener en `laravel-some-other-package` reacciona correctamente al evento `WorkspaceCreated`.
    * Despachar un evento `PostReadyForPublishing` desde `laravel-content-pipeline` y verificar que el listener en `laravel-wordpress-publisher` intenta la publicación.
    * Llamar a un servicio en `ai-content` que utiliza interfaces de `laravel-ai-core` y `laravel-media-library` para generar un post completo.
* **Entorno:** Se ejecutan en un entorno Laravel completo configurado como la aplicación `ai-content`, utilizando una base de datos de prueba dedicada.

## 4. Pruebas de UI y Componentes

* **Paquete UI (`laravel-blade-components`):** Este paquete debe tener su propia suite de tests (probablemente Feature tests usando los helpers de test de Livewire) para verificar el renderizado y comportamiento de sus componentes reutilizables.
* **Componentes Demo/Test (en Paquetes Funcionales):** Los componentes Livewire incluidos en `src/Http/Livewire/Demo/` dentro de cada paquete funcional deben tener, como mínimo, Feature Tests básicos que aseguren que renderizan correctamente y que sus acciones principales (que llaman a los servicios del paquete) funcionan como se espera en el entorno de Testbench.

## 5. Herramientas y Entorno

* **Framework de Testing:** Se utilizará **Pest** o **PHPUnit** de forma consistente en todo el ecosistema (a definir).
* **Entorno de Paquetes:** **Orchestra Testbench** es obligatorio y debe configurarse en `tests/TestCase.php` de cada paquete para poder probarlo aisladamente simulando una aplicación Laravel.
* **Base de Datos de Prueba:** Utilizar **SQLite en memoria (`:memory:`)** por defecto para tests Unitarios y de Funcionalidad para mayor velocidad. Configurar una base de datos dedicada (ej: MySQL/PostgreSQL de prueba) para las Pruebas de Integración en `ai-content`.
* **Datos de Prueba:** Utilizar **Model Factories** para generar datos consistentes y reutilizables.
* **Mocks/Stubs:** Utilizar **Mockery** o las funcionalidades integradas de Pest/PHPUnit para crear dobles de prueba (mocks, stubs, spies).

## 6. Ejecución y CI

* **Ejecución Local:** Cada paquete debe incluir scripts en `composer.json` para facilitar la ejecución local de las pruebas (ej: `composer test`, `composer test:unit`, `composer test:feature`, `composer coverage`).
* **Integración Continua (CI):** Se configurarán **GitHub Actions** (u otra herramienta CI) para ejecutar automáticamente *todas* las pruebas Unitarias y de Funcionalidad, junto con los linters y análisis estático, en cada push a `develop` y en cada Pull Request. El reporte de cobertura debe generarse y verificarse contra el umbral mínimo (85%).