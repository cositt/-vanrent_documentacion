# 12 · Devolución y facturación final

[← Volver al índice](./README.md)

---

## ¿De qué trata este capítulo?

El **cierre del alquiler**: cuando el cliente trae el vehículo de vuelta y hay que:

1. Marcar el contrato como **Devuelto**.
2. Inspeccionar el coche y registrar daños/excesos si los hay.
3. Emitir las **facturas finales** (daños, cargos extras, servicios).
4. **Devolver el depósito** al cliente (total o parcialmente).

Es el último paso antes de cerrar definitivamente el caso.

---

## Resumen visual del proceso

```
[Contrato En Progreso]
        |
        | Cliente devuelve el coche
        v
    [Return]  ← botón de la cabecera
        |
        v
[Contrato Devuelto] ─────┐
        |                │
        | Inspección     │
        |                │
        v                │
  ¿Hay daños?            │
   ├── Sí → Painter → Damage Invoice (factura cliente)
   └── No                │
        |                │
        v                │
  ¿Hay cargos extras?    │
   ├── Sí → Extra Charge Invoice
   └── No                │
        |                │
        v                │
  ¿Hubo depósito?        │
   └── Sí → Return Deposit Invoice (nota de crédito)
                         │
                         v
                  [Cierre completo]
```

---

## Paso 1 — Devolver el contrato

### Botón "Return"

Cuando el contrato está **En Progreso** y ya tiene cuotas creadas, aparece en la cabecera el **botón verde Return**.

Al pulsarlo, el contrato pasa al estado **Devuelto** (`c_return`).

### Qué cambia tras pulsar Return

| Elemento | Antes (En Progreso) | Después (Devuelto) |
|---|---|---|
| Cabecera | Botones *Sustituir Vehículo*, *Return*, *Cancel* | Botones *Damage Invoice*, *Return Deposit Invoice* |
| Pestaña *Vehicle Damages* | Oculta | Visible |
| Pestaña *Documents* | Permite añadir | Bloqueada (no se pueden añadir más) |
| Pestaña *Términos de Alquiler* | Editable | Sólo lectura |
| Pestaña *Vehicle Images* | Editable | Sólo lectura |
| Pestaña *Insurance Policy* | Editable | Sólo lectura |
| Campo *Last Odometer* | Editable | Sólo lectura |
| Campo *Date* del cliente | Editable | Sólo lectura |
| Campo *Signature* del cliente | Editable | Sólo lectura |

> Una vez en Devuelto, el alquiler queda **cerrado documentalmente**. Sólo quedan acciones de facturación y devolución de depósito.

---

## Paso 2 — Inspeccionar el vehículo

### Comprobaciones recomendadas

