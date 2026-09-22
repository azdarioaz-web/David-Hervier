# Módulo Equipamientos

## Estado

Módulo implementado sobre PostgreSQL + n8n.

Carpeta n8n:

`10 - Equipamientos`

Workflows:

- `10 - Equipamientos - API`
  - Workflow ID: `dL8brnLgiZwL8bBB`
- `10 - Equipamientos - Vista`
  - Workflow ID: `2UYNIvcCP2BRlqYO`

La vista y el API están protegidos mediante Cookie Guard.

## Modelo conceptual

Se separan tres niveles:

1. **Categoría**
2. **Modelo / tipo comercial**
3. **Unidad física alquilable**

Esto permite tener múltiples equipos físicos iguales sin duplicar marca, modelo y datos comerciales.

### Identificadores de una unidad física

Cada unidad utiliza:

- UUID interno como PK técnica;
- `alias` obligatorio y único;
- `numero_serie` obligatorio y único.

Alias y número de serie se almacenan como `citext`, por lo que la unicidad no distingue mayúsculas/minúsculas.

El antiguo `codigo_interno` fue eliminado.

## Tablas

### `dh_equipos.categorias_equipos`

Categorías de equipamiento.

Categoría inicial cargada:

- Estética

### `dh_equipos.tipos_equipos`

Define un modelo/tipo comercial.

Campos principales:

- categoría;
- nombre;
- marca;
- modelo;
- descripción;
- tarifa diaria;
- valor de reposición;
- depósito/garantía;
- activo/inactivo;
- auditoría básica.

### `dh_equipos.unidades_equipos`

Cada aparato físico.

Campos principales:

- tipo/modelo;
- alias;
- número de serie;
- fecha de compra;
- costo de compra;
- proveedor;
- número de factura;
- garantía hasta;
- estado operativo;
- observaciones;
- activo/inactivo;
- auditoría básica.

Estados operativos actuales:

- `DISPONIBLE`
- `MANTENIMIENTO`
- `FUERA_DE_SERVICIO`

No se utiliza borrado físico; se trabaja con baja lógica para preservar historial futuro.

## GPS / Traccar

Tabla:

`dh_equipos.equipos_gps_asignaciones`

Objetivo: mantener historial de asignaciones entre equipos físicos y dispositivos Traccar.

Datos principales:

- unidad física;
- `traccar_device_id`;
- `traccar_uniqueid`;
- fecha/hora de asignación;
- fecha/hora de desasignación;
- observaciones;
- usuario que asignó/finalizó;
- estado activo.

Restricciones confirmadas:

- una unidad física puede tener un solo GPS activo;
- un dispositivo Traccar puede estar asignado activamente a una sola unidad;
- las asignaciones anteriores permanecen como historial.

Traccar sigue siendo la fuente de verdad del dispositivo y su ubicación. No se duplican posiciones dentro de `dh_equipos`.

La interfaz actual solicita manualmente `traccar_device_id` y `traccar_uniqueid`. Queda pendiente reemplazarlo por una selección directa de dispositivos disponibles desde Traccar.

## Interfaz

Ruta:

`/webhook/dh-equipos-equipamientos`

API:

`/webhook/dh-equipos-equipamientos-api`

Pestañas actuales:

- Equipos
- Modelos
- Categorías

Desde Equipos se puede:

- alta/edición de unidad física;
- administrar alias y número de serie;
- cargar datos de compra;
- cambiar estado;
- activar/desactivar;
- asignar/desasignar GPS.

## Reglas futuras relacionadas

Los alquileres serán por días completos.

La disponibilidad temporal no se almacena como estado fijo de la unidad: se calculará posteriormente a partir de Alquileres/Agenda.
