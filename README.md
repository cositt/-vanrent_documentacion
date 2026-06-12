# Guía de uso — Módulo Sunset / Vehicle Rental (Odoo 19)

Manual operativo en español para las personas que van a utilizar día a día el módulo de alquiler de vehículos de Sunset Rent. No es documentación técnica: aquí se explica **qué se ve en pantalla, qué se hace clic a clic, y qué ocurre detrás**.

---

## Índice general

| # | Tema | Contenido |
|---|------|-----------|
| 00 | [Introducción](./01-introduccion.md) | Qué es el módulo, recorrido por el menú principal, glosario de términos. |
| 01 | [Panel y disponibilidad](./02-panel-y-disponibilidad.md) | Dashboard con métricas, gráficos, cronograma Gantt y consulta de disponibilidad por fechas. |
| 02 | [Flota y vehículos](./03-flota-y-vehiculos.md) | Alta y edición de vehículos, modelos, categorías, estados, fotos, kilometraje. |
| 03 | [Clientes](./04-clientes.md) | Alta de clientes, gestión de documentos (DNI, carnet, pasaporte), portal del cliente. |
| 04 | [Consultas web y leads](./05-consultas-web-y-leads.md) | Gestión de consultas entrantes desde la web, pipeline CRM, conversión a contrato. |
| 05 | [Reservas](./06-reservas.md) | Asistente de reserva paso a paso y reserva múltiple de varios vehículos. |
| 06 | [Contratos](./07-contratos.md) | Ciclo de vida del contrato (borrador → en progreso → devuelto/cancelado), facturación, ampliaciones, contratos en grupo, firma digital, PDF. |
| 07 | [Tarifas, seguros y depósitos](./08-tarifas-seguros-depositos.md) | Configuración de precios por vehículo/temporada, pólizas de seguro, reglas de depósito y depósito dinámico. |
| 08 | [Pagos y Redsys](./09-pagos-y-redsys.md) | Opciones de pago, pasarela Redsys, enlaces de pago al cliente, seguimiento. |
| 09 | [Mantenimiento y sustituciones](./10-mantenimiento-y-sustituciones.md) | Solicitudes de mantenimiento, horarios, facturación a proveedores, gastos y sustitución de vehículo durante el alquiler. |
| 10 | [Daños y rayones](./11-danos-y-rayones.md) | Registro de daños, painter visual, facturación al cliente, informes de rayones. |
| 11 | [Devolución y facturación final](./12-devolucion-y-facturacion.md) | Proceso completo de devolución, cálculo de cargos finales, devolución de depósito. |
| 12 | [Portal web público](./13-portal-web-publico.md) | Páginas públicas (home, flota, contacto, detalle de vehículo) y proceso de reserva online. |
| 13 | [Emails y reportes](./14-emails-y-reportes.md) | Plantillas de email automáticas y documentos PDF (contrato, anexos, informes). |
| 14 | [Configuración](./15-configuracion.md) | Políticas de cancelación, términos de acuerdo, horarios de mantenimiento, secuencias. |
| 15 | [Solución de problemas](./16-solucion-de-problemas.md) | Errores comunes, mensajes típicos y buenas prácticas. |

---

## Convenciones del manual

- **Ruta de menú**: cuando lees algo como *Vehicles Rental → Contratos → Crear*, significa que en el menú principal de Odoo vas haciendo clic en ese orden.
- **"Botón Confirmar"**, **"Botón Devolver"**: son botones reales en la cabecera del formulario en Odoo. El nombre que ves en pantalla puede variar según el idioma del usuario; aquí siempre se usa el nombre en español.
- **Estado**: la etiqueta superior derecha de un formulario (borrador, en progreso, devuelto, cancelado…). Cambia automáticamente al pulsar los botones de acción.
- **Wizard / Asistente**: ventana emergente que pide datos para realizar una acción (por ejemplo, "Devolver depósito").

---

## ¿Por dónde empezar?

- Si nunca has usado el módulo: empieza por la [Introducción](./01-introduccion.md).
- Si quieres reservar un vehículo ya mismo: ve a [Reservas](./06-reservas.md).
- Si te ha entrado una consulta por la web: ve a [Consultas web y leads](./05-consultas-web-y-leads.md).
- Si tienes que cerrar un alquiler que vuelve: ve a [Devolución y facturación final](./12-devolucion-y-facturacion.md).
- Si algo no funciona: mira primero [Solución de problemas](./16-solucion-de-problemas.md).
