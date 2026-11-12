# Ficha técnica

**Proyecto:** Consentimiento informado digital para mascotas
**Caso de estudio:** Estética de Mascotas de Pilar
**Autores:** Dylan Felipe Parra y Maikol Stiven Osorio
**Versión:** 1.0 — 11 de noviembre de 2026
**Estado:** Base de trabajo del MVP; pendiente de validar campos y texto con el negocio, el equipo y el docente.

## 1. Propósito

Definir qué se construirá para el MVP, cómo se relacionan sus partes y cómo se comprobará que funciona. Esta ficha guía el desarrollo; no reemplaza asesoría jurídica ni garantiza que un consentimiento exonere al negocio de responsabilidad.

## 2. Problema y objetivo

El negocio necesita conservar un registro ordenado de que el responsable de una mascota leyó y aceptó el consentimiento antes del servicio.

El MVP permitirá que el cliente llegue al formulario desde un código QR o la tablet del negocio, registre sus datos y los de su mascota, lea el texto vigente, confirme su aceptación, firme en pantalla y envíe el registro. Pilar iniciará sesión para consultar la evidencia guardada.

## 3. Alcance

### Incluido

- Formulario público responsivo para celular, tablet y computador.
- Acceso al mismo formulario mediante QR o enlace.
- Captura de los datos mínimos aprobados del responsable y una mascota.
- Presentación del texto vigente completo antes de aceptar y firmar.
- Firma dibujada con dedo o mouse.
- Validación de datos y de la firma en el servidor.
- Registro atómico en la base de datos con fecha y hora asignadas por el servidor.
- Inicio de sesión para Pilar y consulta protegida de la lista y el detalle de consentimientos.
- Vista de impresión del detalle desde el navegador.

### Fuera del MVP

- PDF descargable o envío por correo/WhatsApp.
- Historial avanzado, búsqueda, cuentas de clientes o deduplicación automática.
- Fotos, datos médicos, alergias, citas, pagos, inventario, sucursales o aplicación nativa.
- Administración del texto legal desde una interfaz.
- Microservicios, blockchain o mecanismos que no sean necesarios para el flujo descrito.

## 4. Actores y permisos

| Actor | Rol del sistema | Permisos MVP |
|---|---|---|
| Pilar | `ADMIN` | Iniciar y cerrar sesión; consultar lista y detalle de consentimientos. |
| Responsable de la mascota | `CLIENTE` | Completar y enviar el formulario público; no tiene cuenta ni acceso a registros. |

Una sola tabla `usuario` representa ambos tipos de persona y el atributo `rol` los distingue. El formulario público no permite que el visitante elija el rol, establezca privilegios ni cree una contraseña de administrador. Las credenciales solo aplican a la cuenta interna de Pilar.

## 5. Flujo principal

1. El cliente escanea el QR con la cámara del dispositivo o abre el enlace del formulario en la tablet.
2. El sistema muestra los datos que debe completar y la versión vigente del consentimiento.
3. El cliente registra sus datos y los de **una mascota por envío**.
4. El cliente revisa el consentimiento completo, marca su aceptación y firma.
5. El navegador envía los datos a la API; no se conecta directamente a PostgreSQL.
6. La API valida los campos, la aceptación, la firma y la versión vigente.
7. En una transacción, el servidor conserva el registro completo y asigna la fecha y hora de recepción.
8. El cliente recibe una confirmación que no expone registros de otras personas.
9. Pilar inicia sesión y consulta la lista o el detalle protegido del consentimiento.

**Regla de MVP:** cada envío corresponde a una mascota y a una visita/servicio. Si el cliente llega con más de una mascota, completa un envío separado por cada una. Una visita posterior requiere un nuevo consentimiento. Esta regla evita ambigüedades sobre qué mascota cubre cada firma; el negocio y el docente deben confirmarla antes del piloto.

## 6. Requisitos funcionales

