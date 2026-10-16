# Consentimiento Informado Digital para Mascotas

Aplicación web para que los clientes de una estética de mascotas lean y firmen digitalmente el consentimiento informado, generando evidencia consultable que protege al negocio y a los propietarios ante reclamaciones.

**Caso de estudio:** Estética de Mascotas de Pilar  
**Autores:** Dylan Felipe Parra, Maikol Stiven Osorio  
**Año:** 2026

## Estado del proyecto

En fase de análisis y definición de requisitos. El desarrollo aún no ha comenzado.

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

## Stack tecnológico propuesto

Propuesta pendiente de validación por el equipo.

- Next.js con TypeScript
- PostgreSQL con Supabase
- Autenticación: Supabase Auth o Auth.js
- Firma: signature_pad
- Validación: Zod
- Pruebas: Vitest y Playwright
- Despliegue: Vercel y Supabase

## Documentación

- [Ficha técnica](https://claude.ai/chat/docs/ficha-tecnica.md)

## Instalación y ejecución

Pendiente. Se completará cuando se inicie el desarrollo.

## Licencia

Por definir.
