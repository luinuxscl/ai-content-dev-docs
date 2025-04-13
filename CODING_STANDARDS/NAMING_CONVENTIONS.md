# 3.4 Convenciones de Nombres

Este documento centraliza las convenciones de nombres utilizadas en todo el ecosistema de paquetes `luinuxscl/*` y la aplicación principal `luinuxscl/ai-content`. El objetivo es asegurar la consistencia, legibilidad y predictibilidad del código y la estructura del proyecto. Para detalles específicos del lenguaje, consultar los archivos correspondientes (`PHP.md`, `JAVASCRIPT.md`, `CSS.md`).

## 1. PHP

* **Clases, Interfaces, Traits, Enums:** `StudlyCaps` (ej: `WorkspaceService`, `ContentPublisherInterface`, `HasApiCredentials`, `PostStatus`).
* **Métodos:** `camelCase` (ej: `createWorkspace`, `getLatestPosts`).
* **Propiedades (Miembros de Clase):** `camelCase` (ej: `protected $userRepository`).
* **Variables:** `camelCase` (ej: `$activeWorkspace`, `$postCount`).
* **Constantes de Clase:** `UPPER_SNAKE_CASE` (ej: `const STATUS_PUBLISHED = 'published';`).
* *(Referencia: `PHP.md`)*

## 2. JavaScript

* **Variables y Funciones:** `camelCase` (ej: `userName`, `calculateTotal`).
* **Clases (si se usan):** `PascalCase` (ej: `ModalComponent`).
* **Constantes:** `UPPER_SNAKE_CASE` (ej: `const MAX_RETRIES = 5;`).
* *(Referencia: `JAVASCRIPT.md`)*

## 3. CSS (Clases Personalizadas)

* **Metodología:** BEM (Block, Element, Modifier) - `bloque__elemento--modificador`.
* **Prefijos Sugeridos:** `c-` (componente), `u-` (utilidad), `is-`/`has-` (estado).
* *(Referencia: `CSS.md`)*

## 4. Base de Datos

* **Tablas:** `snake_case`, plural (ej: `workspaces`, `api_credentials`, `editorial_contexts`).
* **Columnas:** `snake_case` (ej: `name`, `api_key`, `published_at`, `workspace_id`).
* **Clave Primaria:** `id` (autoincremental bigint por defecto).
* **Claves Foráneas:** Nombre del modelo relacionado en singular + `_id` (ej: `user_id`, `workspace_id`, `post_id`).
* **Tablas Pivot (Muchos a Muchos):** Nombres de los modelos relacionados en singular, unidos por `_` y ordenados alfabéticamente (ej: `post_tag`, `user_workspace`). Incluir solo las claves foráneas (`id` no es necesaria usualmente) y campos adicionales si se requieren.
* **Índices:** Nombre explícito incluyendo tabla y columna(s) (ej: `workspaces_name_index`, `posts_status_published_at_index`).

## 5. Archivos de Configuración (`config/`)

* **Nombre de Archivo:** `snake_case.php` (ej: `workspaces.php`, `publishing.php`).
* **Claves (Keys) dentro del array:** `snake_case` (ej: `'user_model'`, `'default_provider'`).

## 6. Rutas (Web y API)

* **URIs:** `kebab-case`, preferiblemente en plural para recursos RESTful (ej: `/api/v1/workspaces/{workspace}/content-pipelines`, `/app/settings/api-credentials`).
* **Nombres de Ruta (`->name(...)`):** Usar notación `dot.notation` agrupada por recurso/sección para claridad y evitar colisiones. Ser consistente. (ej: `app.workspaces.index`, `app.workspaces.store`, `api.v1.posts.show`).

## 7. Eventos y Listeners

* **Clases de Eventos:** `PascalCase`, describiendo un evento ocurrido (usualmente en tiempo pasado) (ej: `WorkspaceCreated`, `PostPublished`, `ApiKeyRevoked`).
* **Clases de Listeners:** `PascalCase`, describiendo la acción que realizan en respuesta al evento (ej: `SendWelcomeEmailListener`, `ClearPostCacheListener`, `LogApiKeyRevocation`). Si un listener maneja múltiples eventos (Suscriptor), nombrarlo acorde a su función general (ej: `WorkspaceEventSubscriber`).

## 8. Jobs (Colas)

* **Clases de Jobs:** `PascalCase`, describiendo la tarea específica que se ejecutará en segundo plano (ej: `GenerateMonthlyReport`, `ProcessUploadedImage`, `PublishScheduledPosts`).

## 9. Vistas y Componentes Blade

* **Archivos de Vistas:** `kebab-case.blade.php` o `snake_case.blade.php` (elegir uno y ser consistente, `kebab-case` es común). Organizar en subdirectorios por recurso/sección (ej: `resources/views/workspaces/index.blade.php`).
* **Archivos de Componentes Blade:** `kebab-case.blade.php` dentro de `resources/views/components/` (posiblemente con subdirectorios). (ej: `components/forms/input.blade.php`).
* **Etiquetas de Componentes Blade:** `<x-nombre-componente>` o `<x-directorio.nombre-componente>` (ej: `<x-button>`, `<x-forms.input>`).

## 10. Clases y Métodos de Test

* **Archivos de Test:** Sufijo `Test` (`PascalCase`) (ej: `WorkspaceServiceTest.php`, `PublishPostFeatureTest.php`). Ubicados en `tests/Unit` o `tests/Feature`.
* **Métodos de Test:** `camelCase` descriptivo, a menudo usando `_` para separar las partes lógicas del nombre del test, o usando la anotación `@test` con una descripción en lenguaje natural. (ej: `public function test_it_creates_a_workspace_successfully()` o `public function an_admin_can_create_workspaces()`). Ser consistente con el estilo elegido (PHPUnit vs Pest).

## 11. Ramas Git

* Ramas principales: `main`, `develop`.
* Ramas de soporte: `release/vX.Y.Z`.
* Ramas de desarrollo:
    * `feature/<descripcion-corta>` o `feature/ISSUE-<numero>` (ej: `feature/user-invites`, `feature/PROJ-123`).
    * `bugfix/<descripcion-corta>` o `bugfix/ISSUE-<numero>` (ej: `bugfix/fix-pagination`, `bugfix/PROJ-456`).

## 12. Variables de Entorno (`.env`)

* **Formato:** `UPPER_SNAKE_CASE` (ej: `DB_DATABASE`, `REDIS_HOST`).
* **Prefijos:** Usar prefijos para servicios externos o configuraciones específicas de paquetes para evitar colisiones (ej: `OPENAI_API_KEY`, `ALGOLIA_APP_ID`, `WORKSPACE_DEFAULT_LIMIT=50`).