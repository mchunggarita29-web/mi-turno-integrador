# Entrega 2 — Segundo Avance: Aplicación Web (Frontend)

**Curso**: Proyecto Integrador 1 (SOFT-11-C1) — CENFOTEC, C3 2026
**Evaluación**: Segundo Avance – Aplicación Web (10% de la nota final)
**Repo real (código)**: https://github.com/mchunggarita29-web/mi-turno

## Nota de arquitectura — misma autorización que el backend (Entrega 3)

La consigna pide HTML5 + **Bootstrap** + CSS3 propio. El proyecto real, **Mi Turno**, ya
tiene todo el frontend construido sobre **Next.js 16 (App Router) + Tailwind v4 + un set
propio de componentes UI** (sin Bootstrap) — ver `entrega-3-backend/AVANCE.md` para la nota
de autorización original del profesor Mario Alberto Jiménez Espinoza, que cubre la
arquitectura completa del proyecto real (no solo el backend). Este documento mapea cada
criterio de la rúbrica (pensada en términos de Bootstrap) contra su equivalente real en
Tailwind/CSS propio, con evidencia puntual de archivo y línea.

## Rúbrica (100 pts) → evidencia

### A. Estructura y Semántica HTML5 (20 pts)

| Requisito | Evidencia |
|---|---|
| Etiquetas semánticas (`header`, `nav`, `main`, `section`, `article`, `footer`) | `src/components/layout/app-nav.tsx`: `<aside>` (nav lateral desktop, línea 62) + `<nav>` (línea 70 y línea 148, nav inferior móvil). `src/app/(app)/layout.tsx`: `<main>` (línea 15). `src/app/(app)/calendario/page.tsx`: `<section>` para "Eventos del mes" y "Ausencias" (líneas 202, 220). 24 usos de `<section>` en todo `src/app/`. |
| Formularios completos con tipos de `input` correctos (`email`, `number`, `date`, `select`, etc.) | `src/app/(auth)/registro/registro-form.tsx:16` `type="email"`. `src/app/(app)/calendario/agregar.tsx:92,95,99,130,139` `type="date"`/`type="time"`. `src/app/(app)/disponibilidad/persona-card.tsx:141-142` `type="time"`. `src/app/(app)/asignaciones/historial-view.tsx:101,104` `type="date"` (filtros). `src/app/(app)/catalogo/chore-form.tsx` — formulario completo de creación/edición con `<Select>` (`categoria`, `slot`, `modo`, `personas_requeridas`, `responsable_fijo_id`) y `type="time"`/`type="checkbox"`. |
| Nombres/IDs de campos coinciden con los modelos de datos de la API | `chore-form.tsx` usa exactamente los nombres de columna de la tabla `chores` (ver §D): `nombre`, `categoria`, `slot`, `hora_sugerida`, `flexible`, `recurrencia_tipo`, `recurrencia_dias`, `modo`, `personas_requeridas`, `responsable_fijo_id` — mismo `name=` que la columna Postgres, para que el `FormData` del Server Action se mapee 1:1 sin traducción de nombres. |
| Accesibilidad básica (`alt`, jerarquía de encabezados) | `alt=` en imágenes de tareas: `src/app/(app)/task-row.tsx:234`, `src/app/(app)/calendario/[fecha]/page.tsx:271`. `aria-label` en botones icon-only: `borrar-btn.tsx`, `modal.tsx` (`aria-labelledby`). Jerarquía `h1`→`h2` consistente (`catalogo/page.tsx:27` `h1`, `calendario/page.tsx:203` `h2` por sección). |

### B. Uso Eficiente de "Bootstrap" → equivalente real en Tailwind (25 pts)

