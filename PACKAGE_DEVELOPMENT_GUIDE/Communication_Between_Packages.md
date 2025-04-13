# 4.4 Comunicación Entre Paquetes

Este documento describe los patrones y métodos preferidos para la comunicación e interacción entre los diferentes paquetes `luinuxscl/*` del ecosistema. El objetivo principal es mantener un **bajo acoplamiento** y una **alta cohesión**, permitiendo que los paquetes evolucionen de forma independiente y sean más fáciles de probar y mantener.

## Principio Fundamental: Evitar Acoplamiento Directo

La regla de oro es evitar que un paquete `A` dependa directamente de la implementación interna (clases concretas, detalles de base de datos) de otro paquete `B`. La interacción debe realizarse a través de APIs públicas bien definidas.

## 1. Método Preferido #1: Interfaces / Contratos

* **Cuándo Usar:** Ideal cuando un paquete `A` necesita invocar **directamente** una funcionalidad específica de otro paquete `B` de forma **síncrona** y espera una respuesta o resultado directo. Es una dependencia explícita pero controlada.
* **Cómo Funciona:**
    1.  El paquete proveedor (`B`) define una **interfaz PHP** en su directorio `src/Contracts/` que declara los métodos públicos que ofrece (ej: `WorkspaceFinderInterface`).
    2.  El paquete proveedor (`B`) crea una o más **clases de implementación** concretas para esa interfaz (ej: `DatabaseWorkspaceFinder implements WorkspaceFinderInterface`).
    3.  El paquete consumidor (`A`) **type-hinta la interfaz** (`WorkspaceFinderInterface`) en sus constructores o métodos donde necesita la funcionalidad. **Nunca** debe type-hintar la clase concreta (`DatabaseWorkspaceFinder`).
    4.  El **enlace (binding)** entre la interfaz y la implementación concreta se realiza en el **Service Provider** del paquete proveedor (`B`) o, en algunos casos, en la aplicación principal (`ai-content`), usando `$this->app->singleton(Interface::class, Implementation::class);`.
* **Ventajas:**
    * **Desacoplamiento:** `A` depende de un contrato estable, no de detalles de implementación de `B`.
    * **Testabilidad:** Fácil de mockear la interfaz en las pruebas unitarias/feature de `A`.
    * **Claridad:** La interfaz define claramente la API pública ofrecida por `B`.
* **Ejemplo:** `laravel-content-pipeline` necesita obtener datos del workspace actual. Type-hintaría una `LuinuxScl\Workspaces\Contracts\WorkspaceRepositoryInterface` y `laravel-workspaces` proveería la implementación y el binding.

## 2. Método Preferido #2: Eventos / Listeners

* **Cuándo Usar:** Ideal para comunicación **desacoplada** o **asíncrona**.
    * Cuando un paquete `A` realiza una acción significativa y necesita **notificar** a otras partes del sistema (sin saber quiénes son) para que reaccionen.
    * Para ejecutar **acciones secundarias** que no son críticas para el flujo principal (ej: enviar notificaciones, limpiar caché, registrar logs).
    * Para ejecutar tareas que pueden/deben ir a una **cola (queue)**.
    * Como mecanismo para **romper dependencias circulares** potenciales.
* **Cómo Funciona:**
    1.  El paquete emisor (`A`) define una **clase de Evento** (ej: `Events/WorkspaceCreated.php`) que contiene los datos relevantes (payload) del suceso.
    2.  Cuando la acción ocurre, `A` despacha el evento: `event(new WorkspaceCreated($workspace, $user))`.
    3.  Otros paquetes (`B`, `C`) o la aplicación principal (`ai-content`) crean **Listeners** (ej: `Listeners/SetupDefaultWorkspaceSettings.php`) que escuchan ese evento específico.
    4.  Los Listeners se registran en el `EventServiceProvider` (ya sea el de la aplicación o uno propio del paquete si el listener es interno).
    5.  Los Listeners pueden implementar `ShouldQueue` para ejecución asíncrona.
* **Ventajas:**
    * **Muy Bajo Acoplamiento:** El emisor no conoce a los receptores.
    * **Flexibilidad:** Fácil añadir o quitar listeners sin modificar el emisor.
    * **Asincronía:** Permite diferir tareas a colas fácilmente.
* **Payloads de Eventos:**
    * Pasar datos necesarios a través de **propiedades públicas `readonly`** en la clase del Evento.
    * Considerar el uso de **DTOs (Data Transfer Objects)** como payload si los datos son complejos, para un contrato más explícito y desacoplado de los modelos Eloquent. Evitar pasar modelos Eloquent completos si solo se necesitan unos pocos IDs o atributos.
* **Nombres de Eventos:** Usar tiempo pasado, ser claros y descriptivos (ej: `PostPublished`, `CredentialsVerified`).

## 3. Compartir Datos: DTOs y Value Objects

* Al pasar datos entre paquetes (en payloads de eventos, como parámetros o retornos de métodos de interfaz), **preferir el uso de DTOs o Value Objects** simples e inmutables (con propiedades `readonly`) en lugar de depender directamente de la estructura interna de Modelos Eloquent de otro paquete o de pasar arrays asociativos complejos.
* Esto crea contratos de datos más claros y reduce el acoplamiento entre la estructura interna de un paquete y sus consumidores.

## 4. ¿Qué Patrones Evitar?

* **❌ Dependencia de Clases Concretas:** `new OtroPaquete\Services\SuServicio()` o type-hinting `OtroPaquete\Services\SuServicio` en un constructor/método. **Usar interfaces.**
* **❌ Uso de Facades Definidas por Otros Paquetes `luinuxscl/*`:** Que el paquete `A` llame a `OtroPaqueteFacade::metodo()`. **Usar inyección de interfaces.** (El uso de Facades *de Laravel* como `Log::`, `Cache::`, `Storage::` sí está permitido).
* **❌ Acceso Directo a Base de Datos Externa:** Que el paquete `A` haga `DB::table('tabla_del_paquete_b')->...`. **Usar la API (Interfaces/Eventos) que `B` expone.**
* **❌ Modificación de Estado Interno Ajeno:** Que el paquete `A` obtenga un objeto de `B` y modifique sus propiedades protegidas o llame a métodos no públicos. Interactuar solo a través de la API pública definida.

## 5. Documentación de la API del Paquete

* Cada paquete **DEBE** documentar claramente su API pública:
    * Interfaces (`Contracts/`) que ofrece para ser consumidas.
    * Eventos (`Events/`) que despacha y el payload que contienen.
    * DTOs públicos utilizados para la comunicación.
* Esta información debe estar en el `README.md` del paquete o en una sección específica de su documentación.
* El archivo `AI_CONTEXT.md` debe resumir estos puntos clave de interacción.