# 3.2 Estándares de Código JavaScript

Este documento establece los estándares y convenciones para escribir código JavaScript consistente, legible y mantenible dentro del ecosistema `luinuxscl/*`. Dado que el stack principal utiliza Livewire 3 y Alpine.js (a través del starter kit), muchas interacciones se manejarán con esas herramientas, pero estas guías aplican al JavaScript personalizado que se necesite, especialmente dentro del paquete `laravel-blade-components` o para interacciones específicas con Livewire/Alpine.

## 1. Linter y Formateador

* **ESLint:** El uso de ESLint es **obligatorio** para analizar el código JavaScript en busca de errores y problemas de estilo.
    * **Configuración:** Se utilizará una configuración estándar (ej: `eslint:recommended` extendido con reglas específicas del proyecto o un preset como `eslint-config-standard`). La configuración específica se definirá centralizadamente.
    * **Ejecución:** Debe poder ejecutarse mediante un script NPM (ej: `npm run lint`) y se ejecutará automáticamente en el pipeline de CI.
* **Prettier (Recomendado):** Se recomienda usar Prettier junto con ESLint para un formateo de código automático y consistente. La configuración de Prettier debe ser compatible con la de ESLint. Un script NPM (ej: `npm run format`) debería estar disponible.

## 2. Estilo General

* **Punto y Coma (Semicolons):** **Obligatorio** usar punto y coma al final de las declaraciones.
* **Comillas (Quotes):** Preferir **comillas simples (`'`)** para strings, a menos que se necesite interpolación o el contenido del string contenga comillas simples, en cuyo caso se usarán template literals (backticks `` ` ``) o comillas dobles (`"`). Ser consistente.
* **Indentación:** Usar **2 espacios** para la indentación.
* **Nomenclatura:**
    * Variables y Funciones: `camelCase` (ej: `userName`, `calculateTotal`).
    * Clases y Constructores (si se usan): `PascalCase` (ej: `UserService`, `ModalComponent`).
    * Constantes: `UPPER_SNAKE_CASE` (ej: `const MAX_USERS = 100;`).

## 3. Características Modernas (ES6+)

* **Variables:** Preferir `const` por defecto. Usar `let` solo si la variable necesita ser reasignada. **Evitar `var`**.
* **Funciones Flecha (Arrow Functions):** Usar `=>` para funciones anónimas y callbacks donde `this` no sea una preocupación o se desee heredar el contexto léxico.
* **Template Literals:** Usar backticks (`` ` ``) para interpolación de strings y strings multilínea.
    ```javascript
    // Bueno
    const message = `Hola, ${userName}!`;
    // Malo
    const message = 'Hola, ' + userName + '!';
    ```
* **Desestructuración (Destructuring):** Utilizar para extraer valores de arrays y objetos de forma más limpia.
    ```javascript
    // Bueno
    const { userId, isActive } = userConfig;
    const [firstItem] = items;
    // Menos bueno
    const userId = userConfig.userId;
    const isActive = userConfig.isActive;
    const firstItem = items[0];
    ```
* **Operadores Spread/Rest (`...`):** Utilizar para clonar arrays/objetos, pasar argumentos, etc.

## 4. Contexto Livewire y Alpine.js

* **Alpine.js:** Cuando se use Alpine directamente en las vistas Blade:
    * Mantener la lógica en los atributos (`x-data`, `x-init`, `x-on`, etc.) concisa y legible.
    * Para lógica más compleja, extraerla a funciones reutilizables dentro de `x-data` o considerar componentes Alpine externos.
    * Usar `$wire` para interactuar con el componente Livewire asociado.
    * Usar `$dispatch` para eventos del navegador que Livewire pueda escuchar.
* **Interacción con Livewire:** Para interactuar con el ciclo de vida o eventos de Livewire desde JavaScript:
    * Usar los [Hooks de Ciclo de Vida de Livewire](https://livewire.laravel.com/docs/javascript).
    * Escuchar eventos de Livewire con `Livewire.on('event-name', ...)`.
    * Despachar eventos del navegador para que Livewire los escuche (ej: desde Alpine con `$dispatch` o JS plano con `window.dispatchEvent`).
    * Mantener este código JS organizado, idealmente en archivos separados que se carguen según sea necesario.

## 5. Tipado (Type Checking)

* Utilizar **comentarios JSDoc** para anotar tipos en variables, parámetros de función y valores de retorno. Esto mejora la legibilidad y habilita un mejor soporte de herramientas (IntelliSense, autocompletado, verificación básica).
    ```javascript
    /**
     * Envía un saludo al usuario.
     * @param {string} name El nombre del usuario.
     * @param {number} [retries=3] Número opcional de reintentos.
     * @returns {boolean} Verdadero si el saludo fue exitoso.
     */
    function greetUser(name, retries = 3) {
      // ... implementación
      return true;
    }

    /** @type {import('./types').UserConfig|null} */
    let currentUserConfig = null;
    ```
* *(Opcional)* Para componentes JavaScript muy complejos o reutilizables (especialmente en `laravel-blade-components`), se podría considerar el uso de **TypeScript**, pero JSDoc es el estándar mínimo requerido por ahora.

## 6. Evitar Variables Globales

* No adjuntar variables o funciones directamente al objeto `window` a menos que sea absolutamente necesario para interoperabilidad con librerías de terceros.
* Encapsular código en módulos ES6 (`import`/`export`) o, para scripts simples, en IIFEs (Immediately Invoked Function Expressions) si es necesario para evitar colisiones en el scope global.
* Aprovechar el scope proporcionado por Alpine.js (`x-data`) y las herramientas de compilación de assets de Laravel (Vite).

## 7. Comentarios

* Usar comentarios de una línea (`//`) o multilínea (`/* */`) para explicar lógica compleja, soluciones alternativas (workarounds), o decisiones no obvias.
* Usar JSDoc para documentar la API de funciones y el propósito de variables importantes.

## 8. Verificación Automática (Enforcement)

* Se configurará **ESLint** (y **Prettier** si se adopta) para ejecutarse en el pipeline de CI, fallando si el código no cumple con los estándares definidos.
* Los desarrolladores deben ejecutar los linters/formateadores localmente antes de crear Pull Requests.