| Componente Bootstrap pedido | Equivalente real |
|---|---|
| **Grid system** (`col-sm-*`, `col-md-*`, `col-lg-*`) | Grid/flex responsive de Tailwind con los mismos 3 breakpoints (`sm:`/`md:`/`lg:`) — 19 usos en `src/app/`. Ejemplo: `app-nav.tsx:62` (`hidden ... md:flex`, sidebar solo desktop) + nav inferior `md:hidden` (línea 148) — mismo patrón "grid que se adapta" que pide la rúbrica, implementado con utilidades en vez de clases `col-*`. |
| **Navbar** con enlaces funcionales entre pantallas | `src/components/layout/app-nav.tsx` — nav lateral fijo en desktop (`PRIMARY`+`SECONDARY`, línea 25-39) y barra inferior + panel "Más" en móvil, con estado activo animado (`layoutId="nav-pill-*"`, Framer Motion). |
| **Cards** o tablas para listas de datos | `src/components/ui/field.tsx` — componente `Card` reutilizado en `catalogo/page.tsx`, `calendario/page.tsx`, `chore-form.tsx`, etc. |
| **Modals** para confirmaciones, detalles o formularios flotantes | **Agregado en este avance**: `src/components/ui/modal.tsx` — `ConfirmModal` sobre `<dialog>` nativo (top-layer + `::backdrop`, sin dependencias nuevas), aplicado a `src/app/(app)/calendario/borrar-btn.tsx` (antes borraba sin ninguna confirmación) y sus dos usos en `calendario/page.tsx` (borrar evento / borrar ausencia). Verificado con `npx tsc --noEmit`, `npx eslint` y `npx next build` — sin errores. |
| **Buttons/Badges** con estados de color consistentes | `src/components/ui/button.tsx` — 6 variantes (`primary/secondary/outline/ghost/destructive/success`). `src/components/ui/field.tsx` — `Badge` con 5 tonos (`muted/primary/success/warning/destructive`), usado en `task-row.tsx`, `historial-view.tsx`, `usuario-card.tsx`, `chore-list-item.tsx`. |

### C. Diseño Adaptativo (Responsive) y CSS (20 pts)

| Requisito | Evidencia |
|---|---|
| Visualización fluida móvil/escritorio | Layout de dos columnas en desktop (sidebar fija `md:pl-64`, `layout.tsx:16`) que colapsa a nav inferior + contenido a ancho completo en móvil (`pb-32 md:pb-0`). 19 breakpoints `sm:`/`md:`/`lg:` repartidos en formularios, grids de 2 columnas (`chore-form.tsx:42`) y navegación. |
| Hoja de estilos propia | `src/app/globals.css` — sistema completo de tokens de diseño en HSL (`--background`, `--primary`, `--destructive`, etc.) para modo claro (`:root`) y oscuro (`.dark`), consumidos vía `@theme inline` de Tailwind v4. Toggle de tema persistido (`next-themes`, `src/components/theme-provider.tsx`). |
| Identidad visual: paleta, tipografía, espaciados, efectos (`hover`, transiciones) | Paleta cálida (tonos naranja/terracota, `--primary: 16 62% 47%`) consistente en claro/oscuro. Tipografía `Inter` con fallback a SF Pro (`src/app/layout.tsx:9-14`, con comentario explicando la decisión). `hover:bg-secondary`, `transition-colors` en `button.tsx:37`, `app-nav.tsx` (enlaces de nav). Microinteracciones con Framer Motion: pill animada de nav activo, `whileTap`/`whileHover` en botones (ver `docs/plan-arquitectura.md` §0, fila "Motion"). |

### D. Alineación con HU/Jira y Coincidencia con la API (25 pts)

El tablero de Jira formal es objeto del **Tercer avance** (`entrega-4-*`/Bitácora, aún no
iniciado). Mientras tanto, el equipo ya documentó las Historias de Usuario divididas en
los mismos 10 módulos que son hoy la navegación real de la app
(`entrega-1-analisis-diseno/Historias de Usuario - Segundo Avance (Mi Turno).xlsx`, hojas:
*01 Autenticación, 02 Hoy/Dashboard, 03 Calendario, 04 Catálogo de Tareas,
05 Cambios/Intercambios, 06 Historial, 07 Disponibilidad, 08 Notificaciones, 09 Ajustes,
10 Usuarios Admin*) — estos 10 módulos serán las épicas del tablero de Jira en el próximo
avance.

