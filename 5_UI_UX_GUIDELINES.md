# 5. Guías de Desarrollo UI/UX

Este documento establece las directrices para el diseño y desarrollo de interfaces de usuario (UI) y experiencia de usuario (UX) dentro de la plataforma "AI Content". El objetivo es lograr una interfaz **coherente, intuitiva, eficiente y accesible** en todas las partes de la aplicación, aprovechando al máximo los componentes reutilizables y el sistema de diseño establecido.

## 1. Paquete UI Central (`luinuxscl/laravel-blade-components`)

* **Fuente Única de Verdad (Single Source of Truth):** Este paquete es el repositorio central y la **fuente única de verdad** para todos los componentes de UI reutilizables (componentes Blade, componentes Livewire genéricos como modales, selects avanzados), la configuración base de Tailwind CSS y DaisyUI (tema, colores, tipografía), y posibles helpers JavaScript para la UI.
* **Uso Obligatorio:** Antes de implementar cualquier elemento de interfaz, se **DEBE** verificar si ya existe un componente adecuado en `luinuxscl/laravel-blade-components`. **Se priorizará siempre el uso de estos componentes existentes.**
* **Exploración de Componentes:** *(Nota: Definir aquí cómo los desarrolladores pueden ver qué componentes existen. Ejemplos:)*
    * "Consultar el catálogo visual de componentes disponible en [enlace a Storybook o demo del paquete]".
    * "Explorar el directorio `resources/views/components` dentro del repositorio `luinuxscl/laravel-blade-components`".

## 2. Desarrollo de Interfaces

* **Composición sobre Creación:** Construir interfaces complejas componiendo los componentes reutilizables provistos por `laravel-blade-components`.
* **Responsabilidad de la UI Final:** La implementación de las vistas y la orquestación final de la interfaz de usuario recae principalmente en la **aplicación integradora (`luinuxscl/ai-content`)**. Esta aplicación utilizará los componentes del paquete UI central y creará sus propios componentes Livewire específicos de página o de funcionalidades complejas que interactúen con los servicios de los paquetes backend.
* **Componentes Livewire de Demo/Test:** Se reitera que los componentes Livewire encontrados dentro de los paquetes funcionales (ej: `laravel-workspaces/src/Http/Livewire/Demo`) son **exclusivamente para demostración y prueba interna** de dicho paquete. **NO DEBEN** ser utilizados directamente en la UI de producción de `luinuxscl/ai-content`.

## 3. Solicitud de Nuevos Componentes Reutilizables

* **Identificación de Necesidad:** Si durante el desarrollo en `luinuxscl/ai-content` (o incluso en un paquete funcional) se identifica la necesidad de un elemento UI que **no existe** en `laravel-blade-components` y que **tiene potencial de ser reutilizado** en otras partes de la aplicación o en futuros proyectos.
* **NO Implementar Directamente (si es reutilizable):** Evitar crear el componente directamente en `ai-content` si es claramente un candidato para la biblioteca central.
* **Proceso de Solicitud:**
    1.  **Crear "Prompt de Componente":** Documentar detalladamente la necesidad:
        * Funcionalidad requerida.
        * Comportamiento esperado (incluyendo diferentes estados: normal, hover, active, disabled, loading, error).
        * Interacciones del usuario.
        * Props/atributos necesarios para configurarlo.
        * Apariencia visual deseada (adjuntar wireframe, mockup o descripción detallada).
        * Contexto(s) de uso previsto(s).
    2.  **Enviar Solicitud:** Presentar este "prompt" al equipo o persona responsable del paquete `luinuxscl/laravel-blade-components` (ej: a través de un Issue en el repositorio de ese paquete).
* **Implementación Centralizada:** El equipo responsable evaluará la solicitud. Si se aprueba, el componente se implementará, probará y documentará dentro de `luinuxscl/laravel-blade-components`.
* **Consumo:** Una vez disponible en una nueva versión del paquete UI, podrá ser consumido en `ai-content` o donde sea necesario.

## 4. Consistencia Visual y de Interacción

* **Sistema de Diseño:** Adherirse **estrictamente** a las definiciones del sistema de diseño (paleta de colores primarios/secundarios/semánticos, escala tipográfica, sistema de espaciado, iconografía) tal como están configuradas en `tailwind.config.js` (a través de `laravel-blade-components`). No introducir colores o fuentes personalizadas fuera del tema establecido.
* **Componentes DaisyUI:** Utilizar las clases de componentes de DaisyUI (`btn`, `card`, `alert`, `modal`, `dropdown`, etc.) para asegurar la coherencia en elementos comunes.
* **Patrones de Interacción:** Reutilizar los patrones de interacción establecidos para acciones comunes. Por ejemplo, usar los mismos estilos y comportamientos para botones de acción principal vs. secundaria, usar los componentes modales estándar para confirmaciones, emplear el sistema de notificaciones (toasts) provisto para feedback al usuario, etc.
* **Accesibilidad (a11y):** El desarrollo de UI debe considerar activamente la accesibilidad. Utilizar HTML semántico, asegurar contraste de color adecuado, proveer alternativas textuales para imágenes (alt text), y usar atributos ARIA cuando sea necesario y no sea manejado automáticamente por los componentes base.
* **Modo Oscuro:** Todas las interfaces deben ser probadas y funcionar correctamente tanto en modo claro como oscuro, utilizando las variantes `dark:` de Tailwind y asegurando la compatibilidad de cualquier CSS personalizado.

## 5. Estilos y CSS

* **Referencia:** Seguir las directrices establecidas en `3_CODING_STANDARDS/CSS.md` sobre el uso prioritario de Tailwind/DaisyUI, la limitación de `@apply`, y el uso de BEM para CSS personalizado (si es inevitable).
* **Ubicación:** Reiterar que el CSS personalizado significativo debe residir en `laravel-blade-components` (si es reutilizable) o en `ai-content` (si es específico de la aplicación). **Evitar CSS** en los paquetes funcionales backend.