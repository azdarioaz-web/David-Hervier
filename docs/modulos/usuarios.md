# Módulo Usuarios

## Estado

Módulo implementado y verificado para administrar usuarios internos y clientes.

Workflows n8n:

- `50 - Usuarios - Vista`
  - Workflow ID: `ywEy10Jc6EsgRt5n`
- `50 - Usuarios - API`
  - Workflow ID: `iXtYvLlijz40yLBZ`

Ambos permanecen activos. La vista exige sesión válida y acceso `OWNER`.

## Roles

Roles actuales:

- `OWNER`
- `OPERADOR`
- `CLIENTE`

Los usuarios `OWNER` y `OPERADOR` no se vinculan a un cliente.
Los usuarios `CLIENTE` se vinculan mediante `cliente_id` a `dh_equipos.clientes`.

## Datos principales

### `dh_equipos.users`

Campos principales:

- email único;
- contraseña almacenada como hash;
- nombre;
- WhatsApp;
- rol;
- cliente asociado cuando corresponde;
- recepción de notificaciones;
- estado activo/inactivo.

### `dh_equipos.clientes`

Campos principales:

- tipo de cliente;
- nombre / razón social;
- documento / CUIT;
- teléfono / WhatsApp;
- email;
- identificador visual;
- color;
- observaciones;
- estado activo/inactivo;
- excepción de aprobación de reserva;
- excepción de aprobación de cancelación.

### `dh_equipos.clientes_direcciones`

Cada cliente puede tener múltiples direcciones con:

- alias opcional;
- dirección;
- localidad;
- ubicación;
- dirección principal;
- observaciones;
- estado activo/inactivo.

## Edición de clientes con acceso al portal

El guardado coordinado se realiza mediante la función PostgreSQL:

`dh_equipos.guardar_cliente_usuario(...)`

Cuando un cliente ya tiene acceso al portal:

- cambiar su email actualiza el email del cliente y el de su usuario de acceso;
- dejar la contraseña vacía conserva la contraseña actual;
- ingresar una contraseña nueva reemplaza el hash almacenado;
- el email se valida contra duplicados antes de guardar.

Las opciones:

- `no_requiere_aprobacion_reserva`;
- `no_requiere_aprobacion_cancelacion`;

se guardan en una operación separada posterior al guardado principal del cliente. Esto evita que la misma fila de `dh_equipos.clientes` sea actualizada dos veces dentro de una única sentencia PostgreSQL.

## Corrección confirmada — 25/09/2026

Se corrigió el fallo que impedía editar el email de un cliente existente y mostraba en la interfaz:

`No se pudo guardar`

La causa confirmada era PostgreSQL:

`tuple to be updated was already modified by an operation triggered by the current command`

El workflow `50 - Usuarios - API` quedó publicado con el guardado principal y las preferencias de aprobación separados en nodos consecutivos.

La corrección fue verificada por el usuario desde la interfaz modificando el email de un cliente existente.

## Limpieza de usuarios y fichas — 09/10/2026

Por indicación del propietario se eliminaron los accesos con rol `CLIENTE` (2) y `OPERADOR` (1), junto con las 7 fichas comerciales de clientes y sus 7 direcciones. No había reservas ni ocupaciones vinculadas. Las sesiones de los tres accesos eliminados se invalidaron por la FK `web_sessions.user_id ON DELETE CASCADE`.

**Estado productivo verificado:** en `dh_equipos.users` permanecen exactamente dos cuentas activas, Dario y David Hervier, ambas con rol `OWNER`; `dh_equipos.clientes` y `dh_equipos.clientes_direcciones` quedaron vacías. Se conservó la categoría del proyecto, la configuración existente y los demás datos ajenos a Usuarios.

**Respaldo previo:** `dh_equipos._respaldo_usuarios_pre_limpieza_20261009` (19 registros JSONB: 5 usuarios, 7 clientes y 7 direcciones). Contiene hashes de contraseña y debe permanecer bajo acceso administrativo; no exportar a documentos ni registros públicos. La limpieza se ejecutó de forma atómica con validaciones de roles, IDs, cantidad de datos, integridad y estado final. Cualquier recuperación deberá evaluar los cambios posteriores antes de reinsertar registros.

La API actual `50 - Usuarios - API` implementa alta/edición y activación/desactivación, **no eliminación definitiva**; esta limpieza se aplicó directamente en PostgreSQL sin modificar los workflows.
