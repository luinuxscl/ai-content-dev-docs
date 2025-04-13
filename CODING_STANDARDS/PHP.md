# 3.1 Estándares de Código PHP

Este documento establece los estándares y convenciones para escribir código PHP consistente, legible y mantenible en todos los paquetes del ecosistema `luinuxscl/*` para la plataforma AI Content. La adherencia a estos estándares es obligatoria y se verificará parcialmente mediante herramientas automáticas en el pipeline de CI.

## 1. Estándar Base: PSR-12

* Todo el código PHP **DEBE** adherirse al estándar **PSR-12 (Extended Coding Style guide)**.
* Referencia oficial: [PSR-12](https://www.php-fig.org/psr/psr-12/)
* Se utilizará **Laravel Pint** (configurado para PSR-12) o **PHP CS Fixer** para formatear y verificar automáticamente el cumplimiento de este estándar.

## 2. Tipado Estricto (Strict Typing)

* La declaración `declare(strict_types=1);` **DEBE** estar presente al inicio de **todos** los archivos PHP.
* Se **DEBEN** usar **type hints** para **todos** los parámetros de métodos y funciones.
* Se **DEBEN** usar **return type hints** para **todos** los métodos y funciones.
    * Usar `void` si un método no retorna nada.
    * Usar `mixed` solo cuando sea estrictamente necesario y justificado; preferir tipos más específicos o Union Types.
    * Usar `never` (PHP 8.1+) para métodos que siempre lanzan una excepción o terminan la ejecución.
* Utilizar los tipos específicos disponibles: `int`, `string`, `bool`, `float`, `array`, `object`, `iterable`, `callable`.
* Utilizar `self` y `static` apropiadamente para referencias dentro de la clase.
* Aprovechar las características de PHP 8+:
    * **Union Types:** `TypeA|TypeB` (ej: `int|string`).
    * **Intersection Types:** `TypeA&TypeB` (ej: `Countable&Iterator`).
    * **`readonly` properties:** (Ver sección 6).
    * **Enums:** (Ver sección 6).

## 3. DocBlocks

* Se **DEBEN** incluir DocBlocks para:
    * Clases, Interfaces, Traits, Enums.
    * Propiedades públicas y protegidas.
    * Métodos/Funciones públicas y protegidas.
    * Constantes de clase (opcional si el nombre es autoexplicativo, pero recomendado).
* **Formato:** Utilizar las etiquetas estándar `@param`, `@return`, `@throws`, `@var`, etc.
    ```php
    /**
     * Describe el propósito de la clase/método/propiedad.
     *
     * @param string $paramName Descripción del parámetro.
     * @param bool|null $optionalParam Descripción (puede ser nulo).
     * @return int Descripción del valor de retorno.
     * @throws \App\Exceptions\SpecificException Descripción de cuándo se lanza.
     */
    ```
* **Descripciones Claras:** El DocBlock debe explicar el *qué* y el *por qué* del elemento documentado, incluso si los type hints ya especifican el *cómo*. Son esenciales para entender la intención.
* **Tipos Complejos:** Usar DocBlocks para clarificar tipos genéricos (ej: `@param array<int, \App\DTO\UserData> $users`).

## 4. Convenciones de Nombres

* **Clases:** `StudlyCaps` (ej: `WorkspaceService`, `ProcessContentJob`)
* **Traits:** `StudlyCaps` (ej: `HandlesApiCredentials`, `Observable`)
* **Interfaces:** `StudlyCaps` (ej: `ContentRepositoryInterface`, `Runnable`)
* **Enums:** `StudlyCaps` (ej: `PostStatus`, `ProviderType`)
* **Métodos:** `camelCase` (ej: `createWorkspace`, `getLatestPosts`)
* **Variables:** `camelCase` (ej: `$activeWorkspace`, `$userList`)
* **Propiedades (Miembros de Clase):** `camelCase` (ej: `protected $workspaceRepository`)
* **Constantes de Clase:** `UPPER_SNAKE_CASE` (ej: `const DEFAULT_TIMEOUT = 30;`, `const STATUS_PUBLISHED = 'published';`)
* **Keys de Arrays (Configuración, etc.):** `snake_case` (siguiendo la convención de Laravel).
* **Nombres Descriptivos:** Elegir nombres que comuniquen claramente la intención y eviten abreviaturas ambiguas.

## 5. Buenas Prácticas Específicas (Laravel)

* **Inyección de Dependencias vs. Facades:** **Preferir siempre la Inyección de Dependencias** (principalmente vía constructor) para obtener instancias de otras clases dentro de Servicios, Listeners, Jobs, etc. El uso de Facades es aceptable y idiomático en:
    * Archivos de Rutas (`routes/*.php`)
    * Controladores (con moderación, para brevedad)
    * Comandos de Consola
    * Vistas Blade
    * Service Providers
    * Archivos de Configuración
* **Controladores:** Deben ser **delgados (thin)**. Su responsabilidad es recibir la request, delegar la lógica de negocio a otras clases (Servicios, Acciones, Jobs), y retornar una respuesta. Utilizar **Form Requests** para encapsular la validación y autorización de la request. Considerar **Single Action Controllers** (con método `__invoke`) para acciones simples.
* **Modelos (Eloquent):** Enfocados en la representación de datos y relaciones. Evitar lógica de negocio compleja. Utilizar `protected $fillable` o `$guarded` explícitamente. Usar Scopes para consultas reutilizables, Factories para testing.
* **Servicios:** Clases dedicadas para la lógica de negocio, orquestación de tareas o interacción con APIs externas. Deben ser inyectables y testeables.
* **Vistas (Blade):** Mínima lógica PHP. Usar componentes Blade y pasar datos pre-procesados desde el controlador o View Composer/Componente.
* **Configuración:** Acceder a valores de configuración usando `config('nombre_archivo.key')`. **Nunca** usar `env('VARIABLE')` directamente fuera de los archivos de `config/`.
* **Eventos/Listeners/Jobs:** Utilizar para desacoplar acciones o ejecutar tareas en segundo plano (colas).

## 6. Otras Convenciones y Características Modernas

* **`final`:** Marcar las clases como `final` si no están diseñadas o no se espera que sean extendidas. Ayuda a la mantenibilidad y al análisis estático.
* **`readonly` Properties (PHP 8.1+):** Usar para propiedades inmutables (asignadas en el constructor y que no cambian), especialmente útil en DTOs (Data Transfer Objects) o Value Objects.
* **`use` Statements:** Agrupar por tipo (clases/interfaces/traits, funciones, constantes) y ordenar alfabéticamente dentro de cada grupo al principio del archivo.
* **Enums (PHP 8.1+):** Utilizar Enums nativos (preferiblemente Backed Enums) para representar conjuntos fijos de valores (ej: estados, tipos de proveedor) en lugar de constantes de clase, ya que proporcionan más seguridad de tipos y claridad.

## 7. Verificación Automática (Enforcement)

* El cumplimiento de PSR-12 y otros aspectos de formato se verificará usando **Laravel Pint** (o PHP CS Fixer) en el pipeline de CI.
* La corrección de tipos y otros posibles errores se verificará usando **PHPStan** (o Psalm) en el pipeline de CI.
* **Es responsabilidad del desarrollador** asegurar que el código cumple con estos estándares *antes* de crear un Pull Request. Las herramientas de CI son una red de seguridad, no un sustituto de la escritura de código limpio.