| ID | Requisito |
|---|---|
| RF-01 | El formulario público recoge los datos mínimos aprobados del responsable y una mascota. |
| RF-02 | El cliente puede leer el texto completo de la versión vigente antes de aceptarlo. |
| RF-03 | El sistema no envía el formulario si falta un dato obligatorio, no se acepta el texto o la firma está vacía. |
| RF-04 | La firma funciona con mouse o pantalla táctil. |
| RF-05 | La API valida nuevamente los datos; no confía en la validación del navegador. |
| RF-06 | La API asigna la fecha y hora de recepción en el servidor y guarda el envío completo o no guarda nada. |
| RF-07 | La evidencia permite identificar los datos capturados, la mascota, la firma y la versión exacta aceptada. |
| RF-08 | El QR abre el formulario público sin requerir una aplicación especial. |
| RF-09 | Pilar inicia sesión y consulta la lista cronológica y el detalle de los registros. |
| RF-10 | Una persona sin autorización no puede consultar registros, aunque conozca la dirección de la API o de una página administrativa. |
| RF-11 | El cliente recibe confirmación tras un envío exitoso, sin acceso a los registros de otras personas. |

## 7. Reglas de negocio y protección de la evidencia

- La fecha y hora se generan en el servidor y se almacenan con zona horaria.
- Cada consentimiento referencia una versión publicada e inmutable del texto. Una modificación del texto crea una versión nueva.
- El consentimiento enviado no se edita ni se elimina desde la aplicación. Si hay un error, se conserva el original y se solicita un nuevo envío; cualquier excepción requiere una política aprobada.
- La API y la base de datos restringen la consulta de evidencia a `ADMIN`. Ocultar controles en la interfaz no cuenta como autorización.
- El cliente no recibe identificadores que permitan consultar otros registros.
- El formulario público asigna internamente el rol `CLIENTE`; jamás acepta del navegador rol, estado activo, contraseña, fecha de firma ni propietario de la cuenta administradora.
- La creación del responsable, la mascota y el consentimiento se realiza de forma atómica.
- Los datos de responsable, mascota y firma son personales o potencialmente sensibles. Se recopila únicamente lo necesario; no se pide documento de identidad, información médica, IP ni agente del navegador salvo justificación y aprobación explícitas.
- El contacto obligatorio se decidirá con Pilar y el docente. No se asume que teléfono o correo sean identificadores únicos: pueden compartirse o cambiar.
- En el MVP, cada envío puede crear un registro de cliente. La identificación de clientes recurrentes y la eliminación de duplicados quedan fuera de alcance hasta definir una regla segura.
- La firma y el consentimiento sirven para conservar evidencia de aceptación, pero no aseguran por sí solos la validez jurídica ni exoneran al negocio de toda responsabilidad.

## 8. Modelo de datos inicial

El modelo relacional tiene cuatro entidades principales. PostgreSQL aplica claves foráneas, restricciones y transacciones. El ER complementario está en [diagrama-er-mvp.html](./diagrama-er-mvp.html).

### `usuario`

Persona registrada como cliente o cuenta interna.

| Campo | Uso |
|---|---|
| `id` | Identificador UUID, clave primaria. |
| `nombre_completo` | Nombre capturado en el formulario. |
| `telefono` | Contacto; obligatoriedad pendiente de confirmar. |
| `correo` | Contacto opcional o requerido según la decisión del equipo. No se considera único por defecto. |
| `rol` | `CLIENTE` o `ADMIN`; el servidor controla la asignación. |
| `contrasena_hash` | Solo para cuentas que inician sesión; nunca almacenar una contraseña legible. |
| `activo` | Permite desactivar una cuenta interna. |
| `creado_en` | Fecha asignada por el servidor. |

### `mascota`

| Campo | Uso |
|---|---|
| `id` | Identificador UUID, clave primaria. |
| `usuario_id` | Clave foránea al responsable registrado. |
| `nombre` | Nombre de la mascota. |
| `especie` | Especie; en el MVP el flujo se centra en perros. |
| `raza` | Opcional si el negocio lo necesita. |
| `edad_aproximada_meses` | Opcional; no pedir si no aporta al flujo aprobado. |
| `creado_en` | Fecha asignada por el servidor. |

### `version_consentimiento`

