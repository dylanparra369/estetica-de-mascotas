# Specs técnicos

**Proyecto:** Consentimiento Informado Digital para Mascotas

**Estado:** borrador para validación

## 0. Cómo usar este documento

Este documento complementa a `FICHA-TECNICA.md`. **No repite** lo que ya está allí: alcance MoSCoW (§4), requisitos RF-01 a RF-09 (§7), requisitos no funcionales (§8), stack base (§11), entidades (§12), roles (§13) y criterios de MVP terminado (§15). Aquí solo se agrega lo que la ficha no define. Las referencias `§N` apuntan a la ficha.

**Vacío:** solo se analizó la ficha. No se recibieron la propuesta, la presentación del MVP ni el `README.md`. Si contradicen algo de aquí, hay que ajustar.

---

## 1. Propuesta para las decisiones pendientes (§14)

Son recomendaciones, no decisiones tomadas. Cada una debe confirmarse con Pilar y reflejarse en la ficha.

| # | Pendiente de la ficha | Propuesta más simple |
|---|---|---|
| 1 | Firma por servicio o una vez por mascota | Una firma por visita. Es la más defendible y no exige reconocer clientes recurrentes. |
| 2 | Quién redacta y valida el texto | Un abogado o asesor lo valida antes del piloto. Es bloqueante. |
| 3 | País y normativa | Sin definir. Se asume Colombia (Ley 527 de 1999, Ley 1581 de 2012). **Suposición a confirmar.** |
| 4 | Datos exactos a pedir | Los campos de §12, todos obligatorios salvo correo y raza. |
| 5 | Identificar al mismo dueño o mascota | Dueño por número de documento. Mascota asociada al dueño y comparada por nombre. |
| 6 | Menores o terceros | Solo firman mayores de edad, con casilla: "Soy mayor de edad y responsable de la mascota". |
| 7 | ¿Los Should en el primer uso? | No. Quedan en v2, coherente con §10 y §15. |
| 8 | Conservación | Sin borrar nada hasta que la asesoría legal defina un plazo. |
| 9 | Corregir un error tras firmar | El registro no se edita. Se crea un nuevo consentimiento y el original queda intacto. |

**Observación:** RF-06 (consultar cualquier consentimiento) y RF-07 (historial por mascota) se solapan. En el MVP, RF-06 se cubre con una lista cronológica.

---

## 2. Ajustes al stack (§11)

El stack de la ficha se mantiene. Solo se concretan estas decisiones:

| Tema | Decisión | Justificación |
|---|---|---|
| Autenticación | Supabase Auth (de las dos opciones de §11) | Un solo proveedor para datos y login. |
| Estilos | Tailwind CSS (**adición propia**) | Diseño responsivo sin escribir CSS desde cero. |
| Backend | Server Actions y Route Handlers de Next.js | Sin servidor aparte. |
| Algoritmo de hash | SHA-256 (**suposición**, la ficha no lo especifica) | Estándar, disponible en Node sin dependencias. |
| QR | Librería `qrcode` | Cumple "sin dependencias externas". |

**Costo:** $0 en planes gratuitos, más dominio opcional. Verificar antes del piloto las condiciones vigentes de Supabase y Vercel (por ejemplo, pausa de proyectos inactivos).

---

## 3. Arquitectura

**Estilo:** monolito web. Una aplicación Next.js en Vercel conectada a Supabase. Sin microservicios ni API pública separada.

```
Cliente (celular/tablet)          Pilar (computador)
        │                                 │
        └──────────► Next.js (Vercel) ◄───┘
                     ├─ Páginas (UI)
                     └─ Lógica de servidor
                              │
                              ▼
                     Supabase (PostgreSQL + Auth)
```

### Decisiones de arquitectura

