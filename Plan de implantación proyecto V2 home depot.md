# 📋 Plan de Implantación Proyecto V2 Home Depot

> ⚠️ Este documento es **estrictamente procedural y arquitectónico**. Solo se incluye código para `pubspec.yaml` y la estructura visual de carpetas, tal como solicitaste. El resto mantiene formato tabular para facilitar la trazabilidad y ejecución por fases.

---

## 🎨 Tabla 1: Diseño UI/UX y Paleta de Colores (Primer Plano vs Fondo)
| Elemento Visual | Color (Hex) | Rol en la Interfaz | Criterio de Accesibilidad |
|----------------|-------------|-------------------|---------------------------|
| **Primario (Acentos/CTA)** | `#F96302` | Botones de acción, indicadores de progreso, iconos activos, headers destacados | Contraste ≥4.5:1 sobre blanco. Hover/Pressed: `#D45500` |
| **Secundario (Fondos)** | `#FFFFFF` | Fondo de pantallas, tarjetas, modales, áreas de formulario | Base limpia para jerarquía visual y lectura |
| **Primer Plano (Texto Principal)** | `#1A1A1A` | Títulos, descripciones, precios, etiquetas críticas | Contraste 16:1 sobre `#FFFFFF` (WCAG AAA) |
| **Primer Plano (Texto Secundario)** | `#4A4A4A` | Subtítulos, placeholders, metadatos, estados inactivos | Reduce fatiga visual, mantiene legibilidad |
| **Bordes & Separadores** | `#E5E7EB` | Divisores de listas, contornos de inputs, bordes de cards | Delimitación sutil sin romper flujo |
| **Estados** | Éxito: `#2E7D32` / Error: `#C62828` / Advertencia: `#F59E0B` | Validaciones, stock, alertas, pagos | Icono + texto semántico. Nunca solo color. |
| **UX Base** | Material 3 adaptado | Navegación inferior (móvil), riel lateral (desktop), deep links, estados explícitos (carga/vacío/error) | Padding `8/12/16/24`, radios `12px`, tipografía escalable, responsive por breakpoint |

---

## 🌳 Estructura de Carpetas y Archivos (Visual)
```
📦 home_depot_app/
├── 📁 bin/                          # Scripts CLI de automatización
│   ├── 📄 setup_env.sh              # Configuración .env + Firebase CLI
│   ├── 📄 build_multiplatform.sh    # Genera APK, IPA, Web y Windows
│   ├── 📄 run_web_debug.sh          # Inicia servidor web debug
│   └── 📄 sync_firestore_rules.sh   # Despliega reglas e índices compuestos
│
├── 📁 lib/
│   ├── 📄 main.dart                 # Entry point + Firebase.initializeApp()
│   ├── 📄 app.dart                  # MaterialApp + MultiProvider raíz
│   │
│   ├── 📁 core/                     # Configuración transversal
│   │   ├── 📁 theme/
│   │   │   ├── 📄 app_colors.dart
│   │   │   ├── 📄 app_text_styles.dart
│   │   │   └── 📄 app_theme_data.dart
│   │   ├── 📁 navigation/
│   │   │   ├── 📄 app_router.dart
│   │   │   └── 📄 route_guards.dart
│   │   └── 📁 utils/
│   │       ├── 📄 validators.dart
│   │       └── 📄 formatters.dart
│   │
│   ├── 📁 data/                     # Capa de acceso a datos
│   │   ├── 📁 models/               # DTOs (json_serializable)
│   │   │   ├── 📄 user_model.dart
│   │   │   ├── 📄 product_model.dart
│   │   │   ├── 📄 order_model.dart
│   │   │   └── 📄 inventory_model.dart
│   │   ├── 📁 services/             # Conexión directa Firebase
│   │   │   ├── 📄 auth_service.dart
│   │   │   └── 📄 firestore_service.dart
│   │   └── 📁 repositories/         # Abstracción para Providers
│   │       ├── 📄 auth_repository.dart
│   │       ├── 📄 product_repository.dart
│   │       └── 📄 cart_repository.dart
│   │
│   ├── 📁 domain/                   # Reglas de negocio puras
│   │   └── 📁 usecases/
│   │       └── 📄 calculate_cart_totals.dart
│   │
│   └── 📁 presentation/             # Capa UI y Estado
│       ├── 📁 screens/
│       │   ├── 📄 splash_screen.dart
│       │   ├── 📄 login_screen.dart
│       │   ├── 📄 home_screen.dart
│       │   ├── 📄 product_detail_screen.dart
│       │   ├── 📄 cart_screen.dart
│       │   └── 📄 profile_screen.dart
│       ├── 📁 widgets/
│       │   ├── 📄 app_button.dart
│       │   ├── 📄 product_card.dart
│       │   ├── 📄 custom_text_field.dart
│       │   └── 📄 state_widgets.dart
│       └── 📁 providers/            # Gestión de estado (Provider)
│           ├── 📄 auth_provider.dart
│           ├── 📄 product_provider.dart
│           ├── 📄 cart_provider.dart
│           └── 📄 user_provider.dart
│
├── 📁 assets/
│   ├── 📁 images/
│   ├── 📁 icons/
│   └── 📁 fonts/
│
├── 📁 test/
│   ├── 📁 unit/
│   ├── 📁 widget/
│   └── 📁 integration/
│
├── 📁 android/
├── 📁 ios/
├── 📁 web/
├── 📁 windows/
├── 📄 pubspec.yaml
└── 📄 .gitignore
```

