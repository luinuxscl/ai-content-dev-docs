# Cómo Contribuir

¡Agradecemos tus contribuciones para mejorar el ecosistema de paquetes `luinuxscl/*` y la plataforma AI Content! Para asegurar la calidad, consistencia y mantenibilidad del código, te pedimos que sigas estas directrices al contribuir.

Este documento aplica a todos los repositorios dentro del ecosistema (`luinuxscl/laravel-workspaces`, `luinuxscl/ai-content`, `luinuxscl/ai-content-dev-docs`, etc.).

**Referencias Rápidas:**

* [Flujo de Trabajo de Desarrollo](2_DEVELOPMENT_WORKFLOW.md)
* [Estándares de Código](3_CODING_STANDARDS/)
* [Guía de Desarrollo de Paquetes](4_PACKAGE_DEVELOPMENT_GUIDE/)

## Reportando Bugs

Si encuentras un error o un comportamiento inesperado:

1.  **Busca Primero:** Revisa la sección de "Issues" en el repositorio GitHub **del paquete específico** donde encontraste el bug. Es posible que ya haya sido reportado.
    * Si el bug afecta la interacción entre varios paquetes, repórtalo en el repositorio principal `luinuxscl/ai-content`.
    * Si el bug está en la documentación de estándares, repórtalo en `luinuxscl/ai-content-dev-docs`.
2.  **Crea un Nuevo Issue:** Si no existe, crea un nuevo issue detallado:
    * **Título Claro:** Conciso y descriptivo del problema (ej: "Error al agregar miembro a Workspace si el email contiene caracteres especiales").
    * **Descripción Detallada:**
        * Pasos exactos para reproducir el bug.
        * Comportamiento observado (lo que pasó).
        * Comportamiento esperado (lo que debería haber pasado).
    * **Contexto:** Versión del paquete, versión de PHP, versión de Laravel, navegador (si es un bug de UI).
    * **Evidencia:** Incluye logs de error relevantes (stack traces), screenshots o incluso un video corto si ayuda a ilustrar el problema.
    * **Etiquetas:** Usa etiquetas como `bug`, `ui`, `backend`, etc., si están disponibles.

## Sugiriendo Mejoras o Nuevas Funcionalidades

¿Tienes una idea para mejorar un paquete o añadir una nueva funcionalidad?

1.  **Busca Primero:** Revisa los Issues existentes en el repositorio del paquete relevante para ver si tu idea ya ha sido propuesta o discutida.
2.  **Crea un Nuevo Issue:**
    * **Título Claro:** Descriptivo de la mejora o funcionalidad (ej: "Feature Request: Permitir asignar roles al invitar miembros a Workspace").
    * **Descripción:**
        * Explica claramente la mejora o funcionalidad propuesta.
        * Detalla el **problema** que resuelve o el **valor** que aporta. ¿Por qué es necesaria esta mejora?
        * Describe los casos de uso principales.
        * (Opcional) Puedes sugerir una posible implementación técnica o cambios en la API, pero no es obligatorio.
    * **Etiquetas:** Usa etiquetas como `enhancement`, `feature`, `discussion`, etc.

## Proceso de Contribución de Código (Pull Requests)

¡El código es bienvenido! Sigue estos pasos para contribuir con código:

1.  **Asociación a un Issue:** Asegúrate de que tu contribución corresponda a un Issue existente (sea un `bug` o un `enhancement`/`feature` aprobado). Si no existe, créalo primero y discute la propuesta si es un cambio significativo. Asigna el issue a ti mismo o comenta que estás trabajando en él.
2.  **Sigue el Flujo de Trabajo:** Adhiérete estrictamente al [Flujo de Trabajo de Desarrollo](2_DEVELOPMENT_WORKFLOW.md) (crear rama `feature/*` o `bugfix/*` desde `develop`, etc.).
3.  **Desarrolla:**
    * Escribe código limpio y claro siguiendo **todos** los [Estándares de Código](3_CODING_STANDARDS/).
    * Escribe **pruebas automatizadas** (Unit y/o Feature) que cubran tus cambios y aseguren que la funcionalidad es correcta y no introduce regresiones. Sigue la [Estrategia de Pruebas](4_PACKAGE_DEVELOPMENT_GUIDE/Testing_Strategy.md).
    * Mantén **commits atómicos** y descriptivos, usando preferiblemente [Conventional Commits](#estándares-de-commits-conventional-commits).
    * **Actualiza la documentación** relevante: DocBlocks en el código, el `README.md` del paquete si cambias su uso o API, y el `AI_CONTEXT.md`.
    * **Actualiza el `CHANGELOG.md`** del paquete en la sección `[Unreleased]`, describiendo tu cambio de forma concisa.
4.  **Crea el Pull Request (PR):**
    * Envía el PR apuntando a la rama `develop` del repositorio del paquete.
    * Usa un **título claro** y una **descripción detallada** en el PR, explicando *qué* hace tu cambio y *por qué*. Enlaza al Issue que resuelve (ej: `Closes #123`).
    * Utiliza la plantilla de PR si existe (`templates/PULL_REQUEST_TEMPLATE.md`).
    * Asegúrate de que todas las **verificaciones de CI (tests, linters) pasan** correctamente. Un PR con CI en rojo no será revisado.
5.  **Revisión por Pares:**
    * Al menos un miembro del equipo revisará tu PR.
    * Responde a los comentarios y realiza los ajustes necesarios. Empuja los nuevos commits a tu rama (el PR se actualizará automáticamente).
    * Una vez aprobado y con CI en verde, un mantenedor hará el merge.

## Estándares de Commits (Conventional Commits)

Se **recomienda encarecidamente** seguir la especificación de [Conventional Commits](https://www.conventionalcommits.org/es/v1.0.0/) para los mensajes de commit. Ayuda a automatizar la generación de Changelogs y a entender el historial de cambios.

**Formato Básico:**

```
<tipo>[ámbito opcional]: <descripción>

[cuerpo opcional]

[footer(s) opcional(es)]
```

**Tipos Comunes:**

* `feat`: Una nueva funcionalidad.
* `fix`: Una corrección de bug.
* `docs`: Cambios solo en la documentación.
* `style`: Cambios que no afectan el significado del código (espacios, formato, comas faltantes, etc.).
* `refactor`: Cambios de código que no corrigen un bug ni añaden una funcionalidad.
* `perf`: Cambios de código que mejoran el rendimiento.
* `test`: Añadir tests faltantes o corregir tests existentes.
* `build`: Cambios que afectan el sistema de build o dependencias externas (ej: composer, npm).
* `ci`: Cambios en archivos y scripts de configuración de CI.
* `chore`: Otros cambios que no modifican código fuente ni de tests (ej: actualizar .gitignore).

**Ejemplos:**

```
feat: Add endpoint for listing user workspaces
fix(Auth): Correct redirect loop after password reset
docs(README): Update installation instructions for v1.2
test(Workspaces): Cover edge case for member removal
refactor(Services): Extract PDF generation to dedicated class
chore: Update Orchestra Testbench dev dependency
```

## Contacto / Dudas

Si tienes preguntas sobre cómo contribuir, contacta a [Nombre o Equipo Responsable] a través de [Canal de Comunicación, ej: Slack #development].