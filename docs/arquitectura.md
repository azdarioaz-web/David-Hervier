# Arquitectura

## Aplicación

Nombre de trabajo: **DH Equipos Estéticos**.

Dominio público confirmado:

`https://dh-equiposesteticos.enredadosdelpaso.com/`

El acceso HTTP redirige permanentemente a HTTPS.

## Backend

La lógica web se implementa mediante **n8n** en ServerDockers.

Proyecto n8n:

- Project ID: `hK6Kb7G6jUIJxGZt`
- Carpeta raíz funcional: `David Hervier / DH Equipos esteticos`

Carpetas actuales:

- `00 - Seguridad`
- `10 - Equipamientos`

No se reinicia n8n para aplicar cambios de workflows; las publicaciones se realizan en caliente.

## Base de datos

PostgreSQL de ServerDockers.

- base: `enredados`
- schema exclusivo del proyecto: `dh_equipos`

El usuario de n8n tiene permisos de lectura/escritura dentro de este schema.
Los MCP PostgreSQL/ServerDockers permanecen de solo lectura.

## Apache / proxy

VirtualHost dedicado para:

`dh-equiposesteticos.enredadosdelpaso.com`

Características confirmadas:

- HTTP → HTTPS;
- certificado Let's Encrypt;
- raíz `/` → login;
- proxy hacia n8n;
- solo se exponen webhooks con prefijo `/webhook/dh-equipos-`;
- webhooks ajenos al proyecto quedan bloqueados.

## Identidad visual

Color principal:

`#143061`

Color claro / desactivado:

`#f9f8f8`

Referencia visual: estilo general similar a ISPCube, sin compartir identidad ni dependencias funcionales.
