# 10 · Mantenimiento y Sustituciones

[← Volver al índice](./README.md)

---

## Índice del capítulo

- [A · Solicitudes de mantenimiento](#solicitudes-de-mantenimiento)
- [B · Horarios de mantenimiento](#horarios-de-mantenimiento)
- [C · Facturación al proveedor (taller)](#facturacion-al-proveedor)
- [D · Gastos del vehículo](#gastos-del-vehiculo)
- [E · Sustituciones de vehículo](#sustituciones)

---

## A. Solicitudes de mantenimiento

### ¿Qué son?

Las **solicitudes de mantenimiento** (`maintenance.request`) son las **órdenes de taller** asociadas a un vehículo: cambio de aceite, revisión periódica, reparación, ITV…

El módulo hereda la pantalla estándar de **Mantenimiento de Odoo** y añade campos específicos del alquiler.

### Cómo acceder

**Vehicles Rental → Solicitudes de mantenimiento**

Las solicitudes que aparecen son las que tienen un vehículo de flota asignado.

### Campos añadidos por el módulo

| Campo | Notas |
|---|---|
| **Vehículo de flota** (`fleet_vehicle_id`) | Vehículo afectado. Visible cuando se rellena, en sólo lectura. |
| **Próximo Horario de Mantenimiento** (`maintenance_schedule_id`) | El horario que originó la solicitud. |
| **Fecha Próximo Mantenimiento** (`upcoming_maintenance_date`) | Cuándo toca la próxima revisión. |

### Botones añadidos en cabecera

| Botón | Cuándo | Qué hace |
|---|---|---|
| **Create Bill** | Hay piezas o servicios añadidos y aún no se ha generado factura | Abre el wizard de facturación al proveedor (ver [C](#facturacion-al-proveedor)). |
| **Maintenance Bill** (botón estadístico) | Cuando ya hay factura | Abre la factura del proveedor. |

### Pestaña "Piezas y Servicios Usados"

Dos bloques editables:

#### Repuestos de Vehículo Requeridos

Tabla con:

- **Producto** (de tipo *consumible*).
- **Descripción** — se autocompleta con el nombre del producto.
- **Cantidad** (default 1).
- **Precio unidad** — se autocompleta con el precio de venta del producto.
- **Subtotal** = cantidad × precio.

#### Servicios de Vehículo Requeridos

Tabla con:

- **Producto** (de tipo *servicio*).
- **Descripción**.
- **Cargo del servicio**.

#### Bloque de totales (abajo a la derecha)

- **Parts Price** — suma de repuestos.
- **Service Charges** — suma de servicios.
- **Total** estimado.

### Estados de la solicitud

Statusbar estándar de Odoo Mantenimiento:

```
Nuevo → En curso → Reparado → Hecho (o Cancelado)
```

### Cómo se crean

Tres maneras:

#### 1. Manual desde la ficha del vehículo

1. Abrir la ficha del vehículo en **Flota → Vehículos**.
2. Pulsar **Crear Solicitud de Mantenimiento** (amarillo, en cabecera).
3. Se genera una nueva orden con el vehículo y el horario asignado.

#### 2. Automática (cron diario)

Si el vehículo tiene asignado un **Horario de Mantenimiento**, una tarea programada (`action_create_schedule_maintenance`) revisa cada noche si hoy coincide con la *Fecha Próximo Mantenimiento* y, si coincide:

1. Calcula la siguiente fecha sumando los días del horario.
2. Crea una **nueva solicitud de mantenimiento** para el vehículo.
3. Recalibra la siguiente fecha.

Esto mantiene el calendario al día sin intervención humana.

#### 3. Manual desde el menú

**Vehicles Rental → Solicitudes de mantenimiento → Crear** — pero hay que rellenar todo, no recomendado.

---

## B. Horarios de mantenimiento

### ¿Qué son?

Un pequeño **catálogo** (`maintenance.schedule`) que define **cada cuántos días toca revisión**.

### Cómo acceder

**Vehicles Rental → Configuraciones → Horarios de Mantenimiento**

### Campos

| Campo | Notas |
|---|---|
| **Name** | Nombre descriptivo. Ej.: *30 Días*, *6 Meses*. |
| **Maintenance Days** | Número de días entre revisiones. Validación: > 0. |
| **Compañía** | Heredada. |

### Cómo se asignan al vehículo

1. **Configuración → Maintenance Schedules → Crear**: por ejemplo *Cada 30 días*, *Cada 90 días*.
2. En la ficha del vehículo, seleccionar el horario en el campo **Horario de Mantenimiento**.
3. A partir de ese momento, las órdenes de mantenimiento se calculan tomando la fecha de la última revisión y sumando los días configurados.
4. El cron diario genera las nuevas órdenes cuando llega la fecha.

### Ejemplos

- **30 Días** — revisión mensual de aceite y niveles para flota de uso intensivo.
- **6 Meses** — revisión general.
- **365 Días** — ITV anual.
- **15.000 KM** — *(no contemplado por días sino por km; este sistema sólo soporta días, el control por km va aparte)*.

---

## C. Facturación al proveedor

### ¿Para qué?

Cuando un taller externo te repara un coche, te emite una factura. Desde la solicitud de mantenimiento puedes **registrar esa factura de proveedor** directamente.

### Cómo se usa

Desde una solicitud de mantenimiento con **piezas y/o servicios cargados**:

1. Pulsar **Create Bill** en la cabecera.
2. Se abre el wizard `maintenance.request.bill`:
   - **Vendor / Proveedor** — el taller o suministrador (obligatorio).
   - Botones **Create Bill** y **Cancel**.
3. Al pulsar **Create Bill**:
   - Crea una **factura de proveedor** (`account.move` tipo `in_invoice`) con:
     - Sección **Vehicle Maintenance Parts** + una línea por cada repuesto (producto, cantidad, precio).
     - Sección **Vehicle Maintenance Services** + una línea por cada servicio.
     - Fecha de factura: hoy.
   - La factura queda **vinculada a la solicitud y al vehículo** (visible desde el botón *Maintenance Bills* en la ficha del coche).
   - Aterriza en la factura recién creada para revisar/postear.

### Dónde se ven las facturas de mantenimiento

- Desde la ficha del **vehículo** → botón estadístico **Maintenance Bills**.
- Desde la **solicitud de mantenimiento** → botón estadístico **Maintenance Bill**.

---

## D. Gastos del vehículo

### ¿Para qué?

Registrar gastos imputados a un vehículo concreto (combustible, peajes, multas, lavados, recambios pagados al contado…) para conocer el coste real de cada coche.

### Cómo activarlos

En la ficha del vehículo:

1. Marcar la casilla **¿Hay Gastos del Vehículo?**.
2. Aparece la pestaña **Vehicle Expenses**.

### Campos por gasto (sobre `hr.expense`)

El módulo añade a los gastos estándar de Odoo:

- **Fleet Vehicle** — vehículo al que se imputa.
- **Vehicle Contract** — contrato al que se imputa (opcional).
- **Driver** — conductor (`res.partner`) que originó el gasto. Si el conductor tiene un único empleado vinculado, se rellena automáticamente el campo *Employee*; si tiene varios, hay que elegirlo a mano. Si no es empleado → error.

Filtros añadidos al buscador de Gastos: **Contract**, **Vehicle**, **Driver**.

### Cómo se registra un gasto

1. En la pestaña *Vehicle Expenses* de la ficha del vehículo, pulsar **Añadir línea**.
2. Rellenar:
   - **Producto** (gasto).
   - **Importe** (con impuestos si procede).
   - **Fecha**.
   - **Distribución analítica** (cuenta analítica).
   - **Recibo adjunto** (foto del ticket o factura).
3. Pulsar **Create Report** para enviar el gasto a aprobación.

---

## E. Sustituciones

### ¿Qué es una sustitución?

Una **sustitución** (`vehicle.contract.substitution`) es cuando, **durante un alquiler activo** (`En Progreso`), se **cambia el vehículo asignado por otro**:

- Avería.
- Accidente.
- Mantenimiento programado urgente.
- Mejora de categoría.
- Reducción de categoría.
- Solicitud del cliente.
- Otro motivo.

El contrato **NO se cierra**: simplemente "cambia" el vehículo y se guarda un registro histórico con anexo PDF firmable.

### Cómo acceder

- **Desde el contrato** (forma habitual): botón **Sustituir Vehículo** en la cabecera (sólo visible si el contrato está *En Progreso*).
- **Listado global**: **Vehicles Rental → Sustituciones de Vehículos**.

### Wizard de sustitución (4 pasos)

#### Paso 1 — Inspección del Vehículo Actual

Campos:

| Campo | Notas |
|---|---|
| **Vehículo actual** y **Matrícula** | Sólo lectura. Si hubo sustituciones previas, toma el último sustituto, no el original. |
| **Fecha de Sustitución** | Por defecto: ahora. |
| **Motivo de Sustitución** | Obligatorio. Avería / Accidente / Mantenimiento Programado / Mejora de Categoría / Reducción de Categoría / Solicitud del Cliente / Otro. |
| **Notas del Motivo** | Texto libre. |
| **Kilometraje de Devolución** | Obligatorio. No puede ser menor al último registrado. |
| **Nivel de Combustible** | Vacío / 1/4 / 2/4 / 3/4 / Lleno. Obligatorio. |
| **¿Tiene daños?** | Checkbox. Si activa, se muestran: descripción HTML + monto estimado + botón **Abrir Editor de Daños - Vehículo Devuelto**. |

#### Paso 2 — Seleccionar Vehículo Sustituto

| Campo | Notas |
|---|---|
| **Vehículo Sustituto** | Sólo vehículos en estado *Disponible* y SIN contratos solapados en el período del contrato actual. Se calcula dinámicamente. |
| **Matrícula** y **Categoría** | Sólo lectura. |

Si no se selecciona vehículo o no se indica motivo, **notificación de error al avanzar**.

#### Paso 3 — Inspección del Vehículo Sustituto

| Campo | Notas |
|---|---|
| **Kilometraje de Entrega** | Se inicializa con el odómetro actual del nuevo vehículo. |
| **Nivel de Combustible** | Por defecto Lleno. |
| **¿Tiene daños previos?** | Checkbox. Si activa: descripción + botón **Abrir Editor de Daños - Vehículo Sustituto**. |

#### Paso 4 — Confirmación y Documentos

| Campo | Notas |
|---|---|
| **Tarifa Vehículo Actual** y **Tarifa Vehículo Sustituto** | Sólo lectura. |
| **¿Hay diferencia de precio?** | Si las tarifas no coinciden. |
| **Diferencia de Precio** | Positiva si el nuevo es más caro, negativa si es más barato. |
| **Aplicar diferencia de precio al contrato** | Checkbox. |
| **Generar Addendum al Contrato** | Checkbox. Por defecto activado. |
| **Firma del Cliente** y **Firma de la Empresa** | Widgets de firma (táctil). |

### Botones del wizard

- **Anterior** — vuelve al paso previo.
- **Siguiente** — valida y avanza.
- En el paso 4: **Confirmar Sustitución**.

### Qué pasa al confirmar

1. **Valida** que el contrato siga *En Progreso*, que el nuevo vehículo esté *Disponible* y que no tenga contratos solapados. Si hay solapamiento → *"El vehículo X NO está disponible en el período del contrato. Tiene contratos superpuestos: REF1, REF2... Por favor, seleccione otro vehículo"*.
2. **Crea el registro** `vehicle.contract.substitution` con toda la información (fechas, motivo, datos del viejo y del nuevo, daños, firmas, diferencia de precio).
3. **Libera el vehículo original** (lo pone como *Disponible*).
4. **Cambia el vehículo del contrato al nuevo** (`vehicle_id` → nuevo) y actualiza `last_odometer`.
5. Registra **entradas de odómetro en Fleet** para ambos vehículos.
6. Si el viejo tenía daños y monto > 0 → crea **factura de daños** automáticamente.
7. Si hay diferencia de precio y se ha marcado aplicarla → crea **factura de ajuste**:
   - `out_invoice` si el sustituto es más caro.
   - `out_refund` (nota de crédito) si es más barato.
8. Si se marcó *Generar Addendum* → genera el **PDF del anexo** (informe `action_report_vehicle_substitution_addendum`) y lo adjunta al chatter.
9. Publica un **mensaje formateado en el chatter** del contrato con tabla de detalles: fecha, motivo, vehículo anterior (nombre + matrícula), vehículo sustituto, kilometraje, diferencia de precio si aplica, y enlace *"Ver Addendum/Anexo"*.

### Listado de sustituciones

Desde el contrato, el botón estadístico **Sustituciones** abre la lista. Cada sustitución muestra como "nombre" el patrón:

```
REF_CONTRATO - MATRÍCULA_VIEJA → MATRÍCULA_NUEVA (fecha)
```

### Botones adicionales en cada sustitución

| Botón | Qué hace |
|---|---|
| **Generar Addendum** | Genera el PDF del anexo. |
| **Factura de Daños** | Crea la factura de daños del vehículo viejo si hay daños y monto. |
| **Factura de Ajuste de Precio** | Crea la factura de la diferencia. |

### Painter de daños en sustituciones

Tanto en el paso 1 (vehículo devuelto) como en el paso 3 (sustituto) hay un botón **Abrir Editor de Daños** que lanza el **painter visual** sobre la silueta del coche. Ver [11 · Daños y rayones](./11-danos-y-rayones.md).

La imagen pintada queda guardada como:

- *Imagen de Daños Pintada (Original)* — del vehículo devuelto.
- *Imagen de Daños Pintada (Sustituto)* — del vehículo entregado.

Ambas se reflejan en el anexo PDF.

### Ejemplo

> **Caso:** Cliente alquila un Renault Megane del 10 al 25 de junio. El día 15, se avería el motor. Hay un Volkswagen Golf disponible.
>
> 1. Operario abre el contrato → pulsa **Sustituir Vehículo**.
> 2. **Paso 1**: Vehículo actual = Renault Megane. Fecha: 15-jun 14:00. Motivo: Avería. Km devolución: 42.350. Combustible: 1/2. Sí hay daños → describe "Motor con fallo bielas, no arranca". Monto estimado: 0 € (lo cubre el seguro del taller).
> 3. **Paso 2**: Vehículo sustituto = Volkswagen Golf (mismo Tipo C).
> 4. **Paso 3**: Km entrega del Golf: 28.100. Combustible: Lleno. Sin daños previos.
> 5. **Paso 4**: Misma tarifa → no hay diferencia. Generar Addendum: sí. Cliente firma y empresa firma. **Confirmar Sustitución**.
> 6. El sistema cambia el vehículo del contrato al Golf, libera el Megane, genera el PDF del anexo y lo adjunta al chatter. El contrato sigue activo hasta el 25-jun, pero ya con el Golf.

### Anexo PDF de sustitución

Resumen de lo que muestra:

- Cabecera con datos de la empresa y del contrato.
- Datos del cliente (nombre, DNI).
- **Tabla del vehículo anterior**: nombre, matrícula, fecha de sustitución, km devolución, nivel de combustible, daños (con descripción e imagen pintada).
- **Tabla del vehículo sustituto**: nombre, matrícula, km entrega, combustible, daños previos.
- **Motivo y notas**.
- **Diferencia de precio** si aplica.
- **Firmas** del cliente y de la empresa.

Ver [14 · Emails y reportes](./14-emails-y-reportes.md).

---

## Tareas programadas relacionadas

| Tarea | Frecuencia | Qué hace |
|---|---|---|
| **Vehicle Rental: Vehicle Maintenance Schedule** | Diaria | Genera nuevas solicitudes de mantenimiento cuando llega la fecha programada según el horario asignado al vehículo. |

---

## Relacionado

- [03 · Flota y vehículos](./03-flota-y-vehiculos.md) — botones de mantenimiento y campo *Horario de Mantenimiento*.
- [07 · Contratos](./07-contratos.md) — desde donde se lanza la sustitución.
- [11 · Daños y rayones](./11-danos-y-rayones.md) — el painter visual usado en sustituciones.
- [14 · Emails y reportes](./14-emails-y-reportes.md) — anexo PDF de sustitución.
- [15 · Configuración](./15-configuracion.md) — gestión de horarios de mantenimiento.

---

[← Volver al índice](./README.md) · Anterior: [09 · Pagos y Redsys](./09-pagos-y-redsys.md) · Siguiente: [11 · Daños y rayones →](./11-danos-y-rayones.md)
