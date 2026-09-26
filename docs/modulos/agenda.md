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
- se muestran tres acciones cuando la reserva sigue siendo accionable: abrir detalle, rechazar y aprobar.

### Comportamiento de los botones al desplazarse

Confirmado el 2026-09-26:

- los botones permanecen inicialmente en su posición natural dentro del detalle;
- al desplazarse la página, se mueven junto con el contenido;
- cuando su ubicación natural alcanza el borde superior, pasan a modo flotante a 8 px del borde;
- permanecen visibles mientras la ubicación original queda por encima de la pantalla;
- al volver hacia arriba y reaparecer su ubicación real, dejan de ser flotantes y regresan al flujo normal;
- el mismo comportamiento funciona cuando el desplazamiento es producido automáticamente al seleccionar una aprobación pendiente.

La implementación usa un ancla en la posición real de las acciones y resincroniza el estado durante `scroll`, `scrollend` y cambios de tamaño.

## Validación

La versión activa de `20 - Agenda - Vista` fue publicada y validada con el HTML/JavaScript real del workflow cargado en Chrome DevTools con datos de prueba equivalentes a una reserva pendiente.

Resultado confirmado:

- 3 botones presentes;
- modo flotante al salir de la zona visible;
- retorno correcto a la posición natural;
- sin errores ni advertencias de consola en la prueba.
