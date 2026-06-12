# 02 · Panel y Disponibilidad

[← Volver al índice](./README.md)

---

## A. Panel (Dashboard)

### ¿Qué es?

El **Panel** es la pantalla resumen del negocio. Muestra en una sola vista cuántos vehículos tienes, cuántos están alquilados, cuánto has facturado este mes y qué contratos están actualmente en curso. Es el primer sitio donde mirar cada mañana.

### Cómo acceder

**Vehicles Rental → Panel**

Es el primer ítem del menú (sequence 0) — el que se abre por defecto al entrar en la aplicación.

### Indicadores que muestra

El panel se divide en **tarjetas numéricas (KPI)** y **gráficos**.

#### Tarjetas numéricas

| Tarjeta | Qué cuenta |
|---|---|
| **Total Vehicles** | Vehículos totales en la flota. |
| **Available Vehicles** | Vehículos en estado *Operacional* (disponibles para alquilar). |
| **Under Maintenance Vehicles** | Vehículos en estado *En Mantenimiento*. |
| **Draft Vehicle** | Total de contratos de vehículo registrados (en cualquier estado). |
| **In Progress** | Contratos actualmente activos (vehículo en manos del cliente). |
| **Return** | Contratos ya devueltos. |
| **Cancel** | Contratos cancelados. |
| **Customers** | Total de contactos (clientes) en la base de datos. |
| **Customer Invoice** | Facturas de cliente vinculadas a contratos de alquiler. |
| **Pending Invoices** | Facturas de contrato aún no pagadas. |

Cada tarjeta es **navegable**: al pulsarla se abre el listado correspondiente con el filtro pre-aplicado. Por ejemplo, pulsar "Pending Invoices" abre la lista de facturas pendientes para que las gestiones.

#### Gráfico de facturación por mes

Gráfico de barras del año en curso, agrupado por mes (Enero a Diciembre). Sólo se contabilizan facturas de contratos **devueltos** (cerrados), no las de contratos aún abiertos.

> Sirve para ver de un vistazo la estacionalidad del negocio: en julio-agosto subirán las barras si trabajas en zona turística.

#### Tabla de contratos en curso

Lista de los contratos *In Progress* con:

- Referencia del contrato (ej. `Contracts/0001`).
- Fecha de inicio.
- Fecha de fin.

Es la "línea de tiempo" rápida: qué coches están alquilados y hasta cuándo.

### Filtros

El panel se filtra **automáticamente por compañía activa** (multi-empresa). Si en tu instalación coexisten Sunset y Pinveco, sólo verás los datos de la compañía actualmente seleccionada en la barra superior de Odoo.

### Ejemplos de uso

- **Apertura de turno**: nada más entrar, mirar *In Progress* y *Pending Invoices*. Sabes cuántos coches están fuera y cuántas facturas hay sin cobrar.
- **Cierre de mes**: revisar el gráfico de facturación mensual y compararlo con meses anteriores.
- **Auditoría rápida**: si *Total Vehicles ≠ Available + Under Maintenance + In Progress*, hay un descuadre y conviene revisar estados.

---

## B. Disponibilidad

### ¿Qué es?

Una vista a **página completa con cronograma Gantt** donde se ven, en una línea de tiempo horizontal, todos los contratos de alquiler activos o futuros agrupados por vehículo. Permite ver de un vistazo qué coches están ocupados y cuándo se liberan.

### Cómo acceder

**Vehicles Rental → Disponibilidad**

### Qué se ve en pantalla

- **A la izquierda**: la lista de **vehículos** (icono coche). Bajo cada vehículo, sus **contratos** como sub-líneas expandibles (icono llave).
- **Columnas del grid**: Vehículo · Fecha de inicio · Fecha de fin · Duración.
- **A la derecha**: la **escala temporal** con barras horizontales. Cada barra es un contrato.
- **El texto sobre cada barra** es la referencia del contrato (`Contracts/0001`, `Contracts/0002`...).

### Qué datos incluye

- Trae **todos los contratos** del vehículo que **NO estén cancelados**.
- Considera sólo las compañías activas en tu sesión (cookies `cids` de Odoo).
- Si un vehículo tiene varios contratos, queda agrupado: el vehículo es el "padre" y cada contrato es un hijo expandible.

### Qué puedes hacer

- **Ver ocupación**: scroll horizontal en la barra temporal para mirar el pasado, el presente o el futuro.
- **Abrir un contrato directamente**: clic sobre el texto de una barra (la referencia). El sistema busca el contrato por referencia y lo abre en la misma pestaña.
- **Expandir/colapsar** un vehículo para ver sólo los que te interesan.

### Limitaciones

- **Es sólo lectura**: no se reservan contratos arrastrando barras. Para crear una reserva nueva tienes que ir a [Reservar Vehículo](./06-reservas.md) o [Reserva Múltiple](./06-reservas.md#b-reserva-multiple).
- **No hay filtro por categoría** ni por fechas dentro del propio Gantt. Se navega visualmente con la barra temporal.

### Ejemplos de uso

- **Llama un cliente preguntando si tiene libre un coche del 15 al 20**: abres Disponibilidad, buscas el vehículo en la columna izquierda y miras si hay barras en ese rango. Si no, está libre.
- **Quieres ver cuándo se libera el Audi A3**: clic sobre el vehículo, ves su barra en curso y la fecha de fin.
- **Mantenimiento programado**: identificar visualmente huecos sin contratos donde puedes meter una revisión sin afectar reservas.

---

## Cómo se actualiza la información

Tanto el Panel como Disponibilidad son **vistas dinámicas en tiempo real**. Cualquier cambio (nuevo contrato, devolución, cancelación) se refleja inmediatamente al recargar la página.

Los **estados de contrato** y **estados de vehículo** que alimentan las métricas se gestionan en:

- Estados del vehículo → ver [03 · Flota y vehículos](./03-flota-y-vehiculos.md#estado-del-vehiculo).
- Estados del contrato → ver [07 · Contratos](./07-contratos.md#ciclo-de-vida).

---

## Relacionado

- [07 · Contratos](./07-contratos.md) — para entender los estados que se ven aquí.
- [03 · Flota y vehículos](./03-flota-y-vehiculos.md) — para entender el campo "Available Vehicles".
- [06 · Reservas](./06-reservas.md) — para crear nuevas reservas desde otro punto.

---

[← Volver al índice](./README.md) · Anterior: [01 · Introducción](./01-introduccion.md) · Siguiente: [03 · Flota y vehículos →](./03-flota-y-vehiculos.md)