---

## 📦 `pubspec.yaml` (Dependencias en Código)
```yaml
name: home_depot_app
description: Aplicación multiplataforma Home Depot con Flutter y Firebase.
publish_to: 'none'
version: 1.0.0+1

environment:
  sdk: '>=3.3.0 <4.0.0'

dependencies:
  flutter:
    sdk: flutter

  # 🔥 Firebase (Solo Auth, Firestore y Storage. SIN Analytics ni Crashlytics)
  firebase_core: ^3.10.0
  firebase_auth: ^5.4.0
  cloud_firestore: ^5.6.0
  firebase_storage: ^12.4.0

  # 🔄 Estado & Navegación
  provider: ^6.1.2
  go_router: ^14.6.0

  # 🎨 UI, Imágenes & Formularios
  cached_network_image: ^3.4.0
  flutter_form_builder: ^9.5.0
  form_builder_validators: ^10.0.0

  # 🛠️ Utilidades & Modelos
  intl: ^0.19.0
  equatable: ^2.0.5
  json_annotation: ^4.9.0
  uuid: ^4.5.0

  # ⚙️ Entorno (Solo desarrollo local, excluye prod)
  flutter_dotenv: ^5.2.0

dev_dependencies:
  flutter_test:
    sdk: flutter
  flutter_lints: ^5.0.0
  mocktail: ^1.0.4
  
  # Generadores de código para modelos JSON
  build_runner: ^2.4.13
  json_serializable: ^6.8.0

flutter:
  uses-material-design: true
  assets:
    - assets/images/
    - assets/icons/
    - .env
```

---