| Campo | Uso |
|---|---|
| `id` | Identificador UUID, clave primaria. |
| `numero` | Número de versión único. |
| `texto` | Texto íntegro presentado para aceptación. |
| `vigente` | Indica la versión que se ofrece en formularios nuevos. |
| `publicado_en` | Fecha de publicación asignada por el administrador autorizado. |

No se modifica el texto de una versión ya utilizada en un envío. Se publica una nueva versión.

### `consentimiento`

| Campo | Uso |
|---|---|
| `id` | Identificador UUID, clave primaria. |
| `mascota_id` | Clave foránea a la mascota de este envío. |
| `version_id` | Clave foránea a la versión exacta aceptada. |
| `responsable_firmante_snapshot` | Nombre del responsable tal como se mostró al firmar. |
| `telefono_firmante_snapshot` | Teléfono mostrado al firmar, si se recopila. |
| `correo_firmante_snapshot` | Correo mostrado al firmar, si se recopila. |
| `mascota_nombre_snapshot` | Nombre de la mascota tal como se mostró al firmar. |
| `mascota_especie_snapshot` | Especie de la mascota tal como se mostró al firmar. |
| `mascota_raza_snapshot` | Raza tal como se mostró, si se recopila. |
| `acepta_consentimiento` | Debe ser verdadero para aceptar el envío. |
| `firma_imagen` | Firma como PNG binario (`BYTEA`/`Bytes` en PostgreSQL/Prisma), con tamaño validado; nunca se acepta vacía. |
| `firmado_en` | Fecha y hora asignadas por el servidor. |

**Integridad histórica:** como los datos actuales del responsable o de la mascota podrían cambiar, el consentimiento guarda una instantánea de los campos que se mostraron al firmante. La versión del texto se referencia por su clave foránea y no se modifica una vez publicada.

La aplicación debe mantener una sola versión vigente para formularios nuevos. Una restricción o transacción en el servidor debe evitar que dos versiones queden vigentes simultáneamente.

## 9. Arquitectura y herramientas

Se usará un repositorio único organizado como monorepo, con dos aplicaciones separadas y una base de datos:

```text
apps/
  web/    Next.js: formulario público y panel de Pilar
  api/    Nest.js: API, validación, autenticación y reglas de negocio
packages/
  shared/ Tipos o validaciones compartidas, solo si resultan útiles
database/ PostgreSQL local, configuración y migraciones
```

| Capa | Herramienta inicial | Responsabilidad |
|---|---|---|
| Interfaz web | Next.js, React, TypeScript y Tailwind CSS | Formulario, consentimiento, firma y panel administrativo responsivo. |
| API/backend | Nest.js y TypeScript | Validación autoritativa, autenticación, permisos, lógica y persistencia. |
| Base de datos | PostgreSQL local durante desarrollo | Almacenamiento relacional de usuarios, mascotas, versiones y consentimientos. |
| ORM/migraciones | Prisma | Modelo tipado y cambios reproducibles de esquema. |
| Validación | Zod en formularios; validación equivalente en Nest.js | Mensajes de formulario y verificación obligatoria en servidor. |
| Firma | `signature_pad` | Captura táctil o con mouse en el navegador. |
| QR | `qrcode` | Genera un QR que apunta a la URL pública del formulario. |
| Pruebas | Vitest y Playwright | Pruebas unitarias/de integración y flujo web de extremo a extremo. |
| Entorno local | Docker Compose | Ejecuta PostgreSQL de desarrollo de forma reproducible. |

El navegador se comunica con la API Nest.js por HTTPS en producción y nunca recibe credenciales de base de datos. No se implementan microservicios. Alojamiento y proveedor de producción se deciden después de probar costos, privacidad, copias de seguridad y capacidad de mantenimiento.

## 10. Rutas y operaciones previstas