| Módulo (futura épica Jira) | Vista(s) | Tabla(s) Supabase | Ruta API (avance 1) |
|---|---|---|---|
| 01 Autenticación | `(auth)/login`, `(auth)/registro` | `profiles`, `households` | `POST /api/registro` |
| 02 Hoy / Dashboard | `(app)/page.tsx`, `dashboard-view.tsx` | `assignments`, `chores`, `notifications` | `GET /api/dashboard` |
| 03 Calendario | `calendario/page.tsx`, `calendario/[fecha]/page.tsx`, `calendario/agregar.tsx` | `events`, `absences`, `assignments` | `GET /api/assignments` |
| 04 Catálogo de Tareas | `catalogo/page.tsx`, `catalogo/nueva`, `catalogo/[choreId]`, `chore-form.tsx` | `chores`, `rotations` | `GET /api/chores` |
| 05 Cambios / Intercambios | `intercambios/page.tsx`, `intercambios-view.tsx` | `swaps`, `notifications` | `PATCH /api/assignments/:id` (origen del swap) |
| 06 Historial | `asignaciones/page.tsx`, `historial-view.tsx` | `assignments` | `GET /api/assignments?...` (filtros) |
| 07 Disponibilidad | `disponibilidad/page.tsx`, `persona-card.tsx` | `availability_windows`, `days_off`, `absences` | — (Server Actions, RLS directo) |
| 08 Notificaciones | `notificaciones/page.tsx`, `notif-list.tsx` | `notifications` | `GET /api/notifications` |
| 09 Ajustes | `ajustes/page.tsx`, `ajustes-client.tsx` | `households`, `profiles` | — (Server Actions) |
| 10 Usuarios Admin | `usuarios/page.tsx`, `usuario-card.tsx` | `profiles`, `invites` | — (Server Actions, `requireAdmin()`) |

Los campos de cada formulario usan el mismo nombre de columna que su tabla (ver `chores` en
§A); esto es lo que la rúbrica pide como "mapeo de campos para el backend" — ya está resuelto
porque frontend y backend comparten el mismo repo y los mismos tipos (`src/types/db.ts`,
generado por Supabase CLI desde el esquema real).

### E. Orden y Estructura del Código (10 pts)

Estructura modular ya documentada en `docs/plan-arquitectura.md` §1.1: `src/{app,components,server,lib,types}`,
con `components/ui/` para primitivas reusables (incluye el `modal.tsx` nuevo de este avance),
`components/layout/` para navegación, y una página por ruta siguiendo el App Router. Código
verificado sin errores de TypeScript ni ESLint (`npx tsc --noEmit`, `npx eslint`, `npx next build`
— los tres limpios tras los cambios de este avance).

## Qué se agregó específicamente para este avance

El resto de la app (todas las vistas de los 10 módulos) ya existía — fue construida de una
vez como parte de la app real en producción (ver `docs/plan-arquitectura.md`). Para que la
rúbrica quedara cubierta al 100% (en particular el criterio de **Modals**, que no tenía
ningún equivalente real: las confirmaciones destructivas eran paneles inline, y borrar un
evento/ausencia del calendario no pedía ninguna confirmación), se agregó:

- `src/components/ui/modal.tsx` — `ConfirmModal` genérico sobre `<dialog>` nativo, sin
  dependencias nuevas (no se instaló Radix ni ningún paquete adicional).
- `src/app/(app)/calendario/borrar-btn.tsx` — ahora pide confirmación antes de borrar
  (antes borraba directo al primer click).
- `src/app/(app)/calendario/page.tsx` — pasa título/descripción contextual al modal en los
  dos usos (borrar evento, borrar ausencia).

Verificado: `npx tsc --noEmit` (sin errores), `npx eslint` sobre los archivos tocados (sin
warnings), `npx next build` (compila, genera las 26 rutas sin fallos).

## Pendiente — requiere al equipo, no solo a este repo

El punto 3 de "Entregables Requeridos" de la consigna pide **capturas de pantalla de las
pantallas en modo escritorio y móvil**. No se generaron en este avance: las vistas están
detrás de login real de la app en producción (datos de una familia real), y no corresponde
que un asistente de IA inicie sesión con credenciales de ningún integrante del equipo.
Alguien del equipo con sesión activa debe tomar esas capturas (desktop + ancho móvil, se
puede usar el inspector del navegador) de al menos: Hoy, Calendario, Catálogo, Ajustes.

## Entregables de esta evaluación

1. **Código fuente**: https://github.com/mchunggarita29-web/mi-turno (incluye el `ConfirmModal`
   agregado en este avance).
2. **Este documento** (`entrega-2-frontend/AVANCE.md`): mapeo completo rúbrica → evidencia.
3. **Pendiente del equipo**: capturas de pantalla desktop/móvil (ver sección anterior).