## 🗄️ Tabla 2: Mapeo de Entidades/Tablas y Atributos (SQL → Firestore)
| Entidad (Tabla SQL) | Atributos Exactos | Estructura Firestore | Propósito en la App |
|---------------------|-------------------|----------------------|---------------------|
| `cargo` | `id_cargo`, `nombre`, `descripcion`, `salario_base`, `departamento` | `cargos/{id}` | Roles y permisos internos |
| `tienda` | `id_tienda`, `nombre`, `direccion`, `ciudad`, `estado`, `telefono`, `gerente_id`, `horario` | `tiendas/{id}` | Sucursales, ubicación, asignación de inventario |
| `empleado` | `id_empleado`, `nombre`, `email`, `telefono`, `id_tienda`, `id_cargo`, `salario`, `fecha_contrato`, `activo` | `empleados/{uid_auth}` | Vinculado a cuenta Firebase, gestión interna |
| `categoria` | `id_categoria`, `nombre`, `descripcion`, `id_padre`, `nivel` | `categorias/{id}` | Navegación jerárquica del catálogo |
| `proveedor` | `id_proveedor`, `nombre`, `rfc`, `contacto`, `email`, `telefono`, `direccion`, `activo` | `proveedores/{id}` | Gestión de abastecimiento (solo admin) |
| `producto` | `id_producto`, `sku`, `nombre`, `descripcion`, `precio_unitario`, `precio_costo`, `id_categoria`, `id_proveedor`, `unidad_medida`, `activo` | `productos/{id}` | Catálogo principal, búsqueda, filtros |
| `inventario` | `id_inventario`, `id_producto`, `id_tienda`, `cantidad_disponible`, `stock_minimo`, `stock_maximo`, `ubicacion`, `ultima_actualizacion` | `tiendas/{id_tienda}/inventario/{id_producto}` | Stock en tiempo real por sucursal |
| `cliente` | `id_cliente`, `nombre`, `email`, `telefono`, `direccion`, `ciudad`, `fecha_registro`, `tipo_cliente`, `activo` | `clientes/{uid_auth}` | Perfil de usuario, historial, direcciones |
| `pedido` | `id_pedido`, `id_cliente`, `id_empleado`, `id_tienda`, `fecha_pedido`, `estado`, `subtotal`, `impuesto`, `total`, `canal` | `pedidos/{id}` | Transacciones de compra, seguimiento |
| `detalle_pedido` | `id_detalle`, `id_pedido`, `id_producto`, `cantidad`, `precio_unitario`, `descuento`, `subtotal` | `pedidos/{id}/detalles/{id}` | Líneas de compra por pedido |
| `pago` | `id_pago`, `id_pedido`, `metodo_pago`, `monto`, `referencia`, `fecha_pago`, `estado` | `pagos/{id}` | Registro financiero, conciliación |
| `envio` | `id_envio`, `id_pedido`, `direccion_entrega`, `ciudad`, `estado_envio`, `fecha_estimada`, `fecha_entrega`, `costo_envio`, `transportista`, `numero_guia` | `envios/{id}` | Logística y rastreo de entregas |
| `lista_deseos` | `id_lista`, `id_cliente`, `id_producto`, `fecha_agregado`, `nota` | `clientes/{uid}/wishlist/{id_producto}` | Guardado rápido por usuario |

---

