# Glosario de Términos

Este documento define términos, acrónimos y conceptos clave utilizados específicamente dentro del ecosistema de paquetes `luinuxscl/*` y la plataforma "AI Content". El objetivo es promover una comunicación clara y consistente. Este glosario debe mantenerse actualizado a medida que evolucionan la plataforma y su terminología.

---

* **Agente IA (AI Agent)**
    * Referencia: Paquete `laravel-ai-core`.
    * Definición: Entidad de software configurada para realizar tareas específicas utilizando modelos de Lenguaje Grandes (LLMs) u otras APIs de IA. Generalmente tiene un rol, personalidad e instrucciones definidas.

* **AI Context (`AI_CONTEXT.md`)**
    * Definición: Archivo Markdown ubicado en la raíz de cada paquete funcional, diseñado para proporcionar un resumen conciso de su propósito, API principal, límites y contexto a asistentes de codificación por IA (como GitHub Copilot, Cursor AI).

* **API Credential (Credencial API Externa)**
    * Referencia: Paquete `laravel-api-credentials`.
    * Definición: Clave, token o secreto utilizado por **nuestra aplicación** para autenticarse y consumir APIs de **terceros** (ej: OpenAI, OpenRouter, Google APIs). Se gestionan de forma segura a través de este paquete.

* **API Key (Jetstream/Sanctum)**
    * Referencia: Funcionalidad del Starter Kit (Jetstream/Sanctum).
    * Definición: Token generado por los **usuarios** de la plataforma AI Content para permitir que aplicaciones **externas** o scripts se autentiquen y consuman la **API interna** de la plataforma de forma segura.

* **BEM (Block, Element, Modifier)**
    * Referencia: `3_CODING_STANDARDS/CSS.md`.
    * Definición: Metodología de nomenclatura para escribir clases CSS personalizadas de forma modular y mantenible.

* **Changelog (`CHANGELOG.md`)**
    * Definición: Archivo obligatorio en cada paquete que registra los cambios relevantes (funcionalidades, correcciones, etc.) para cada versión publicada, siguiendo el formato "Keep a Changelog".

* **CI/CD (Integración Continua / Entrega Continua)**
    * Definición: Prácticas y herramientas (ej: GitHub Actions) para automatizar la integración de código, las pruebas y (potencialmente) el despliegue de paquetes o la aplicación.

* **Componente UI**
    * Referencia: Paquete `laravel-blade-components`.
    * Definición: Elemento de interfaz de usuario reutilizable (componente Blade o Livewire genérico) provisto por el paquete UI central para construir interfaces de forma consistente.

* **Contrato (Interface PHP)**
    * Referencia: `4_PACKAGE_DEVELOPMENT_GUIDE/Communication_Between_Packages.md`.
    * Definición: Mecanismo principal para la comunicación síncrona y desacoplada entre paquetes. Define la API pública que un servicio debe implementar.

* **Contexto Editorial (Editorial Context)**
    * Referencia: Paquete `laravel-editorial-context`.
    * Definición: Conjunto de guías y definiciones (Línea Editorial, Público Objetivo, Idioma) asociadas a un Workspace que dirigen cómo debe generarse el contenido.

* **Conventional Commits**
    * Referencia: `CONTRIBUTING.md`.
    * Definición: Especificación para formatear mensajes de commit de Git de manera estructurada, facilitando la automatización y la comprensión del historial.

* **DaisyUI**
    * Definición: Librería de componentes UI construida sobre Tailwind CSS, utilizada (a través de `laravel-blade-components`) para proporcionar elementos pre-estilizados.

* **Desacoplamiento (Loose Coupling)**
    * Definición: Principio de diseño que busca minimizar las dependencias directas entre diferentes partes de un sistema (en este caso, entre paquetes).

* **DTO (Data Transfer Object)**
    * Definición: Objeto simple, usualmente inmutable (`readonly`), utilizado para transferir datos estructurados entre capas o paquetes, creando un contrato de datos claro.

* **Ecosistema (de Paquetes)**
    * Definición: El conjunto completo de paquetes `luinuxscl/*` desarrollados para la plataforma AI Content, incluyendo los paquetes funcionales, de UI, core y el integrador.

* **Evento (Laravel Event)**
    * Referencia: `4_PACKAGE_DEVELOPMENT_GUIDE/Communication_Between_Packages.md`.
    * Definición: Mecanismo principal para comunicación asíncrona o desacoplada entre paquetes, basado en el sistema de Eventos/Listeners de Laravel.

* **Feature Test**
    * Referencia: `4_PACKAGE_DEVELOPMENT_GUIDE/Testing_Strategy.md`.
    * Definición: Prueba automatizada que verifica una funcionalidad completa dentro de un paquete, interactuando con sus componentes internos (servicios, modelos, BBDD de prueba) a través de su API pública o puntos de entrada. Se ejecuta con Orchestra Testbench.

