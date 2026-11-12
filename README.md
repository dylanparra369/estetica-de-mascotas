# Consentimiento Informado Digital para Mascotas

Aplicación web para que los clientes de una estética de mascotas lean y firmen digitalmente el consentimiento informado, generando evidencia consultable que protege al negocio y a los propietarios ante reclamaciones.

**Caso de estudio:** Estética de Mascotas de Pilar  
**Autores:** Dylan Felipe Parra, Maikol Stiven Osorio  
**Año:** 2026

## Estado del proyecto

Preparación del MVP. El alcance y las herramientas iniciales están descritos en [SPECS.md](./SPECS.md); la implementación aún no ha comenzado.

## El problema

El negocio no cuenta con evidencia gestionable de que los clientes aceptan los riesgos del servicio antes de dejar a sus mascotas. Esto genera conflictos, pérdida de tiempo y desorden operativo ante cualquier reclamación.

## La solución

Una aplicación web responsiva donde:

1. El cliente accede por código QR o desde la tablet del negocio.
2. Registra sus datos y los de su mascota.
3. Lee el consentimiento completo.
4. Firma digitalmente.
5. El sistema guarda automáticamente fecha, hora y evidencia.
6. Pilar consulta cualquier consentimiento registrado.

## Usuarios

- **Pilar:** propietaria y administradora. Consulta los consentimientos.
- **Cliente:** dueño de la mascota. Completa y firma el formulario.

## Alcance del MVP

**Incluido (Must)**

- Registro de datos del dueño y la mascota
- Consentimiento informado completo
- Firma digital de aceptación
- Fecha, hora y evidencia guardadas
- Acceso por código QR

**Siguiente versión (Should)**

- Historial por mascota
- Búsqueda por dueño o mascota
- Exportación en PDF

**Futuro (Could)**

- Fotos del estado inicial
- Envío por WhatsApp o correo
- Condiciones médicas y alergias

**Fuera de alcance (Won't)**

- Agenda de citas
- Facturación o pagos
- Inventario y fidelización
- Múltiples sucursales

## Hoja de ruta

| Versión | Etapa | Alcance |
| --- | --- | --- |
| v1 | MVP | Web, QR, firma, almacenamiento |
| v2 | Gestión | Historial, búsqueda, PDF |
| v3 | Enriquecido | Fotos, WhatsApp, salud |
| v4 | Nativo | App Android |
| v5 | Escala | Agenda, clientes recurrentes |

## Herramientas previstas para el MVP

- Next.js con TypeScript para la interfaz web
- Nest.js con TypeScript para el backend/API
- PostgreSQL local durante el desarrollo y Prisma para el acceso a datos
- Firma: signature_pad
- Estilos: Tailwind CSS
- Validación: Zod
- Código QR: qrcode
- Pruebas: Vitest y Playwright

La arquitectura y el alcance están descritos en la [ficha técnica](./FICHA-TECNICA.md) y la [especificación del MVP](./SPECS.md). El alojamiento de producción está por decidir.

## Documentación

- [Ficha técnica](./FICHA-TECNICA.md)
- [Especificación del MVP](./SPECS.md)
- [Diagrama ER del MVP](./diagrama-er-mvp.html)

## Instalación y ejecución

Pendiente. Se completará al configurar el entorno de desarrollo descrito en [FICHA-TECNICA.md](./FICHA-TECNICA.md).

## Licencia

Por definir.
