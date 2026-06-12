# 03 · Flota y Vehículos

[← Volver al índice](./README.md)

---

## ¿Qué es?

La flota es el catálogo de todos los **vehículos** que tu empresa alquila. Cada vehículo tiene una **ficha** con sus datos (matrícula, modelo, color, asientos, combustible, kilometraje…), sus **precios de alquiler**, sus **cargos extras** y su **estado operativo** (disponible o en mantenimiento).

El módulo amplía el modelo estándar de Flota (`fleet.vehicle`) de Odoo con la información específica del alquiler.

## Cómo acceder

**Vehicles Rental → Vehículos**

Se muestra una lista con todos los vehículos. Las vistas disponibles son: **Lista, Kanban, Calendario, Pivot y Gráfico**.

---

## Estado del vehículo

El estado es la **etiqueta superior derecha** de la ficha. El módulo sustituye los estados estándar de Odoo por dos valores propios:

| Estado | Color | Significado |
|--------|-------|-------------|
| **Operacional** | Verde | Vehículo disponible para alquilar. |
| **En Mantenimiento** | Rojo | Vehículo bloqueado en taller o revisión. No se puede alquilar. |

Visualmente verás una cinta lateral verde (*Operacional*) o roja (*En Mantenimiento*) sobre la ficha, y en las vistas lista y kanban un *badge* con el mismo color.

### Botones de la cabecera

| Botón | Cuándo aparece | Qué hace |
|---|---|---|
| **Under Maintenance** (rojo) | Estado actual = Operacional | Pasa el vehículo a *En Mantenimiento*. **Bloquea** si hay un contrato en curso con ese vehículo. |
| **Operational** (verde) | Estado actual = En Mantenimiento | Devuelve el vehículo a *Operacional* (disponible). |
| **Crear Solicitud de Mantenimiento** (amarillo) | Cualquier estado | Genera una nueva orden de mantenimiento programado a partir del *Horario de Mantenimiento* asignado al vehículo. Ver [10 · Mantenimiento](./10-mantenimiento-y-sustituciones.md). |

> Si intentas pasar a *En Mantenimiento* un coche que tiene un contrato activo, sale un aviso: *"Devuelve el contrato antes de pasar el vehículo a mantenimiento"*.

### Botones estadísticos (esquina superior derecha)

| Botón | Qué muestra |
|---|---|
| **Rental Contracts** (icono taxi) | Todos los contratos donde aparece este vehículo (lista / kanban / pivot / actividad). |
| **M. Requests** (icono engranajes) | Solicitudes de mantenimiento del vehículo. Sólo visible si hay al menos una. |
| **Maintenance Bills** (icono dinero) | Facturas de mantenimiento (de proveedores) del vehículo. Sólo visible si hay al menos una. |

---

## Crear / editar un vehículo

### Paso a paso

1. **Vehicles Rental → Vehículos → Crear** (botón "Nuevo").
2. Rellenar la cabecera:
   - **Matrícula** (`License Plate`).
   - **Modelo** (`Model`) — se selecciona del catálogo de modelos (Renault Master, Ford Transit, etc.). Al elegirlo, autorrellena marca, transmisión y combustible si están definidos en el modelo.
   - **Color**.
   - **Asientos**.
   - **Año del modelo**.
   - **Conductor por defecto** (`driver_id`) — opcional, asignado si el vehículo tiene un conductor habitual.
   - **Ubicación** — texto libre o ciudad donde está aparcado.
