# Módulo Equipamientos

## Estado confirmado — 2026-10-09

**Un equipo físico es una sola entidad administrable**. No existe un catálogo de Modelos ni una tabla `tipos_equipos` ni una relación `tipo_equipo_id`: se eliminaron al unificar el alta de cada máquina. El campo **modelo** puede anotarse como atributo libre y opcional de cada equipo, junto a su marca.

Backend n8n:
- `10 - Equipamientos - API` (`dL8brnLgiZwL8bBB`), activo, 20 nodos.
- `10 - Equipamientos - Vista` (`2UYNIvcCP2BRlqYO`), activo.
- Vista: `/webhook/dh-equipos-equipamientos`.
- API: `/webhook/dh-equipos-equipamientos-api`.
- Protección de ambas rutas: Cookie Guard, rol OWNER.

## Modelo de datos

Base `enredados`, esquema `dh_equipos`:

- `categorias_equipos`: categorías de equipos; se conserva Estética.
- `unidades_equipos`: un registro por máquina, con PK `unidad_equipo_id`, FK directa `categoria_id`, `alias` (nombre comercial único y obligatorio), `marca`, `modelo`, `descripcion`, `valor_reposicion`, `numero_serie`, `tarifa_diaria`, datos de compra, estado operativo, observaciones, activo, campos de auditoría.
- `equipos_gps_asignaciones`: GPS de Traccar asociado por `unidad_equipo_id`, con histórico y unicidad de asignación activa.

El atributo SQL `alias` se presenta en la interfaz como **Nombre del equipo** y se mantiene como nombre canónico en Agenda, Portal Cliente y Reportes. `numero_serie` es opcional y único si se informa. `marca`, `modelo`, `descripcion` y `valor_reposicion` son optativos. El precio por día se registra por equipo, no por modelo.

Estados operativos: `DISPONIBLE`, `MANTENIMIENTO`, `FUERA_DE_SERVICIO`. La disponibilidad por fecha se calcula con Agenda/Reservas.

## Interfaz y operaciones

Dentro de **Artículos > Alquiler** sólo hay pestañas **Equipos** y **Categorías**. Un único formulario permite crear y editar equipo con categoría, nombre, marca, modelo, número de serie, reposición, tarifa, compra, garantía, estado y observaciones. La tabla conserva activar/desactivar, editar y asignar/desasignar GPS.

Las otras secciones de Artículos, **Consumibles** y **Accesorios**, permanecen independientes e intactas.

Acciones de API vigentes: `guardar_categoria`, `estado_categoria`, `guardar_unidad`, `estado_unidad`, `activo_unidad`, `asignar_gps`, `desasignar_gps`, `guardar_consumible`, `estado_consumible`, `guardar_accesorio`, `estado_accesorio`. Las acciones antiguas `guardar_tipo` y `estado_tipo` se retiraron.

Dependencias migradas para consultar el equipo directamente sin JOIN a Modelos:
- `20 - Agenda - Vista` (`fTtBUaMUtty6NvnC`);
- `60 - Portal Cliente - Vista` (`owpKynlm3io4eJHo`);
- `70 - Reportes - Vista` (`9yO0ROZ6rspZVtqj`).

## Limpieza de datos de demostración — 2026-10-09

Antes de modificar se generó un backup comprimido completo del esquema `dh_equipos` con `pg_dump`, en ServerDockers:

`/srv/backups/dh-equipos/dh_equipos_pre_unificacion_20261009_0928.dump`

Permisos 0600; validado mediante `pg_restore --list`; 18 tablas respaldadas. **No restaurar sobre producción sin estudiar las modificaciones posteriores.**

Dentro de una única transacción validada por recuentos se eliminaron:
- 39 reservas, 41 asociaciones de equipos, 76 eventos históricos;
- 39 ocupaciones de agenda, 31 notificaciones;
- 6 equipos y 6 modelos; las asignaciones GPS y consumibles por reserva ya estaban vacíos.

Se retiraron la columna FK `tipo_equipo_id` y la tabla `tipos_equipos`, sin usar `CASCADE`. Quedaron **0 alquileres y 0 equipos**; se conservaron **7 clientes, 5 usuarios, 1 categoría** y la configuración del sistema.

## Validación

- Publicación n8n en caliente, sin reinicios.
- SQL de lectura ejecutado satisfactoriamente para Equipamientos, Agenda, Portal Cliente y Reportes después de retirar la tabla Modelos.
- Alta y edición del equipo ejecutadas en transacción PostgreSQL con nombre/serie opcional; `ROLLBACK` verificado: 0 registros QA.
- Comprobado que ningún workflow **activo** de n8n referencia `tipos_equipos` o `tipo_equipo_id`; algunos workflows `TEMP` inactivos conservan referencias históricas y no deben reutilizarse sin adaptarlos.
- La revisión visual y el uso autenticado del nuevo formulario desde navegador siguen pendientes de confirmación operativa.