1. **El navegador nunca escribe directo en la base de datos.** Todo pasa por el servidor, que valida con Zod y guarda.
2. **El servidor genera fecha, hora y hashes.** El cliente no puede enviarlos.
3. **La inmutabilidad se impone en la base de datos**, no solo en la interfaz: las tablas de consentimientos y firmas no permiten actualizar ni borrar.
4. **QR estático.** Apunta a una URL fija (`/consentimiento`). Se genera una vez y se imprime.
5. **Dos hashes:** uno sobre el contenido del consentimiento y otro sobre la imagen de la firma.
6. **Versiones del texto inmutables.** Un cambio crea una versión nueva; cada consentimiento referencia la versión exacta que vio el cliente.
7. **Guardado atómico.** Dueño, mascotas, consentimiento y firma se guardan en una sola operación. Si algo falla, no queda nada parcial.
8. **El texto legal se carga por script o panel de Supabase.** No hay pantalla de administración del texto en el MVP.

---

## 4. Rutas

| Ruta | Acceso | Función |
|---|---|---|
| `/consentimiento` | Público (destino del QR) | Formulario del cliente |
| `/consentimiento/confirmacion` | Público | Confirmación de envío |
| `/admin/login` | Público | Inicio de sesión de Pilar |
| `/admin/consentimientos` | Solo Pilar | Lista cronológica |
| `/admin/consentimientos/[id]` | Solo Pilar | Detalle de la evidencia |
| `/admin/qr` | Solo Pilar | Ver o descargar el QR |

## 5. Estructura de carpetas (orientativa)

```
/
├─ app/
│  ├─ consentimiento/
│  └─ admin/
├─ components/
├─ lib/            (validaciones, hashes, acceso a datos)
├─ supabase/       (migraciones y reglas de seguridad)
├─ tests/          (unitarias y e2e)
├─ docs/           (ficha y specs)
└─ .env.local      (secretos, fuera de Git)
```

---

## 6. Criterios de aceptación por requisito

| ID | Criterio verificable |
|---|---|
| RF-01 | Los campos obligatorios se validan antes del envío. No se avanza con datos incompletos. Se permiten una o más mascotas. |
| RF-02 | La casilla de aceptación y la firma se habilitan solo después de mostrar el texto completo. |
| RF-03 | La firma se dibuja con dedo o mouse. No se acepta en blanco. |
| RF-04 | La marca la asigna el servidor. Ningún campo del formulario permite enviarla o modificarla. |
| RF-05 | Al escanear el QR con la cámara estándar se abre el formulario, sin apps adicionales. |
| RF-06 | Con sesión, Pilar ve lista y detalle. Sin sesión, se redirige al login y no se expone ningún dato. |

### Requisitos complementarios (derivados de la ficha, no nuevos)

- **RF-10.** Mostrar confirmación al cliente tras un envío exitoso (flujo §6).
- **RF-11.** Autenticar a Pilar con correo y contraseña (flujo §6).
- **RF-12.** Guardado atómico, sin registros parciales (decisión de arquitectura 7).

---

## 7. Herramientas y dependencias

**Entorno:** Node.js (LTS) y npm · Git y GitHub · editor con asistente de IA · cuentas gratuitas de GitHub, Vercel y Supabase · un celular y una tablet reales para pruebas (exigido en §15).

**Paquetes:**

| Propósito | Paquete |
|---|---|
| Framework | `next`, `react`, `react-dom`, `typescript` |
| Estilos | `tailwindcss` |
| Base de datos y auth | `@supabase/supabase-js`, `@supabase/ssr` |
| Firma | `signature_pad` |
| Validación | `zod` |
| QR | `qrcode` |
| PDF (v2) | `pdf-lib` |
| Pruebas | `vitest`, `@playwright/test` |

---

## 8. Riesgos

| Riesgo | Mitigación |
|---|---|
| Texto legal sin validar | Asesoría legal antes del piloto (bloqueante). |
| Normativa sin definir | Confirmar país y normas aplicables. |
| Límites del plan gratuito | Revisar condiciones y definir un respaldo propio antes del piloto. |
| Envíos falsos o spam en el formulario público | Validación estricta en servidor. Si aparece el problema, agregar un control antiabuso simple (recomendación, no está en la ficha). |
| Equipo principiante | Mantener el alcance en los Must y no adelantar funcionalidades. |
| Validez legal del no repudio | La evidencia técnica fortalece la prueba, pero su validez depende de la normativa y del texto validado. |