* **Integration Test**
    * Referencia: `4_PACKAGE_DEVELOPMENT_GUIDE/Testing_Strategy.md`.
    * Definición: Prueba automatizada que verifica la correcta interacción *entre* dos o más paquetes `luinuxscl/*`. Se ejecuta principalmente en el contexto de la aplicación integradora (`ai-content`).

* **Jetstream**
    * Definición: Starter kit de Laravel que proporciona la base para la autenticación y otras funcionalidades en el Starter Kit Personalizado. *(Nota: La funcionalidad de Teams está deshabilitada en nuestro caso)*.

* **Listener (Laravel Listener)**
    * Definición: Clase que "escucha" y reacciona a un Evento específico despachado en el sistema.

* **Livewire**
    * Definición: Framework full-stack para Laravel que permite construir interfaces dinámicas usando principalmente PHP. Utilizado en el Starter Kit y para componentes UI y de demo.

* **Orchestra Testbench**
    * Definición: Paquete esencial para escribir pruebas (Unit/Feature) para paquetes Laravel de forma aislada, simulando una instalación de Laravel.

* **Paquete (Package)**
    * Definición: Módulo de código PHP distribuido vía Composer, con una responsabilidad única dentro del ecosistema. Sigue la estructura y estándares definidos.

* **Pipeline (Content Pipeline)**
    * Referencia: Paquete `laravel-content-pipeline`.
    * Definición: Flujo de trabajo definido por estados (ej: Semilla -> Título Generado -> Contenido Generado -> Listo para Publicar) por el que transita el contenido.

* **PR (Pull Request)**
    * Definición: Solicitud en GitHub (u otro) para fusionar los cambios de una rama (ej: `feature/x`) en otra (ej: `develop`). Es el mecanismo principal para la revisión de código.

* **Prompt (AI Prompt)**
    * Referencia: Paquete `laravel-ai-core`.
    * Definición: Texto o conjunto de instrucciones que se envían a un modelo de IA para obtener una respuesta o generar una salida.

* **PSR-4 / PSR-12**
    * Definición: Estándares de PHP-FIG para autoloading de clases (PSR-4) y estilo de código (PSR-12), respectivamente. Son obligatorios en el proyecto.

* **Release**
    * Referencia: `4_PACKAGE_DEVELOPMENT_GUIDE/Versioning_And_Releases.md`.
    * Definición: Una versión específica, estable y etiquetada (tag) de un paquete, publicada siguiendo SemVer.

* **Semilla (Content Seed)**
    * Referencia: Paquete `laravel-content-seeds`.
    * Definición: Información o idea inicial (ej: un titular de RSS, una tendencia de búsqueda, una palabra clave) que sirve como punto de partida para generar una pieza de contenido en el Pipeline.

* **SemVer (Semantic Versioning)**
    * Referencia: `4_PACKAGE_DEVELOPMENT_GUIDE/Versioning_And_Releases.md`.
    * Definición: Esquema de versionado `Mayor.Menor.Parche` (X.Y.Z) utilizado obligatoriamente para todos los paquetes.

* **Servicio (Service Class)**
    * Definición: Clase PHP que encapsula una lógica de negocio o proceso específico, diseñada para ser inyectada y reutilizada.

* **Spatie/laravel-permission**
    * Definición: Librería externa utilizada (a través del Starter Kit) para gestionar Roles y Permisos en toda la aplicación.

* **Starter Kit (Personalizado)**
    * Definición: La aplicación base de Laravel 12 preconfigurada (con Jetstream (sin Teams), Spatie/Permission, Livewire, Tailwind) sobre la cual se instalan y ejecutan todos los paquetes `luinuxscl/*`.

* **Submódulo Git (Git Submodule)**
    * Definición: Mecanismo de Git para incrustar un repositorio (como `ai-content-dev-docs`) dentro de otro (como un paquete funcional).

* **Tailwind CSS**
    * Definición: Framework CSS utility-first utilizado como base para todo el estilado.

* **Target Audience (Público Objetivo)**
    * Referencia: Paquete `laravel-editorial-context`.
    * Definición: Descripción del lector ideal para el contenido (demografía, intereses, etc.), parte del Contexto Editorial.

* **Unit Test**
    * Referencia: `4_PACKAGE_DEVELOPMENT_GUIDE/Testing_Strategy.md`.
    * Definición: Prueba automatizada que verifica una unidad de código (clase/método) de forma completamente aislada, usando mocks/stubs.

* **Utility-First**
    * Definición: Enfoque de CSS (propio de Tailwind) donde los estilos se construyen aplicando directamente clases de utilidad en el HTML/Blade.

* **Workspace (Espacio de Trabajo)**
    * Referencia: Paquete `laravel-workspaces`.
    * Definición: La entidad organizacional principal en la plataforma. Actúa como un contenedor aislado para usuarios, contenido, configuraciones, etc. (similar a un tenant).