# 3.3 Estándares de Código CSS / Estilado

Este documento define las convenciones y mejores prácticas para escribir CSS y aplicar estilos en el ecosistema `luinuxscl/*`, asegurando consistencia visual y mantenibilidad. El objetivo es aprovechar al máximo los frameworks adoptados y minimizar el CSS personalizado.

## 1. Frameworks Principales

* **Tailwind CSS 4:** Es el framework **fundamental** basado en utilidades. La **principal forma** de aplicar estilos debe ser mediante la aplicación directa de clases de utilidad de Tailwind en los archivos Blade/HTML.
* **DaisyUI:** Se utiliza **sobre** Tailwind para proporcionar un conjunto de **componentes pre-estilizados** y semánticos (botones, modales, cards, etc.) a través de clases como `btn`, `card`, `modal`. Se **DEBE** priorizar el uso de estos componentes y clases de DaisyUI, que serán provistos principalmente a través del paquete `luinuxscl/laravel-blade-components`.

## 2. Filosofía Utility-First

* Adoptar completamente el enfoque **Utility-First** de Tailwind. Construir interfaces componiendo clases de utilidad directamente en el markup.
* **Evitar** escribir CSS personalizado de forma prematura. Intentar siempre lograr el estilo deseado combinando utilidades existentes de Tailwind y clases de componentes de DaisyUI.

## 3. Configuración de Tailwind (`tailwind.config.js`)

* La configuración central del tema (colores, tipografía, espaciado, breakpoints, configuración de DaisyUI y plugins) reside idealmente en la aplicación principal (`luinuxscl/ai-content`) o en el paquete UI central (`luinuxscl/laravel-blade-components`).
* Los paquetes funcionales individuales (`laravel-workspaces`, etc.) **NO DEBEN** incluir su propio `tailwind.config.js` ni añadir CSS global, a menos que sea absolutamente indispensable y justificado para su funcionalidad aislada (lo cual es poco común).
* Utilizar las claves definidas en la sección `theme` (y `theme.extend`) del archivo `tailwind.config.js` para aplicar valores consistentes (ej: `bg-primary`, `text-gray-700`, `p-4`) en lugar de valores mágicos o hardcodeados.

## 4. Uso de `@apply` y Componentes Blade/Livewire

* El uso de la directiva `@apply` de Tailwind dentro de archivos CSS para crear clases de componentes **DEBE SER LIMITADO**. Abusar de `@apply` va en contra de la filosofía utility-first y puede llevar a CSS difícil de mantener.
* **Alternativas Preferidas:**
    1.  **Componentes Blade/Livewire:** Crear componentes reutilizables (principalmente en `luinuxscl/laravel-blade-components`) que encapsulen el markup y las clases de utilidad necesarias. Ejemplo: `<x-button color="primary">Texto</x-button>`.
    2.  **Clases de DaisyUI:** Utilizar directamente las clases que proporciona DaisyUI para sus componentes (ej: `<button class="btn btn-primary">Texto</button>`).
* **Cuándo usar `@apply`:** Solo como último recurso para extraer patrones de utilidad *muy* repetitivos que *no* se pueden encapsular lógicamente en un componente y que mejorarían significativamente la legibilidad del HTML. Debe justificarse.

## 5. Clases CSS Personalizadas

* Crear clases CSS personalizadas solo cuando el estilo o comportamiento deseado **no pueda lograrse razonablemente** con la combinación de utilidades Tailwind y componentes/clases DaisyUI (ej: animaciones complejas, layouts muy específicos, overrides profundos).
* **Nomenclatura:** Si se crean clases personalizadas, **SE DEBE** usar la metodología **BEM (Block, Element, Modifier)** para evitar colisiones y mejorar la legibilidad.
    * Formato: `.prefijo-nombre-bloque[__elemento][--modificador]`
    * Prefijos Sugeridos: `c-` para componentes (`.c-user-profile`), `u-` para utilidades específicas (`.u-text-shadow`), `is-` o `has-` para estados (`.is-active`).
    * Ejemplo:
        ```css
        .c-invoice-summary { /* Bloque */ }
        .c-invoice-summary__line-item { /* Elemento */ }
        .c-invoice-summary__line-item--total { /* Modificador */ }
        ```
* **Ubicación:** El CSS personalizado debe residir:
    * Preferentemente en el paquete `luinuxscl/laravel-blade-components` si es para componentes reutilizables.
    * En la aplicación principal (`luinuxscl/ai-content`) para estilos específicos de la aplicación.
    * **Evitar** definir CSS personalizado significativo dentro de los paquetes funcionales backend.

## 6. Modo Oscuro (Dark Mode)

* El starter kit soporta modo oscuro. Todos los estilos y componentes **DEBEN** ser compatibles.
* Utilizar la variante `dark:` de Tailwind para aplicar estilos específicos del modo oscuro (ej: `bg-white dark:bg-gray-800`).
* Aprovechar las variables de color y temas de DaisyUI que ya incorporan soporte para modo oscuro.
* Probar siempre los componentes y vistas en ambos modos.

## 7. Variables CSS (Custom Properties)

* Utilizar principalmente las variables CSS que Tailwind y DaisyUI generan a partir de la configuración del tema.
* Definir variables CSS personalizadas (ej: `:root { --mi-color-especial: #123456; }`) solo si es necesario para funcionalidades avanzadas (theming dinámico complejo, interoperabilidad con JS) y declararlas en un scope adecuado (global o específico del componente).

## 8. Organización y Linting

* Mantener los archivos CSS (si los hay) organizados y limpios.
* Considerar el uso de **Stylelint** con una configuración adecuada para asegurar la calidad y consistencia del CSS personalizado. Integrar la verificación en el pipeline de CI.