| Método y ruta | Acceso | Función |
|---|---|---|
| `GET /consents/current` | Público | Devuelve solo la versión vigente que debe mostrar el formulario. |
| `POST /consents` | Público, con límites antiabuso | Valida y guarda un consentimiento completo; el servidor asigna la hora. |
| `POST /auth/login` | Público | Inicia sesión para Pilar. |
| `POST /auth/logout` | Sesión activa | Cierra la sesión. |
| `GET /auth/me` | Sesión activa | Devuelve información mínima de la cuenta actual. |
| `GET /admin/consents` | Solo `ADMIN` | Lista cronológica paginada de consentimientos. |
| `GET /admin/consents/:id` | Solo `ADMIN` | Devuelve el detalle y evidencia del registro seleccionado. |

Las rutas son una guía inicial y pueden ajustarse a las convenciones del equipo. Las respuestas públicas no incluyen datos de registros ya guardados.

## 11. Requisitos no funcionales

- Diseño usable en celular, tablet y computador.
- Validación del lado del cliente y obligatoriamente del servidor.
- Contraseñas con hash seguro; sesiones protegidas y cookies `HttpOnly`, `Secure` y `SameSite` en producción.
- Secretos solo en variables de entorno excluidas de Git.
- Consultas administrativas protegidas en el backend; no confiar solo en la interfaz.
- Restricciones de base de datos y transacción para evitar envíos parciales.
- Paginación de la lista y límites razonables de tamaño para la imagen de firma.
- Registros y versiones publicadas tratados como inmutables desde la aplicación.
- Mensajes claros ante errores, sin reportar éxito si el guardado falla.
- Copias de seguridad y restauración probadas antes de usar datos reales.
- Registro de errores operativos sin guardar innecesariamente contenido de formularios, firmas ni secretos.

## 12. Criterios de aceptación

El MVP está listo para una demostración cuando:

1. El QR abre el formulario en un teléfono sin instalar otra aplicación.
2. El cliente puede leer el texto, completar los datos mínimos y firmar en celular y tablet.
3. El sistema rechaza datos obligatorios incompletos, aceptación ausente y firma vacía tanto en la interfaz como en la API.
4. Un envío exitoso crea el registro íntegro y la hora corresponde al servidor.
5. El registro queda asociado a la mascota y a la versión exacta que se mostró.
6. Pilar puede autenticarse, ver la lista y abrir el detalle con la firma.
7. Una persona no autenticada no puede leer la lista ni un detalle, incluso si conoce la URL de la API.
8. Un error durante el guardado no deja filas parciales.
9. Las pruebas cubren validaciones, envío correcto, envío rechazado, hora del servidor y acceso no autorizado.

## 13. Plan de trabajo

1. **Alinear requisitos:** confirmar esta ficha, datos obligatorios, regla por mascota/visita y revisión del texto con negocio/docente.
2. **Preparar entorno:** instalar Node.js LTS y Docker Desktop, crear monorepo Next/Nest y levantar PostgreSQL local.
3. **Cerrar el modelo:** acordar instantáneas de evidencia, convertir el ER a Prisma y crear la migración inicial.
4. **Construir el flujo público:** versión del texto, formulario, validación y firma.
5. **Guardar y confirmar:** endpoint Nest, transacción, fecha del servidor y confirmación web.
6. **Panel de Pilar:** autenticación, lista y detalle protegidos.
7. **Verificar y preparar piloto:** pruebas en dispositivos reales, revisión de privacidad/texto legal, respaldo y decisión de alojamiento.

## 14. Pendientes antes de usar con clientes reales

- Confirmar con Pilar y el docente los datos obligatorios y si la regla de un consentimiento por mascota y visita es correcta.
- Revisar el texto del consentimiento, el aviso de privacidad, la firma electrónica y las obligaciones aplicables en el país donde opera el negocio.
- Definir responsable del tratamiento, propósito, acceso, conservación, solicitudes de titulares y eliminación permitida por ley.
- Definir una política aprobada para corregir datos erróneos sin alterar evidencia firmada.
- Definir almacenamiento, copias de seguridad, recuperación y controles de acceso en producción.
- No afirmar que la aplicación garantiza inmunidad frente a reclamaciones. La validez y efecto jurídico dependen de la jurisdicción, el proceso y los hechos; solicitar asesoría profesional.
