**`4_PACKAGE_DEVELOPMENT_GUIDE/Structure.md`**

Este documento especificará la estructura de directorios y archivos estándar que *todos* los paquetes `luinuxscl/*` deben seguir. Esto es vital para que cualquier desarrollador pueda navegar y entender rápidamente la organización de cualquier paquete dentro del ecosistema.

# 4.1 Estructura de Directorios Estándar para Paquetes

Este documento define la estructura de directorios y archivos recomendada y esperada para todos los paquetes Laravel desarrollados bajo el namespace `luinuxscl/*` para la plataforma AI Content. Seguir una estructura consistente facilita la navegación, el mantenimiento y la colaboración entre equipos.

La estructura se basa en las convenciones comunes de desarrollo de paquetes Laravel, con algunas especificaciones adicionales.

## Estructura Raíz del Repositorio del Paquete

Cada paquete debe tener la siguiente estructura de directorios y archivos en su raíz:

```
.
├── AI_CONTEXT.md             # Contexto específico para asistentes AI (Ver sección X.X)
├── CHANGELOG.md              # Registro de cambios por versión (Keep a Changelog format)
├── LICENSE.md                # Archivo de licencia (ej: MIT, o especificar si es propietario)
├── README.md                 # Descripción del paquete, instalación, uso básico
├── composer.json             # Dependencias y autoloading (PSR-4)
├── phpunit.xml               # Configuración de PHPUnit (o pest.xml si se usa Pest)
├── .gitignore                # Archivos y directorios a ignorar por Git
│
├── config/                   # Archivos de configuración publicables
│   └── package-name.php      # ej: workspaces.php (nombre en snake_case)
│
├── database/
│   ├── factories/            # Model Factories
│   └── migrations/           # Migraciones de base de datos
│   # └── seeders/            # (Opcional) Seeders si el paquete los proporciona
│
├── resources/
│   ├── lang/                 # Archivos de traducción
│   │   └── en/
│   │       └── package-name.php
│   │   └── es/
│   │       └── package-name.php
│   └── views/                # Vistas Blade (principalmente para componentes demo/internos)
│       └── components/       # Componentes Blade específicos de este paquete (¡no reutilizables!)
│
├── routes/                   # (Opcional) Archivos de rutas si el paquete las define
│   ├── api.php
│   └── web.php               # A menudo para rutas de demo/testing
│
├── src/                      # TODO el código fuente PHP del paquete (Namespace: LuinuxScl\PackageName)
│   └── ... (ver detalle abajo)
│
└── tests/                    # Pruebas automatizadas
    ├── Feature/              # Feature tests
    ├── Unit/                 # Unit tests
    ├── TestCase.php          # Clase base para los tests del paquete
    └── Pest.php              # (Opcional) Si se usa Pest
```

## Detalle del Directorio `src/`

El código fuente del paquete reside aquí, siguiendo el estándar PSR-4 definido en `composer.json`. La estructura interna recomendada es:

```
src/
├── Console/                  # Comandos de Artisan (ej: InstallPackageCommand)
├── Contracts/                # Interfaces definidas por el paquete (API pública o interna)
├── Database/                 # (Opcional) Clases relacionadas con BBDD que no son modelos (ej: Repositories si se usan)
├── Events/                   # Clases de Eventos despachados por el paquete
├── Exceptions/               # Clases de Excepciones personalizadas
├── Facades/                  # (Opcional) Fachadas para acceso simplificado (usar con moderación)
├── Http/
│   ├── Controllers/          # Controladores (si el paquete define rutas web/api)
│   ├── Livewire/             # Componentes Livewire (¡SOLO para demo/testing interno!)
│   │   └── Demo/             # (Namespace sugerido para claridad)
│   ├── Middleware/           # Middleware específico del paquete
│   ├── Requests/             # Form Requests para validación
│   └── Resources/            # API Resources (si define APIs)
├── Jobs/                     # Clases de Jobs para colas
├── Listeners/                # Listeners para eventos (propios o de Laravel/otros paquetes)
├── Models/                   # Modelos Eloquent y Traits relacionados con modelos
├── Providers/                # (Opcional) Service Providers adicionales si se necesita modularidad interna
├── Rules/                    # (Opcional) Reglas de validación personalizadas
├── Services/                 # Clases de Servicio (lógica de negocio principal)
├── Traits/                   # Traits genéricos reutilizables dentro del paquete
│
└── PackageNameServiceProvider.php  # El Service Provider principal del paquete (ej: WorkspacesServiceProvider.php)
```

## Notas Adicionales

* **No todos los directorios son necesarios:** Un paquete simple podría no necesitar `routes/`, `resources/views/`, `Console/`, etc. Incluir solo los directorios que se utilicen.
* **Autoloading:** Configurar correctamente el autoloading PSR-4 en `composer.json` para el namespace principal del paquete (ej: `"LuinuxScl\\Workspaces\\": "src/"`).
* **Service Provider:** El `PackageNameServiceProvider.php` es el punto de entrada principal del paquete. Debe registrar bindings, publicar assets (config, migraciones, vistas, lang), registrar comandos, rutas, etc.
* **Demo/Testing UI:** Reiterar que cualquier componente Livewire o Vista Blade dentro de un paquete funcional debe ser exclusivamente para fines de demostración o prueba interna de ese paquete, no para la UI reutilizable de la aplicación final (eso pertenece a `luinuxscl/laravel-blade-components`).