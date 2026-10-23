# Entrega 3 — Primer Avance: Aplicación Web (Backend)

**Curso**: Proyecto Integrador 1 (SOFT-11-C1) — CENFOTEC, C3 2026
**Evaluación**: Primer Avance – Aplicación Web (10% de la nota final)
**Repo real (código)**: https://github.com/mchunggarita29-web/mi-turno

## Nota de arquitectura — autorizado por el profesor

La consigna pide Node.js + Express.js + Mongoose/MongoDB. El proyecto real, **Mi Turno**,
ya está en producción desde antes de este curso para una familia de 5 personas, construido
sobre **Next.js 16 (Route Handlers + Server Actions) + Supabase/Postgres**. El profesor
Mario Alberto Jiménez Espinoza autorizó resolver este avance con la arquitectura real de la
app en vez de levantar un backend Express/Mongoose aparte, con la condición de **presentar
todo** en la exposición del avance.

Este documento mapea cada criterio de la rúbrica contra la implementación real.

## Rúbrica (100 pts) → evidencia

| Criterio | Pts | Dónde está |
|---|---|---|
| **Arquitectura y Configuración** | 15 | `package.json` (deps: `next`, `@supabase/ssr`, `@supabase/supabase-js`, `zod`…), estructura modular `src/{app,server,lib,types}`, `.env.example` con todas las variables documentadas. Ver tabla de equivalencias en el README del repo real. |
| **Modelado con Mongoose** → modelado con Postgres/Supabase | 25 | `supabase/migrations/0001_init.sql` en adelante: tablas `chores`, `assignments`, `profiles`, `households`, etc., con *constraints*, enums, FKs, índices. Validación adicional con Zod (`src/lib/validation/chore.ts`) en cliente y servidor. RLS por `household_id` en todas las tablas (`docs/plan-arquitectura.md` §3). |
| **Lógica de Negocio y Endpoints (CRUD)** | 35 | `src/app/api/chores/route.ts` (GET lista + POST crear) y `src/app/api/chores/[choreId]/route.ts` (GET uno, PATCH actualizar, DELETE = baja lógica) — CRUD completo sobre la entidad principal del catálogo. Más `src/app/api/assignments/*` (GET historial filtrable, PATCH cambiar estado). |
| **Manejo de Respuestas y Errores** | 15 | Envelope único `{ ok, data }` / `{ ok:false, error:{code,message} }` (`src/lib/api/envelope.ts`) con códigos HTTP correctos (401/403/404/422/409/500) y `try/catch` en cada handler. |
| **Pruebas y Documentación (README/Postman)** | 10 | `postman/mi-turno-api.postman_collection.json` en el repo real (CRUD completo + 2 casos de error). README del repo real: instalación, variables de entorno, tabla completa de rutas con método/body/query. |

## Qué se agregó específicamente para este avance

El resto de la app (dashboard, calendario, intercambios, notificaciones, cron de
WhatsApp, etc.) ya existía. Para que el avance tuviera **CRUD REST completo y demostrable
con Postman** sobre una entidad principal, se agregó:

- `src/app/api/chores/route.ts` — `GET` (listado con filtros `categoria`/`modo`/`activo`/`q`)
  y `POST` (crear, valida con Zod).
- `src/app/api/chores/[choreId]/route.ts` — `GET` (uno), `PATCH` (actualización parcial),
  `DELETE` (inactivación lógica — justificado en el README: `chores` tiene historial real de
  `assignments` de la familia encadenado por FK).
- `src/lib/validation/chore.ts` — se expuso `choreBaseSchema`/`choreUpdateSchema` para
  reusar la validación entre el formulario (Server Action) y el body JSON de la API.
- `src/server/services/chores.ts` — se extrajo el mapeo `toRow()` (antes vivía dentro de la
  Server Action) para que lo use tanto la action como la API REST sin duplicar lógica.
- `postman/mi-turno-api.postman_collection.json` — colección ejecutable.
- `README.md` del repo real — reescrito con instalación real, tabla de rutas y la nota de
  arquitectura de esta sección.

Verificado: `npx next build` (compila, genera las rutas), `npx tsc --noEmit` (sin errores),
`npx eslint` sobre los archivos tocados (sin warnings). Las rutas nuevas siguen exactamente
el mismo patrón que `/api/assignments` (ya en producción desde antes), así que heredan su
comportamiento probado: sin sesión, el middleware (`src/proxy.ts`) redirige a `/login` antes
de llegar al handler — para probar con Postman hay que copiar la cookie de sesión de un
navegador ya logueado (instrucciones en la descripción de la colección).

## Entregables de esta evaluación

1. **Código fuente**: https://github.com/mchunggarita29-web/mi-turno
2. **README.md**: en la raíz del repo real (instalación + variables de entorno + rutas).
3. **Colección Postman**: `postman/mi-turno-api.postman_collection.json` en el repo real.