1. **Kilómetros**: comparar con los km de inicio. Si superan los contratados → registrar **cargos extras**.
2. **Combustible**: confirmar que el cliente lo devuelve al mismo nivel pactado.
3. **Estado del exterior**: rayones, abolladuras, cristales… → consultar el [Scratch Report](./11-danos-y-rayones.md#d-informes-de-rayones-scratch-reports) previo para descartar lo que ya existía.
4. **Estado del interior**: limpieza, tapicería, asientos, salpicadero.
5. **Documentación**: que devuelva las llaves y la documentación del coche.

### Si hay daños

1. Abrir la pestaña *Daños del Vehículo* → **Abrir Editor de Daños**.
2. Marcar visualmente la zona en el [painter](./11-danos-y-rayones.md#b-painter-de-danos).
3. Hacer fotos del daño (las subes como adjuntos al wizard).
4. Pulsar **Damage Invoice** en la cabecera → indicar importe + descripción → **Create Invoice**.

### Si hay cargos extras (días/horas/km de más)

1. En el bloque *Extra Charges Details* del contrato, marcar el checkbox **If any extra charges**.
2. Aparecen campos para indicar:
   - Cantidad extra (días/horas/km/etc. según el tipo de alquiler).
   - Precio por unidad extra (heredado del campo del vehículo en *Extra Charge Details*).
3. El sistema calcula automáticamente el **Total Extra Charges**.
4. Pulsar **Extra Charge Invoice** (botón cerca del bloque de facturas) → genera factura `vehicle_rent_extra_charge`.

### Si hay servicios extras pendientes de facturar

Pulsar **Create Extra Service Invoice** en la cabecera → genera una factura con una línea por cada producto de la pestaña *Extra Services*.

---

## Paso 3 — Devolver el depósito

### Cuándo aparece el botón "Return Deposit Invoice"

Sólo si se cumplen estos cuatro:

1. El contrato está en estado **Devuelto**.
2. Hubo un **depósito** registrado en el contrato.
3. Se generó la **factura del depósito** previamente (`deposit_invoice_id`).
4. Aún **no se ha devuelto** (`return_deposit_invoice_id` está vacío).

### Wizard "Return Deposit" (`return.deposit`)

Es una ventana modal simple con:

| Campo | Notas |
|---|---|
| **Contract** | El contrato actual. Sólo lectura. |
| **Total Deposit** | El depósito cobrado al cliente. Sólo lectura. |
| **Amount to Return** | Campo editable. Por defecto se propone el total del depósito, pero el operario puede reducirlo si descuenta daños, combustible no repuesto, etc. |

### Qué hace al pulsar "Crear"

1. Genera una **nota de crédito** al cliente (`account.move` tipo `out_refund`):
   - Producto: **"Vehicle Rent Deposit"**.
   - Importe: el indicado en *Amount to Return*.
2. La guarda en el campo `return_deposit_invoice_id` del contrato.
3. **Abre la nota de crédito en pantalla** para que el operario la valide.

Si el campo *Amount to Return* está vacío → notificación amarilla: *"Please note: A return deposit amount is required."*

### Casos típicos del importe a devolver

| Situación | Importe a devolver |
|---|---|
| Devolución sin incidencias | = total del depósito |
| Hay daños menores que cubre el depósito | = depósito − coste de los daños |
| Hay daños que superan el depósito | = 0 (y emites factura de daños por la diferencia) |
| Combustible no repuesto | = depósito − coste del combustible |
| Limpieza extrema necesaria | = depósito − coste de la limpieza |
| Multas o sanciones pendientes | = depósito − coste de las multas |

### Conciliación

Una vez emitida la nota de crédito y validada, el contable la concilia contra el pago original del depósito desde **Contabilidad**. Cuando el pago real se hace (transferencia al cliente, devolución a la tarjeta…), se registra en la factura.

El campo **Estado Devolución Depósito** (`return_deposit_state`) del contrato se actualiza automáticamente.

---

## Resumen de facturas que se generan en el cierre

| Factura | Tipo | Cuándo se emite |
|---|---|---|
| **Cuotas del alquiler** | `out_invoice` | Durante el contrato (manual o por cron diario). |
| **Depósito** | `out_invoice` | Al inicio (o automático si vino de Redsys). |
| **Cargos extras** | `out_invoice` | Al cerrar, si hubo exceso. |
| **Servicios extras** | `out_invoice` | Al cerrar, si quedaban pendientes. |
| **Daños** | `out_invoice` | Al cerrar, si hubo desperfectos. |
| **Devolución de depósito** | `out_refund` | Al cerrar, nota de crédito al cliente. |
| **Cancelación** | `out_invoice` | Sólo si se cancela el contrato. |

Todas accesibles desde el botón estadístico **Invoices** del contrato.

---

## Verificar que el cliente está al día

Antes de despedir al cliente, comprobar en el contrato:

1. Botón estadístico **Invoices** → todas las facturas deben estar en estado **Pagado** o tener un plan claro de cobro.
2. **Estado del Depósito**: si era reembolsable, debe estar marcado *Devolución emitida*.
3. **Botón Imprimir Contrato**: el cliente puede llevarse una copia del PDF actualizado con las facturas finales.

---

## Cancelación de contrato (caso especial)

Si el contrato se **cancela** en lugar de devolverse (porque el cliente nunca llegó a recogerlo, o porque hubo un problema):

### Cómo se cancela

1. Contrato en estado **En Progreso** → pulsar **Cancel** (botón rojo, icono ⊘) en la cabecera.
2. El estado pasa a **Cancelado** (`d_cancel`).
3. Se hace visible la **pestaña Cancellation Policy**.

### Pestaña "Cancellation Policy"

Rellenar:

| Campo | Notas |
|---|---|
| **Policy** | Política de cancelación predefinida (ver [15 · Configuración](./15-configuracion.md#a-politicas-de-cancelacion)). Al elegirla rellena automáticamente los términos. |
| **Cancellation Charge** | Cargo a aplicar al cliente. |
| **Terms & Conditions** | Texto rico (editable). |
| **Cancellation Reasons** | Texto rico explicando el motivo. |

### Factura de cancelación

Una vez relleno, en la cabecera aparece el botón **Cancellation Charge**.

Al pulsarlo:

- Crea una factura al cliente con el producto **"Vehicle Contract Cancellation Charge"** por el importe.
- Si el importe es 0, igualmente permite generarla (cancelación sin cargo), pero la factura llevará líneas vacías y se crea vacía.

### Impacto en facturas previas y depósito

- Las facturas de cuotas/depósito/servicios **ya emitidas NO se anulan automáticamente**.
- El operario debe gestionar sus **reembolsos manualmente** desde Contabilidad si procede.
- El depósito ya cobrado se puede devolver con el wizard **Return Deposit Invoice** (mismo flujo que en devolución normal).

---

## Ejemplos de uso

### Ejemplo 1 — Cierre limpio sin incidencias

> **Caso:** Renault Clio alquilado 7 días. Depósito 150 €. Cuotas ya facturadas y pagadas.

1. Cliente devuelve el coche, todo correcto.
2. Operario inspecciona, todo OK.
3. Pulsa **Return** en el contrato.
4. Pulsa **Return Deposit Invoice** → Amount to Return = 150 € (por defecto).
5. **Crear** → nota de crédito de 150 € emitida.
6. Contable concilia, el cliente recibe la devolución en su tarjeta.
7. Fin.

### Ejemplo 2 — Cierre con rayón y exceso de km

> **Caso:** Renault Megane alquilado 5 días, 500 km incluidos. Devuelto con 580 km (80 km extra a 0,30 €/km) y rayón en puerta (60 €).

1. Operario inspecciona → consulta scratch reports, el rayón es nuevo.
2. Pulsa **Return**.
3. **Pinta el rayón** en el painter, lo guarda.
4. Pulsa **Damage Invoice** → 60 €, descripción detallada → **Create Invoice**.
5. Marca **If any extra charges** → Total Extra KM = 80, Extra Charge per KM = 0,30 → Total = 24 €.
6. Pulsa **Extra Charge Invoice** → factura de 24 €.
7. Pulsa **Return Deposit Invoice** → Amount to Return = 150 − 60 − 24 = **66 €**.
8. Cliente recibe: nota de crédito 66 € + factura daños 60 € + factura cargos extras 24 €.

### Ejemplo 3 — Daño grave que supera el depósito

> **Caso:** Coche devuelto con golpe que cuesta reparar 500 €. Depósito sólo de 200 €.

1. Operario pinta el daño y emite **Damage Invoice** por 500 €.
2. Emite **Return Deposit Invoice** por 0 € (no se devuelve nada).
3. La factura de daños queda emitida íntegramente. El cliente debe pagar la diferencia (500 − 200 = 300 € adicionales después de descontar el depósito ya retenido).
4. En la práctica esto se gestiona contablemente: la nota de crédito de 0 € + la factura de daños de 500 € − el pago del depósito de 200 € = saldo a cobrar 300 €.

### Ejemplo 4 — Cancelación

> **Caso:** Cliente reservó por web y pagó 200 € (alquiler 150 + depósito 50). El día antes avisa que no podrá venir.

1. Operario abre el contrato, pulsa **Cancel**.
2. En la pestaña *Cancellation Policy* selecciona la política "No reembolsable 24h antes" y pone Cancellation Charge = 150 € (el alquiler).
3. Pulsa **Cancellation Charge** → factura de 150 € al cliente.
4. Pulsa **Return Deposit Invoice** → Amount to Return = 50 € (sólo se devuelve el depósito).
5. Cliente pierde el importe del alquiler pero recupera el depósito.

---

## Validaciones y restricciones

- **No se puede pulsar Return** si todavía no hay cuotas creadas. Pulsar primero *Create Installment*.
- **No se puede facturar dos veces el mismo concepto**: tras Damage Invoice no aparece más el botón. Tras Return Deposit Invoice tampoco.
- **No se puede modificar Last Odometer ni Signature** tras devolver. Si te equivocas, hay que pedir corrección al administrador.
- **Pestaña Documents bloqueada** en Devuelto: para añadir documentos nuevos hay que crear un nuevo contrato.

---

## Errores frecuentes

| Mensaje | Causa | Solución |
|---|---|---|
| *"Please note: A return deposit amount is required"* | Wizard de devolución con importe vacío | Indicar el importe a devolver. |
| El botón *Return* no aparece | El contrato no tiene cuotas creadas | Pulsar primero *Create Installment*. |
| El botón *Return Deposit Invoice* no aparece | No había depósito o no se llegó a facturar | Revisar el campo *Deposit Invoice* del contrato. |
| La nota de crédito sale con cliente vacío | El cliente del contrato fue borrado | Recuperar el cliente o crear uno nuevo. |
| Factura de daños no aparece en *Invoices* | Falló la creación interna | Recargar y volver a pulsar *Damage Invoice*. Revisar logs si persiste. |

---

## Relacionado

- [07 · Contratos](./07-contratos.md) — estados, botones y campos.
- [11 · Daños y rayones](./11-danos-y-rayones.md) — registro de daños y painter.
- [08 · Tarifas, seguros y depósitos](./08-tarifas-seguros-depositos.md) — cómo se calcula el depósito que ahora se devuelve.
- [09 · Pagos y Redsys](./09-pagos-y-redsys.md) — formas de cobrar pendientes y devolver al cliente.
- [15 · Configuración](./15-configuracion.md#a-politicas-de-cancelacion) — políticas de cancelación.

---

[← Volver al índice](./README.md) · Anterior: [11 · Daños y rayones](./11-danos-y-rayones.md) · Siguiente: [13 · Portal web público →](./13-portal-web-publico.md)