## 🚀 Tabla 3: Plan de Implementación Paso a Paso (Fases 1-8)
| Fase | Procedimiento | Plataformas/Recursos | Entregable | Validación |
|------|---------------|----------------------|------------|------------|
| **1. Entorno & `bin/`** | Instalar Flutter/Dart, configurar VS Code, habilitar `android/web/ios/windows`, crear `bin/` con scripts, inicializar Git | Flutter SDK, CLI, plataformas nativas | Repo base, `bin/` funcional, `.gitignore` | `flutter doctor -v` limpio, `bin/setup` ejecutable |
| **2. Firebase & Auth** | Crear proyecto Firebase, habilitar **Email/Password**, registrar apps (Android, iOS, Web, Windows), ejecutar `flutterfire configure`, excluir analíticas | Firebase Console, `firebase_auth` | `firebase_options.dart`, Auth activo | Login/registro manual en Firebase funciona |
| **3. UI/UX & Tema** | Definir `ThemeData` (naranja/blanco/primer plano oscuro), configurar `go_router`, crear `core/theme/`, implementar estructura de pantallas base | Material 3, `go_router` | Tema aplicado, rutas protegidas | UI coherente, contraste verificado |
| **4. Provider & Estado** | Crear `AuthProvider`, `ProductProvider`, `CartProvider`, `UserProvider`. Inyectar vía `MultiProvider`. Separar lógica de presentación | `provider`, `ChangeNotifier` | Capa `providers/` lista | Rebuilds aislados, estado persistente por sesión |
| **5. Firestore & Modelos** | Traducir 13 entidades a Dart models, configurar `FirestoreService`, crear reglas de seguridad, implementar índices compuestos | `cloud_firestore`, `json_annotation` | Capa `data/` completa | CRUD simulado, reglas aplicadas, offline activo |
| **6. Catálogo & Carrito** | Implementar `HomeScreen`, `ProductDetail`, `CartScreen`. Conexión a providers. Paginación, filtros, cálculo de totales | `cached_network_image`, `form_builder` | Flujo catálogo→carrito funcional | Scroll fluido, sync en tiempo real, estados claros |
| **7. Perfil & Pedidos** | Desarrollar `ProfileScreen`, historial `pedido` + `detalle`, `lista_deseos`, cierre de sesión. Validar permisos por usuario | `firebase_auth`, `cloud_firestore` | Gestión de usuario completa | Datos coherentes, aislamiento por UID |
| **8. Build Multiplataforma** | Ejecutar scripts en `bin/` para generar builds: `flutter build apk`, `flutter build web`, `flutter build ipa`, `flutter build windows`. Pruebas en emuladores/físicos | CLI Flutter, plataformas nativas | Binarios listos por plataforma | Ejecución sin errores críticos, UI responsiva |

---

## 🔁 Tabla 4: Gestión de Estado con Provider
| Provider | Responsabilidad | Patrón de Uso | Flujo de Datos | Optimización |
|----------|----------------|---------------|----------------|--------------|
| `AuthProvider` | Login email/pass, registro, verificación, logout, estado de sesión | `ChangeNotifierProvider` | `FirebaseAuth.instance.authStateChanges()` → actualiza estado → `notifyListeners()` | `Selector` para reconstruir solo UI de sesión |
| `ProductProvider` | Catálogo, búsqueda, filtros, paginación, caché local | `ChangeNotifierProvider` + `Stream` | `FirestoreService` → lista `ProductModel` → expone `items`, `loading`, `error` | `limit(20)`, `startAfterDocument`, evitar `listen: true` innecesario |
| `CartProvider` | Items, cantidades, totales, sync con Firestore, checkout | `ChangeNotifierProvider` | Operaciones locales → `set()`/`update()` en Firestore → recálculo de `total` | Transacción atómica en checkout, debounce en UI |
| `UserProvider` | Perfil, direcciones, historial, wishlist | `ChangeNotifierProvider` | `FirestoreService.getUser(uid)` → modelo → expone estado | Caché en memoria, refresh manual post-update |

---

## ✅ Validación y Siguientes Pasos
1. **Paleta & UX**: Los colores de primer plano (`#1A1A1A`/`#4A4A4A`) sobre fondo blanco y acento naranja `#F96302` cumplen con los estándares de contraste y la identidad solicitada.
2. **Estructura & `bin/`**: La carpeta `bin/` incluye scripts listos para automatizar setup, builds multiplataforma y sincronización de reglas.
3. **Dependencias**: `pubspec.yaml` excluye explícitamente analíticas y paquetes de producción, limitándose a Auth, Firestore, Storage, Provider y utilidades locales.
4. **Entidades**: Las 13 tablas están mapeadas a documentos/subcolecciones preservando atributos exactos y relaciones lógicas para NoSQL.

🔹 **Si el plan es aprobado**, el siguiente entregable será la **configuración inicial ejecutable**: `main.dart`, `app_theme.dart`, `firebase_options.dart` base y estructura de `AuthProvider` lista para conectar con Firebase.

¿Deseas ajustar algún detalle o procedemos directamente a la fase de código inicial?
