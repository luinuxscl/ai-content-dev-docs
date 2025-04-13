# 1. Arquitectura de la Plataforma AI Content

## 1.1. Visión General

La plataforma "AI Content" es un sistema modular diseñado para la generación, gestión y publicación de contenido asistido por Inteligencia Artificial. Se construye sobre Laravel 12 siguiendo una **arquitectura basada en paquetes independientes pero interconectados**, bajo el namespace `luinuxscl`.

El objetivo principal de esta arquitectura es facilitar:

* **Desarrollo Independiente:** Equipos pueden trabajar en paralelo en diferentes paquetes.
* **Reutilización:** Los paquetes genéricos (`laravel-workspaces`, `laravel-api-credentials`, etc.) están diseñados para ser potencialmente reutilizables en otros proyectos.
* **Mantenimiento Simplificado:** Las actualizaciones o correcciones se pueden aislar en paquetes específicos.
* **Escalabilidad:** Nuevas funcionalidades (proveedores de IA, plataformas de publicación, fuentes de semillas) pueden añadirse como nuevos paquetes.

La plataforma se instala sobre un **Starter Kit Personalizado** preexistente que proporciona la base de Laravel 12, autenticación (Jetstream sin Teams), gestión de usuarios, sistema de roles/permisos (Spatie/laravel-permission), y stack UI (Livewire 3 / Tailwind 4). Los paquetes `luinuxscl/*` aprovechan esta base sin duplicar funcionalidades.

## 1.2. Principios de Diseño Clave

El desarrollo de todos los paquetes se rige por los siguientes principios:

* **Modularidad:** Funcionalidades encapsuladas en paquetes cohesivos.
* **Responsabilidad Única (por Paquete):** Cada paquete se enfoca en un dominio o tarea específica y bien definida.
* **Alta Cohesión, Bajo Acoplamiento:** Maximizar la relación interna de los componentes de un paquete y minimizar las dependencias entre paquetes.
* **Comunicación Explícita:** Preferencia por Interfaces/Contratos y Eventos sobre llamadas directas entre clases de diferentes paquetes.
* **Configuración sobre Código:** Permitir la adaptación de los paquetes a través de archivos de configuración publicados.
* **Reutilización (cuando aplique):** Diseñar paquetes base de forma genérica.
* **Adherencia a Estándares:** Seguir los estándares de Laravel y los definidos en esta guía de desarrollo (`luinuxscl/ai-content-dev-docs`).

## 1.3. Estructura de Capas y Paquetes

La arquitectura se puede visualizar en capas lógicas:

* **Capa Base:** Proporciona los fundamentos organizacionales, de contexto y de UI.
    * `luinuxscl/laravel-workspaces`: Gestión de la entidad Workspace.
    * `luinuxscl/laravel-editorial-context`: Gestión de guías de contenido (línea editorial, público).
    * `luinuxscl/laravel-api-credentials`: Almacenamiento seguro de claves API externas.
    * `luinuxscl/laravel-wordpress-integration`: Interacciones básicas con WordPress (credenciales).
    * `luinuxscl/laravel-blade-components`: Biblioteca central de componentes UI reutilizables (Livewire/Blade, CSS, JS).
* **Capa Funcional:** Implementa la lógica de negocio principal de la plataforma.
    * *Pipeline:* `laravel-content-pipeline`, `laravel-content-seeds`, `laravel-trends-source`, `laravel-rss-source`
    * *IA:* `laravel-ai-core`, `laravel-ai-usage-tracking`, `openrouter-laravel-client`, (otros clientes IA), `laravel-ai-image-generation`, `openai-image-laravel`, `stability-image-laravel`
    * *Medios:* `laravel-media-library`
    * *Publicación:* `laravel-publishing-core`, `laravel-wordpress-publisher`, (otros publicadores)
* **Capa de Integración:** Orquesta y presenta la aplicación final.
    * `luinuxscl/ai-content`: Une todos los paquetes, añade configuración específica, rutas principales, dashboards y la lógica de aplicación de la "AI Content Platform".

## 1.4. Diagrama de Arquitectura

*Se debe crear y añadir un diagrama que visualice las capas, los paquetes principales y sus interrelaciones clave.*

_(Nota: Crear el archivo `architecture_overview.png` en la carpeta `diagrams/`)_

## 1.5. Flujo de Datos Principal (Conceptual)

1.  **Semillas:** Se adquieren ideas o datos base de diversas fuentes (`laravel-trends-source`, `laravel-rss-source`, etc.) gestionadas por `laravel-content-seeds`.
2.  **Pipeline:** Las semillas entran en `laravel-content-pipeline`, donde se transforman en `Posts` y avanzan por diferentes estados.
3.  **Contextualización:** El proceso se guía por las definiciones de `laravel-editorial-context` asociadas al `Workspace` activo.
4.  **Generación IA:** Se utilizan los servicios de `laravel-ai-core` (configurados con credenciales de `laravel-api-credentials` y usando clientes específicos como `openrouter-laravel-client`) para generar títulos, contenido, etc., basados en el contexto editorial. Se registra el uso en `laravel-ai-usage-tracking`.
5.  **Generación Medios:** Se invoca `laravel-ai-image-generation` (usando clientes como `openai-image-laravel`) para crear imágenes. Las imágenes se gestionan a través de `laravel-media-library`.
6.  **Publicación:** Una vez listo, el contenido se envía a través de `laravel-publishing-core` usando implementaciones específicas como `laravel-wordpress-publisher` (configurado con `laravel-wordpress-integration`).

## 1.6. Comunicación entre Paquetes

Se priorizan los siguientes mecanismos:

1.  **Interfaces/Contratos:** Para dependencias directas donde un paquete necesita invocar lógica de otro. El paquete dependiente type-hinta una interfaz; la aplicación (`ai-content` o un ServiceProvider) se encarga de enlazar la interfaz a la implementación concreta (a menudo configurable).
2.  **Eventos y Listeners:** Para comunicación desacoplada o asíncrona. Los paquetes disparan eventos significativos (ej: `WorkspaceCreated`, `PostPublished`), y otros paquetes (o la aplicación principal) registran listeners para reaccionar a ellos. Los payloads de los eventos deben ser consistentes.

*Se desaconseja el uso de Facades para la comunicación directa entre paquetes `luinuxscl/*` para mantener bajo acoplamiento.*

## 1.7. Integración con Starter Kit Personalizado

* La base es un starter kit con Laravel 12, Jetstream (Auth, Profile, API Keys - **Teams deshabilitado**), Livewire 3, Tailwind 4.
* **Usuarios:** Los paquetes utilizan el modelo `User` configurado, provisto por el starter kit.
* **Roles/Permisos:** Se utiliza **exclusivamente** el sistema `spatie/laravel-permission` configurado en el starter kit. Los paquetes `luinuxscl/*` definen los nombres de los permisos que requieren y los verifican usando `Gate`, `Policies` o directivas `@can`. La asignación de permisos a roles y roles a usuarios es responsabilidad de la aplicación/starter kit.

## 1.8. Consideraciones Adicionales

* **Base de Datos:** Cada paquete define y gestiona sus propias migraciones. Las relaciones entre datos de diferentes paquetes se establecen comúnmente mediante claves foráneas a entidades clave como `workspaces.id` o `users.id`.
* **Seguridad:** Se basa en la autenticación de Jetstream/Sanctum, la autorización de Spatie, la validación estricta de entradas, y el almacenamiento seguro de credenciales externas (`laravel-api-credentials`).
* **Testing:** Consultar la estrategia detallada en `PACKAGE_DEVELOPMENT_GUIDE/Testing_Strategy.md`.