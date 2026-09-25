# David Hervier — Alquiler de equipos

Sistema web para administrar alquileres de equipos, inicialmente orientado a la categoría **Estética**.

## Estado actual

Componentes verificados e implementados:

- dominio público: `https://dh-equiposesteticos.enredadosdelpaso.com/`;
- HTTPS con Let's Encrypt;
- autenticación por email y contraseña;
- Cookie Guard con sesiones persistentes;
- PostgreSQL en ServerDockers, base `enredados`, schema dedicado `dh_equipos`;
- backend/orquestación en n8n;
- módulo **Equipamientos**;
- módulo **Usuarios**;
- categoría inicial **Estética**.

## Documentación

- [Arquitectura](docs/arquitectura.md)
- [Seguridad y login](docs/seguridad-login.md)
- [Módulo Equipamientos](docs/modulos/equipamientos.md)
- [Módulo Usuarios](docs/modulos/usuarios.md)

## Convenciones

- La información específica de este producto se documenta en este repositorio.
- La infraestructura compartida, MCP, servidores y reglas generales se mantienen en el repositorio privado `Memorias`.
- No almacenar contraseñas, tokens, claves privadas ni secretos en este repositorio.
