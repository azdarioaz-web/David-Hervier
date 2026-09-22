# Seguridad y login

## Estado

Módulo de autenticación implementado y publicado.

### Workflows n8n

- `Loguin`
  - Workflow ID: `6rfrxFEZLTtrFQX2`
- `00 - Cookie Guard`
  - Workflow ID: `n5ctoJkR1AdgEUSN`

Carpeta:

`00 - Seguridad`

## Tablas PostgreSQL

### `dh_equipos.users`

Usuarios del sistema.

Datos principales:

- UUID interno;
- email único;
- hash de contraseña;
- activo/inactivo;
- fechas de creación/actualización;
- último login.

Las contraseñas se almacenan mediante hash bcrypt compatible con PostgreSQL `crypt()`.

### `dh_equipos.web_sessions`

Sesiones web persistentes.

Contiene:

- token de sesión;
- usuario;
- user-agent;
- IP;
- creación;
- última actividad;
- vencimiento;
- estado activo.

## Cookie

Nombre:

`dh_equipos_session`

Propiedades:

- `HttpOnly`;
- `Secure`;
- `SameSite=Lax`;
- duración inicial: 24 horas.

Cookie Guard valida además el user-agent.

## Rutas principales

- `/webhook/dh-equipos-login`
- `/webhook/dh-equipos-auth`
- `/webhook/dh-equipos-home`
- `/webhook/dh-equipos-logout`

`/home` requiere una sesión válida y redirige al login cuando no existe.
