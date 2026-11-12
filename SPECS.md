# Especificación del MVP

## 1. Para qué sirve este documento

Este documento describe, en lenguaje directo, qué vamos a construir primero y cómo lo vamos a comprobar. Es la referencia de trabajo para el MVP; no es un diseño legal ni una decisión sobre el proveedor final de alojamiento.

## 2. Objetivo

Crear una aplicación web sencilla para que un cliente registre sus datos y los de su mascota, lea y acepte el consentimiento informado, firme en pantalla y deje un registro que Pilar pueda consultar.

## 3. Qué incluye la primera versión

### Cliente

1. Abre el formulario desde un enlace o un código QR.
2. Completa sus datos y los de la mascota.
3. Lee el texto completo del consentimiento.
4. Marca su aceptación y firma en la pantalla del celular o la tablet.
5. Envía el formulario y recibe una confirmación.

### Pilar

1. Inicia sesión.
2. Ve la lista de consentimientos recibidos.
3. Abre un registro y consulta los datos, el texto aceptado, la firma y la fecha y hora de envío.
4. Puede imprimir o guardar la página desde el navegador si necesita una copia.

## 4. Qué dejamos para después

No construir en esta etapa:

- Exportación automática a PDF.
- Búsqueda avanzada e historial separado por mascota.
- Fotos, datos médicos, alergias o envío por WhatsApp/correo.
- Agenda, pagos, inventario, varias sucursales o aplicación móvil.
- Microservicios, blockchain u otros mecanismos que no sean necesarios para el flujo básico.

## 5. Reglas que debe cumplir el MVP

- El formulario debe funcionar en celular, tablet y computador.
- No se puede enviar si faltan datos obligatorios, no se aceptó el consentimiento o la firma está vacía.
- La fecha y hora de recepción las genera el servidor, no el dispositivo del cliente.
- Solo una persona autenticada y autorizada puede consultar los registros.
- El cliente no puede ver registros de otras personas.
- El registro enviado no se modifica desde la aplicación. Si hay un error, Pilar conserva el original y solicita un nuevo envío; cualquier corrección administrativa debe definirse antes de implementarse.
- El consentimiento debe guardar la versión exacta del texto que el cliente aceptó.
- El QR abre el mismo formulario público que el enlace directo.

## 6. Herramientas para empezar

Usaremos software de código abierto y mantendremos la aplicación independiente de un proveedor de alojamiento:

| Necesidad | Herramienta inicial |
|---|---|
| Interfaz web | Next.js, React y TypeScript |
| API/backend | Nest.js y TypeScript |
| Estilos | Tailwind CSS |
| Base de datos | PostgreSQL local con Docker |
| Acceso a datos y migraciones | Prisma |
| Autenticación | Sesión protegida implementada en la API Nest.js |
| Validación de formularios | Zod |
| Firma en pantalla | `signature_pad` |
| Código QR | `qrcode` |
| Pruebas | Vitest para lógica y Playwright para el flujo web |
| Control de cambios | Git; el repositorio remoto actual está en GitHub |

Next.js, Nest.js, React, TypeScript, Tailwind CSS, PostgreSQL, Prisma, Zod, `signature_pad`, `qrcode`, Vitest, Playwright y Git son proyectos de código abierto. GitHub es el servicio de alojamiento del repositorio actual, no una herramienta de código abierto ni una dependencia de la aplicación. Que el software sea abierto no significa que cualquier servicio alojado, plan o despliegue sea gratuito o abierto. Para desarrollo, PostgreSQL se ejecutará localmente con Docker. Antes de publicar la aplicación se elegirá el alojamiento, comparando costo, mantenimiento, copias de seguridad y privacidad.

## 7. Organización técnica

- Mantener un repositorio único con dos aplicaciones: `web` (Next.js) y `api` (Nest.js), más PostgreSQL. No crear microservicios.
- Next.js se comunica con Nest.js; el navegador no se conecta directamente a PostgreSQL.
- Separar páginas, componentes de interfaz, módulos de negocio y acceso a datos para no mezclar responsabilidades.
- Validar los datos en la API aunque también se validen en el formulario.
- Guardar el consentimiento, la firma y su fecha de recepción como un solo envío; un fallo no debe dejar un registro incompleto.
- Mantener las claves y contraseñas fuera del repositorio, en variables de entorno.
- Aplicar controles de acceso en la API y en la base de datos. Ocultar una pantalla en la interfaz no es suficiente para proteger los registros.
- Cada envío del MVP se asocia a una mascota y a una visita; si el cliente trae varias, firma un envío por cada una.
- El responsable puede tener rol `CLIENTE` sin cuenta de acceso; Pilar usa rol `ADMIN` para consultar la evidencia.

## 8. Orden de trabajo

1. **Preparar el proyecto:** configurar Next.js, Nest.js, TypeScript, Tailwind, pruebas y PostgreSQL local con Prisma.
2. **Construir el formulario:** campos del cliente y mascota, lectura del texto y firma desde celular y computador.
3. **Guardar y confirmar:** validar en el servidor, guardar el envío completo con fecha y hora y mostrar confirmación.
4. **Construir la consulta de Pilar:** inicio de sesión, lista y detalle protegidos.
5. **Probar y preparar el piloto:** probar el QR y el flujo completo en dispositivos reales; elegir alojamiento y configurar copias de seguridad.

## 9. Cómo sabremos que el MVP está listo

- Un cliente puede completar y enviar el formulario desde un celular y desde una tablet.
- El sistema rechaza campos obligatorios vacíos, aceptación ausente y firma vacía.
- Tras enviar, aparece una confirmación y el registro completo aparece en la lista de Pilar.
- La vista de detalle muestra el texto aceptado, la firma y la fecha y hora asignadas por el servidor.
- Una persona sin sesión no puede acceder a la lista ni al detalle, incluso si conoce la dirección de la página.
- El QR abre el formulario sin requerir una aplicación especial.
- Las pruebas cubren validación, envío exitoso, envío rechazado y acceso no autorizado.

La arquitectura detallada, las rutas previstas y el modelo inicial están en [FICHA-TECNICA.md](./FICHA-TECNICA.md).

## 10. Pendiente antes de usarlo con clientes

- Confirmar el país y las normas de privacidad y firma aplicables.
- Obtener revisión legal del texto del consentimiento y del modo de aceptación.
- Definir qué datos son obligatorios, cuánto tiempo se conservan y cómo se atienden solicitudes de corrección o eliminación.
- Elegir el alojamiento de producción y verificar quién puede acceder a los datos, dónde se almacenan y cómo se respaldan.

Hasta resolver estos puntos, el sistema es un prototipo de desarrollo. Un consentimiento guardado constituye evidencia del proceso, pero no debe presentarse como garantía de validez legal ni como exoneración automática de responsabilidad.
