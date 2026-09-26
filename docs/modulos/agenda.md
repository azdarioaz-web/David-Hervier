# Módulo Agenda

## Propósito

Gestiona la disponibilidad diaria de los equipos, reservas, bloqueos, aprobaciones y vistas de calendario del sistema DH Equipos Estéticos.

## Runtime n8n

Carpeta:

- `David Hervier / DH Equipos esteticos / 20 - Agenda`

Workflows principales:

- `20 - Agenda - Vista` — ID `fTtBUaMUtty6NvnC`
- `20 - Agenda - API` — ID `pGkGaHUtsimlm8BY`

La vista se publica en caliente; no requiere reiniciar n8n.

## Aprobaciones pendientes

Para usuarios OWNER, la parte superior de Agenda muestra las reservas y cancelaciones pendientes de aprobación.

Al seleccionar una aprobación:

- se activa el foco sobre el equipo o los equipos involucrados;
- la Agenda se desplaza automáticamente hasta la fecha correspondiente;
- se muestran las acciones disponibles para esa reserva.

### Comportamiento de los botones al desplazarse

Confirmado el 2026-09-26:

- los botones permanecen inicialmente en su posición natural dentro del detalle;
- al desplazarse la página, se mueven junto con el contenido;
- cuando su ubicación natural alcanza el borde superior, pasan a modo flotante a 8 px del borde;
- permanecen visibles mientras la ubicación original queda por encima de la pantalla;
- al volver hacia arriba y reaparecer su ubicación real, dejan de ser flotantes y regresan al flujo normal;
- el mismo comportamiento funciona cuando el desplazamiento es producido automáticamente al seleccionar una aprobación pendiente.

La implementación usa un ancla en la posición real de las acciones y resincroniza el estado durante `scroll`, `scrollend` y cambios de tamaño.

## Acciones según estado de la reserva

La interfaz y el backend respetan una única etapa operativa por reserva.

### `PENDIENTE_APROBACION`

Acciones de Owner:

- **Confirmar**
- **Rechazar**

No se muestra **Cancelar reserva**.

`rechazar_reserva` cambia la reserva a `RECHAZADA`, libera los equipos de la fecha y registra el rechazo de una solicitud que nunca llegó a aprobarse.

### `CONFIRMADA`

Acción de Owner:

- **Cancelar reserva**

No se muestran **Confirmar** ni **Rechazar**.

`cancelar_reserva` sólo está permitida en backend cuando la reserva está en `CONFIRMADA`. El estado final es `CANCELADA_OWNER`, se libera la fecha y se registra una cancelación realizada por el Owner.

### `CANCELACION_PENDIENTE`

Acciones de Owner:

- **Aprobar cancelación**
- **Rechazar cancelación**

No se muestra **Cancelar reserva**, porque la reserva ya se encuentra dentro del proceso específico de cancelación solicitado por el cliente.

## Regla de backend

La función `dh_equipos.cambiar_estado_reserva` fue ajustada para que el estado destino `CANCELADA_OWNER` sólo sea válido cuando el estado actual es `CONFIRMADA`.

Esto impide que una llamada directa a la API cancele como Owner una reserva que todavía está `PENDIENTE_APROBACION` o que ya está en `CANCELACION_PENDIENTE`.

## Validación

El 2026-09-26 se verificó la versión publicada de `20 - Agenda - Vista` con los tres estados:

- `PENDIENTE_APROBACION`: Confirmar visible, Rechazar visible, Cancelar reserva oculto.
- `CONFIRMADA`: Confirmar oculto, Rechazar oculto, Cancelar reserva visible.
- `CANCELACION_PENDIENTE`: Aprobar cancelación visible, Rechazar cancelación visible, Cancelar reserva oculto.

También se verificó en PostgreSQL que `CANCELADA_OWNER` queda restringido a reservas `CONFIRMADA`.

La prueba de interfaz no produjo errores ni advertencias de consola.

El comportamiento flotante de los botones de aprobaciones había sido validado previamente en la Agenda real por Dario.
