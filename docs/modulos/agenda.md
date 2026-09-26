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

## Semántica de Rechazar y Cancelar reserva

La API usa `dh_equipos.cambiar_estado_reserva`.

### Rechazar

- acción API: `rechazar_reserva`;
- sólo está permitida cuando la reserva está en `PENDIENTE_APROBACION`;
- estado final: `RECHAZADA`;
- libera los equipos de esa fecha eliminando sus filas activas de `agenda_equipos_dias`;
- registra un evento de tipo `RECHAZO`;
- la mensajería utiliza el evento `RECHAZO_RESERVA`.

Representa que el Owner **no acepta una solicitud que todavía estaba esperando aprobación**.

### Cancelar reserva

- acción API: `cancelar_reserva`;
- está permitida para reservas en `PENDIENTE_APROBACION`, `CONFIRMADA` o `CANCELACION_PENDIENTE`;
- estado final: `CANCELADA_OWNER`;
- libera los equipos de esa fecha eliminando sus filas activas de `agenda_equipos_dias`;
- registra un evento `CANCELACION_OWNER`;
- la mensajería utiliza el evento `CANCELACION_OWNER`.

Representa que el Owner **anula una reserva**, independientemente de que todavía esté pendiente, ya haya sido confirmada o tenga una cancelación del cliente pendiente.

En una reserva que todavía está `PENDIENTE_APROBACION`, ambas acciones liberan el día, pero mantienen una diferencia importante de historial y significado: **Rechazar** registra que la solicitud no fue aceptada; **Cancelar reserva** registra una cancelación realizada por el Owner.

## Validación

La versión activa de `20 - Agenda - Vista` fue publicada y validada técnicamente con el HTML/JavaScript real del workflow cargado en Chrome DevTools con datos de prueba equivalentes a una reserva pendiente.

Resultado técnico confirmado:

- 3 botones presentes;
- modo flotante al salir de la zona visible;
- retorno correcto a la posición natural;
- sin errores ni advertencias de consola en la prueba.

Validación funcional final:

- el 2026-09-26 Dario confirmó en la Agenda real que el comportamiento de los botones flotantes quedó correcto.
