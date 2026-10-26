# Akhari Cerámica

Tienda online de Akhari Cerámica, con catálogo público y panel privado de administración.

- **Tienda:** https://akhari.web.app/
- **Administración:** https://akhari.web.app/analia
- **Repositorio canónico:** https://github.com/simondalmasso/akhari
- **Firebase:** proyecto `akhari-e394b`
- **Presupuesto operativo:** Firebase Spark, objetivo de costo $0. No habilitar Blaze ni facturación sin autorización expresa.
- **Pagos:** la web no procesa pagos. El pedido se coordina por WhatsApp.

## Catálogo y precios base

Los costos reales se guardan en el panel de administración; no se muestran en la tienda pública. El precio de venta base equivale al costo más un 60% (costo × 1,60).

| Producto | Costo real (ARS) | Venta al público (ARS) | Diferencia bruta (ARS) |
|---|---:|---:|---:|
| Collar corto | 10.000 | 16.000 | 6.000 |
| Collar largo | 15.000 | 24.000 | 9.000 |
| Pincho planta | 4.000 | 6.400 | 2.400 |
| Pulsera | 5.000 | 8.000 | 3.000 |
| **Total de referencia** | **34.000** | **54.400** | **20.400** |

La diferencia bruta no descuenta embalaje, comisiones, impuestos, entrega ni otros gastos; no equivale a ganancia neta.

En el panel, **Sincronizar precios base** actualiza costo y precio de esos cuatro productos por coincidencia de nombre. Conserva foto, stock, descripción y visibilidad de los productos existentes; crea los que falten. Esta acción requiere iniciar sesión y confirmarla. No se ejecuta automáticamente al abrir la página.

## Funcionalidad

### Tienda pública
- Consulta en Firestore los productos activos.
- Muestra nombre, foto, precio de venta y disponibilidad.
- Si Firestore no devuelve productos o falla la consulta, presenta un catálogo de respaldo con los cuatro precios base.
- Carrito local en el navegador.
- Registra un pedido pendiente en Firestore e intenta abrir WhatsApp con el resumen.
- El usuario debe enviar el mensaje en WhatsApp; abrir el chat no confirma una compra ni garantiza que el pedido se haya guardado.

### Panel privado
- Autenticación por Firebase Authentication (correo y contraseña).
- Solo admite las direcciones autorizadas que figuran en el código; cada usuario define su propia contraseña y debe verificar el correo.
- Alta, edición, visibilidad y eliminación de productos; control de stock y compresión de fotos en el navegador.
- Registro y actualización de estado de pedidos.
- Configuración del número de WhatsApp de la tienda.
- Costo real visible solo en administración y diferencia bruta calculada en pantalla.

### Seguridad
- Las reglas de Firestore exigen sesión verificada y autorización para administrar productos, pedidos y configuración.
- Los visitantes solo pueden listar productos activos; la tienda consulta `active == true`.
- La creación pública de pedidos está restringida por las reglas de Firestore. Los pedidos se crean con estado inicial `pending`.
- La configuración Firebase de una app web incluye una API key pública; la protección depende de reglas de Firestore, restricciones de API y configuración de Authentication. No poner contraseñas ni credenciales privadas en el repositorio.

## Desarrollo y publicación

El sitio es estático y está publicado en Firebase Hosting. Los archivos públicos están en `public/`; las reglas de base de datos están en `firestore.rules`.

Antes de publicar cambios:

1. Revisar `git status`, rama y HEAD.
2. Ejecutar comprobaciones de sintaxis sobre los scripts JavaScript y `git diff --check`.
3. Revisar las reglas de Firestore y comprobar los flujos relevantes.
4. Publicar únicamente el recurso necesario. Para Hosting, usar `firebase deploy --only hosting`; para reglas, `firebase deploy --only firestore:rules`.
5. Confirmar la versión publicada y probar tienda, panel y flujo de pedido.

No ejecutar comandos de facturación, migraciones destructivas, borrado masivo ni cambios de plan. Mantener Firebase Spark y costo $0.

## Verificaciones pendientes de entorno

El código del repositorio y la respuesta HTTP de las páginas no demuestran por sí solos que Authentication, Firestore, correo de verificación, guardado de productos, imágenes o WhatsApp funcionen de punta a punta. Confirmar esos flujos en el proyecto real antes de dar el circuito por validado.
