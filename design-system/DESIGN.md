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
