# DH — Sistema visual (extracción inicial)

La interfaz existente de DH se construye mediante HTML/CSS embebido en nodos Code de n8n. El contrato de valores observados está registrado en [design-model.yaml](design-model.yaml).

**Estado:** extracción de diseño para conservar decisiones, todavía **no** hay `tokens.css`, `components.css` ni `layouts.css` vinculados a las vistas; la ejecución real sigue usando CSS local de cada workflow. No interpretar este archivo como una migración completa a estilos compartidos.

## Componentes reutilizados en Equipamientos

El formulario de Equipos, unificado el 2026-10-09, **reutiliza** `.field`, `.row2`, `.card`, `.grid`, `.tabs`, `.tab`, los botones y el modal propios de DH. No se introdujeron colores, tamaños ni clases nuevas para eliminar la pestaña Modelos.

Las pestañas de **Artículos > Alquiler** son **Equipos** y **Categorías**. Un equipo es un registro único que incluye atributos antes almacenados en un modelo. La marca y el modelo son propiedades textuales opcionales, no un catálogo independiente.

## Criterio para cambios posteriores

1. Comparar la pantalla productiva con `design-model.yaml`.
2. Reutilizar componentes existentes y evitar estilos duplicados.
3. Si se incorpora CSS compartido, desplegarlo con validación visual en todos los módulos y dejar documentado qué workflows lo consumen.
4. No alterar por extrapolación las pantallas de Agenda, Portal Cliente, Usuarios ni Reportes.

Referencia técnica: [Equipamientos](../docs/modulos/equipamientos.md).

## Menú lateral superpuesto (10/10/2026)

Patrón canónico para las pantallas internas de OWNER/OPERADOR: el menú lateral se expande **sobre** el contenido, sin empujar ni redimensionar el área principal. Al estar contraído reserva 62 px de ancho en escritorio y 56 px para pantallas de hasta 900 px; al expandirse mide 228 px y 210 px respectivamente. Se abre mediante `:hover` o `:focus-within` y permanece fijo en el borde izquierdo (`position: fixed`, `top: 0`, `left: 0`).

Implementación actual: HTML/CSS embebido en los nodos Code de n8n, sin archivo CSS externo compartido. El override de `.app` reserva únicamente el ancho contraído; `.app > aside` usa posición fija por encima del contenido, sin reflujo. Este contrato aplica a las vistas internas: Dashboard (nodo `HTML – Inicio protegido` del workflow `Loguin`), Agenda, Artículos/Equipamientos, Usuarios, Reportes, Configuraciones, WhatsApp y Mensajes en espera. No aplica al Portal Cliente ni al formulario de login.

**Verificaciones 10/10/2026:** los ocho workflows activos coinciden con sus versiones publicadas y los ocho nodos Code compilan. Prueba de comportamiento en Chromium con HTML productivo de Agenda pero datos sintéticos y sin scripts: expansión 62→228 px, área principal conservada (`left=62 px`, `width=1537 px`, viewport 1599 px). No se pudo ejecutar verificación visual autenticada en producción porque Chrome redirige al login. La publicación fue en caliente, sin reiniciar n8n; no se modificaron APIs ni consultas a base de datos.