3. Ir a la pestaña **Rent Details** y fijar precios (al menos día, semana y mes son habituales).
4. Ir a la pestaña **Extra Charge Details** y fijar los cargos por exceso.
5. Asignar **Horario de Mantenimiento** (`maintenance_schedule_id`) — ver [Configuración](./15-configuracion.md#c-horarios-de-mantenimiento).
6. Si vas a llevar control de gastos del coche, marcar **¿Hay Gastos del Vehículo?**.
7. **Guardar**. El vehículo nace en estado **Operacional**.

### Campos relevantes (heredados de Odoo)

- **Conductor por defecto** — debe ser un *empleado autorizado*. Hay validación.
- **Odómetro** y **Unidad** (km o mi).
- **Imagen principal** — la foto del coche que aparece en la web pública.

---

## Pestañas de la ficha del vehículo

### Pestaña "Rent Details" (Datos del Alquiler)

Aquí se configuran los **precios base** de alquiler por unidad de tiempo o distancia:

| Campo | Significado |
|---|---|
| **Alquiler por Hora** | Precio por hora. |
| **Alquiler por Día** | Precio por día completo. |
| **Alquiler por Semana** | Precio por semana. |
| **Alquiler por Mes** | Precio por mes. |
| **Alquiler por Año** | Precio por año. |
| **Alquiler por Kilómetro** | Precio por km recorrido. |
| **Alquiler por Milla** | Precio por milla recorrida. |

> Si tienes activado el sistema de **Tarifas dinámicas** (ver [Tarifas](./08-tarifas-seguros-depositos.md)), los precios concretos que se aplican a un contrato pueden venir de una regla de tarifa por categoría/temporada y no de estos campos. Estos campos son el **precio de respaldo** del vehículo.

### Pestaña "Extra Charge Details" (Cargos Adicionales)

Estos importes se cobran al cliente cuando **se pasa de lo contratado**:

| Campo | Cuándo se cobra |
|---|---|
| **Cargo Extra por Hora** | Devuelve el coche más tarde, por cada hora extra. |
| **Cargo Extra por Día** | Por cada día extra que se queda con el coche. |
| **Cargo Extra por Semana / Mes / Año** | Idem para alquileres más largos. |
| **Cargo Extra por Kilómetro** | Por cada km que exceda el cupo contratado. |
| **Cargo Extra por Milla** | Idem para millas. |

Estos cargos se aplican al generar la **Extra Charge Invoice** desde el contrato. Ver [07 · Contratos](./07-contratos.md#cargos-extras).

### Pestaña "Vehicle Expenses"

**Sólo aparece si has marcado la casilla "¿Hay Gastos del Vehículo?"**.

Permite registrar gastos asociados al vehículo: combustible, peajes, multas, lavados… Cada línea tiene:

- **Producto** (de tipo gasto).
- **Importe** (con impuestos si procede).
- **Fecha**.
- **Distribución analítica** (cuenta analítica de costes).
- **Recibo adjunto** (foto del ticket/factura).
- Botón **Create Report** para enviar el gasto a aprobación.

El conductor del vehículo se hereda automáticamente como empleado responsable del gasto.

> Útil para imputar el coste real de cada coche y saber qué vehículo te está dando pérdidas.

### Pestañas estándar de Odoo (heredadas)

- **Modelo** — marca, categoría, tipo (coche/furgoneta/etc.).
- **Indicadores** — KPIs del vehículo.
- **Historial de contratos** — contratos de Odoo Fleet (no confundir con los Contratos de Alquiler del módulo).

---

## Modelos de vehículo

Los **modelos** son las plantillas que usan los vehículos: "Ford Transit Custom 2.0 TDCI", "Renault Master Furgón", "Citroën Berlingo Combi", etc.

### Cómo acceder

**Flota → Configuración → Modelos de vehículos**

### Campos importantes

- **Tipo de Vehículo** — el módulo añade **"Furgoneta" (`van`)** a los tipos estándar (Coche, Bicicleta, etc.).
- Si el tipo es **Coche** o **Furgoneta**, se muestran dos grupos extra:
  - **Modelo**: asientos, color por defecto, año.
  - **Motor**: combustible, transmisión, potencia, cilindrada, emisiones.
- Para otros tipos (bici eléctrica, patinete…), esos bloques se ocultan automáticamente.

### Categorías de vehículo

Las **categorías** (Tipo A, Tipo C, Tipo F, etc.) se gestionan también desde la configuración de Flota. Son **muy importantes** porque las [tarifas](./08-tarifas-seguros-depositos.md#a-tarifas-de-vehiculos), [seguros](./08-tarifas-seguros-depositos.md#b-tarifas-de-seguros) y [reglas de depósito](./08-tarifas-seguros-depositos.md#c-reglas-de-deposito) se configuran **por categoría**, no por vehículo individual.

Categorías típicas del sistema:

- **Tipo A** — Mini / Económico.
- **Tipo C** — Compacto.
- **Tipo F** — SUV.
- **Tipo K** — Furgoneta pequeña.
- **Tipo T, V, W, X, Z** — Variantes según tamaño/uso.

Cada coche tiene asignada **una sola categoría** y heredará automáticamente los precios y depósitos configurados para esa categoría.

---

## Ejemplos de uso

### Alta de un coche nuevo en la flota

> **Caso:** acabas de comprar un Renault Clio 2026, matrícula `1234ABC`, blanco, 5 asientos, gasolina, manual, categoría Tipo A.

1. Menú **Vehicles Rental → Vehículos → Crear**.
2. Matrícula: `1234ABC`, Modelo: `Renault Clio` (si no existe, crearlo primero en Modelos de vehículo), Color: blanco, Año: 2026, Asientos: 5.
3. Confirmar que el Modelo lleva asociada la **Categoría Tipo A**.
4. En **Rent Details** dejar los campos a 0 si vas a usar Tarifas por categoría (recomendado), o poner ahí el precio por día (40 €) y semana (210 €).
5. En **Extra Charge Details** poner 10 €/día y 0,30 €/km como cargo por exceso.
6. Asignar el **Horario de Mantenimiento** "Cada 30 días" o "Cada 10.000 km".
7. Guardar.
8. Comprobar que aparece como **Operacional** y está disponible en el [Asistente de Reserva](./06-reservas.md).

### Pasar un coche a mantenimiento

1. Abrir la ficha del vehículo.
2. Botón **Under Maintenance** (rojo) en la cabecera.
3. El sistema valida que NO haya contratos en curso con ese coche.
4. Si todo OK, el estado pasa a **En Mantenimiento** y la cinta lateral cambia a roja.
5. El vehículo deja de aparecer en el asistente de reserva.
6. Cuando vuelve del taller, pulsar **Operational** (verde) para reactivarlo.

### Quitar un vehículo del catálogo sin borrarlo

No hay un botón "dar de baja" propio. Lo recomendable es **archivar** el vehículo desde la cabecera (icono estrella en la esquina superior derecha — *Acción → Archivar*). Queda invisible para nuevas reservas pero sigue en el histórico.

---

## Validaciones y restricciones

- **Conductor por defecto** debe ser un empleado válido (si activas el sistema de conductor obligatorio).
- **Matrícula** debería ser única en la flota (Odoo lo respeta a nivel de unicidad por compañía).
- **No puedes pasar a En Mantenimiento** un vehículo con contrato activo. Devuelve el contrato primero.
- **El kilometraje** sólo se puede actualizar al alza desde el contrato (el botón *Update Vehicle Data* del contrato avisa si el odómetro nuevo es menor que el actual).

---

## Relacionado

- [02 · Panel y disponibilidad](./02-panel-y-disponibilidad.md) — para ver ocupación visual de la flota.
- [06 · Reservas](./06-reservas.md) — el vehículo se selecciona dentro del asistente de reserva.
- [07 · Contratos](./07-contratos.md) — el contrato usa la información de la ficha del vehículo.
- [08 · Tarifas, seguros y depósitos](./08-tarifas-seguros-depositos.md) — los precios reales se configuran por categoría.
- [10 · Mantenimiento y sustituciones](./10-mantenimiento-y-sustituciones.md) — solicitudes de mantenimiento y horarios.
- [15 · Configuración](./15-configuracion.md) — gestión de horarios y categorías.

---

[← Volver al índice](./README.md) · Anterior: [02 · Panel y disponibilidad](./02-panel-y-disponibilidad.md) · Siguiente: [04 · Clientes →](./04-clientes.md)
