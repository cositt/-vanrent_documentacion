# 07 · Contratos

[← Volver al índice](./README.md)

---

## Índice de este capítulo

- [¿Qué es un contrato?](#que-es-un-contrato)
- [Cómo acceder](#como-acceder)
- [Ciclo de vida](#ciclo-de-vida)
- [Botones de la cabecera](#botones-de-la-cabecera)
- [Botones estadísticos](#botones-estadisticos)
- [Campos importantes](#campos-importantes)
- [Pestañas del contrato](#pestanas-del-contrato)
- [Validaciones](#validaciones)
- [Líneas que se facturan](#lineas-que-se-facturan)
- [Generación de facturas](#generacion-de-facturas)
- [Ampliación de contrato](#ampliacion-de-contrato)
- [Contratos en grupo](#contratos-en-grupo)
- [Firma digital](#firma-digital)
- [Imprimir y enviar contrato](#imprimir-y-enviar-contrato)

---

## ¿Qué es un contrato?

El **Contrato de Alquiler** (`vehicle.contract`) es el documento operativo y legal que regula un alquiler concreto. Tiene un **estado**, un **cliente**, un **vehículo**, **fechas**, un **precio**, un **seguro**, un **depósito** y genera **facturas**.

Es el centro neurálgico del módulo: todo lo demás (reservas, leads, sustituciones, ampliaciones, devoluciones, facturas, daños) gira alrededor del contrato.

## Cómo acceder

**Vehicles Rental → Contratos**

Lista con todos los contratos. Vistas disponibles: **Lista, Kanban, Calendario, Gantt (Ocupación de Flota), Pivot (Análisis Financiero) y Gráfico**.

Filtros disponibles en la barra de búsqueda: por estado, tipo de tarifa, vehículo, categoría, cliente, grupo, mes y trimestre.

---

## Ciclo de vida

La barra superior del contrato muestra **cuatro pasos**:

| Estado | Color | Significado |
|---|---|---|
| **Nuevo / Borrador** (`a_draft`) | Azul | Editable, no compromete vehículo. Se puede cambiar fechas, cliente, tarifa. |
| **En Progreso** (`b_in_progress`) | Verde/amarillo | Alquiler activo. Vehículo comprometido. Se pueden generar facturas. |
| **Devuelto** (`c_return`) | Gris | El cliente entregó el vehículo. Quedan acciones de daños y devolución de depósito. |
| **Cancelado** (`d_cancel`) | Rojo | Contrato no ejecutado o interrumpido. Se factura el cargo de cancelación. |

> En **Lista** y **Gantt**, cada estado se ve con su color distintivo.

### Transiciones

```
[Borrador] --In Progress--> [En Progreso] --Return--> [Devuelto]
                                        \
                                         --Cancel--> [Cancelado]
```

Un contrato **devuelto** o **cancelado** ya no se puede reactivar. Si necesitas volver atrás, duplica el contrato.

---

## Botones de la cabecera

Sólo aparecen cuando tiene sentido según el estado:

| Botón | Cuándo aparece | Qué hace |
|---|---|---|
| **Update Vehicle Data** | No Devuelto ni Cancelado | Copia al fichero de flota (Fleet) el modelo, transmisión, combustible, odómetro y unidad del odómetro definidos en el contrato. Exige que el odómetro nuevo sea mayor que el actual; si no, avisa. |
| **In Progress** | Sólo en Borrador | Activa el contrato. Antes valida: tipo de alquiler informado, fechas informadas, sin solapamiento con otro contrato en curso del mismo vehículo. Si pasa, abre el compositor de email con la plantilla de reserva ya cargada para enviar al cliente. |
| **Create Extra Service Invoice** | Hay servicios extras y contrato no devuelto/cancelado | Crea una factura de cliente con todas las líneas de la pestaña *Extra Services*. |
| **Create Installment** | En Progreso, sin cuotas creadas | Genera el calendario de cuotas según el *Payment Type* elegido (Daily/Weekly/Monthly/Quarterly/Yearly/Full Payment). |
| **Return** | En Progreso y con cuotas creadas | Marca el contrato como Devuelto. |
| **Sustituir Vehículo** | En Progreso | Abre el asistente de sustitución (ver [10 · Mantenimiento y sustituciones](./10-mantenimiento-y-sustituciones.md#e-sustituciones)). |
| **Damage Invoice** | Devuelto y sin factura de daños | Lanza el wizard de daños del vehículo para registrar y facturar daños. |
| **Return Deposit Invoice** | Devuelto, con depósito y factura de depósito, sin devolución aún | Abre el wizard "Devolver depósito" (ver [12 · Devolución](./12-devolucion-y-facturacion.md)). |
| **Create Trip Expense Reports** | Hay conductor obligatorio, gastos de viaje activados y gastos en borrador | Envía a aprobación los gastos del conductor asociados al contrato. |
| **Cancel** | En Progreso | Cambia a Cancelado y hace visible la pestaña *Cancellation Policy*. |
| **Cancellation Charge** | Cancelado y sin factura de cancelación | Genera la factura del cargo de cancelación según política. |
| **Imprimir Contrato** | Siempre | Imprime el PDF del contrato. |
| **Enviar Contrato** | Siempre | Genera el PDF, lo adjunta y abre el compositor de email con la plantilla del contrato. |

---

## Botones estadísticos

En la esquina superior derecha:

| Botón | Qué muestra |
|---|---|
| **Documents** | Documentos del cliente vinculados al contrato (DNI, carnet…). Si el contrato está Devuelto, NO permite crear nuevos. |
| **Invoices** | Todas las facturas que tienen este contrato como referencia (alquiler, depósito, devolución de depósito, servicios, daños, cancelación). |
| **Sustituciones** | Sólo si hubo sustituciones; abre el listado de las mismas. |

---

## Campos importantes

Los campos se agrupan visualmente como en el formulario:

### Cabecera

| Campo | Notas |
|---|---|
| **Reference No** | Código autogenerado por secuencia (`Contracts/0001`...). |

### Detalles del Cliente

| Campo | Notas |
|---|---|
| **Lead** | Sólo lectura si viene de un Lead CRM. |
| **Cliente** | Obligatorio. Sólo editable en Borrador. |
| **Phone / Email** | Se autorrellenan al elegir cliente. Editables en Borrador. |
| **DNI / NIE** | Documento de identidad. |
| **Fecha de expiración del DNI** | Para validación. |
| **Domicilio** | Dirección del cliente. |

### Carnet de Conducir

| Campo | Notas |
|---|---|
| **Carnet de Conducir** | Número. |
| **Fecha Expedición** | Cuándo se sacó. |
| **Fecha Caducidad** | Cuándo expira. |

### Detalles de Recogida y Devolución

| Campo | Notas |
|---|---|
| **Pick-up Date** | Fecha y hora de recogida (obligatoria). |
| **Dirección de recogida** | Calle 1, Calle 2, Ciudad, Provincia, CP, País. Se autorrellena con los datos de la compañía si están vacíos. |
| **Drop-off Date** | Fecha y hora de devolución (obligatoria). |
| **Dirección de devolución** | Misma estructura. |

> Si en un contrato existente cambias la **ciudad de devolución**, el sistema **actualiza automáticamente la ubicación del vehículo en la flota** a esa ciudad.

### Vehicle Details

| Campo | Notas |
|---|---|
| **Vehicle** | Combo de vehículos disponibles (sin solapamientos). Obligatorio si hay fechas. |
| **License Plate** | Matrícula. Sólo lectura. |
| **Model / Transmission / Fuel Type / Categoría** | Heredados del vehículo. Sólo lectura. |
| **Last Odometer** y **Unit** (km o mi) | Kilometraje. |
| **Driver Required** | ¿Requiere conductor? Si está activo aparecen los siguientes. |
| **Driver** | Sólo conductores que sean empleados (lo valida el sistema). |
| **Charge Type** | Incluido en el alquiler / Aparte. |
| **Driver Charge** | Importe del conductor. Obligatorio > 0 si está visible. |
| **Trip Expenses** | Gastos del viaje del conductor. |
| **Custom Scratch Report** + **Scratch Report** | Para anexar el informe de rayones inicial. |

### Rent Details

| Campo | Notas |
|---|---|
| **Tipo de Tarifa** | *Tarifa Estándar* o *FLEXIRENT*. Si eliges FLEXIRENT y la duración es menor al mínimo configurado, sale aviso amarillo y vuelve a Standard automáticamente. |
| **Tipo de Alquiler** (`rent_type`) | Horas / Días / Semanas / Meses / Años / Kilómetros / Millas. (Oculto si tipo FLEXIRENT). |
| **Precio del alquiler** (`rent`) | La etiqueta cambia dinámicamente según el tipo de alquiler (Alquiler por Día, por Semana, por Mes, etc.). |
| **Total Days/Weeks/Months/Hours/Years/KM/Miles** | Cantidad. Para días/semanas/meses/horas/años se calcula automáticamente de las fechas. Para km/millas el usuario teclea el total previsto. |
| **Total Renta del Vehículo** | Calculado: precio × cantidad + cargo del conductor (si es *Excluido*). |
| **Impuestos** (`tax_ids`) | Impuestos aplicables (varios). |

### Extra Charges Details

Cargos adicionales si el cliente excede el contrato:

| Campo | Notas |
|---|---|
| **If any extra charges** | Checkbox que muestra el resto. |
| **Total Extra Days/Weeks/Months/Hours/Years/KM/Miles** | Unidades extras según el tipo de alquiler. |
| **Extra Charge per Day/Week/Month/Hour/Year/KM/MI** | Precio extra por unidad. |
| **Total Extra Charges** | Calculado. |

> **Ejemplo:** si el tipo de alquiler es "Días" y activas el checkbox de cargos extras, aparecen *Total Extra Days* y *Extra Charge per Day*. Si fuera "KM", aparecerían *Total Extra KM* y *Extra Charge per KM*.

### Invoice Details (lado derecho)

Resúmenes con *badge* de estado de pago (`paid`, `not_paid`, `in_payment`, `partial`, `reversed`):

- **Extra Charge Invoice**
- **Extra Service Invoice**
- **Deposit Invoice**
- **Return Deposit**
- **Cancellation Invoice**

Junto al bloque, un botón **Extra Charge Invoice** para crear la factura de cargos extras.

### Payment Details

| Campo | Notas |
|---|---|
| **Payment Type** | Cómo se factura el alquiler: Daily · Weekly · Monthly · Quarterly · Yearly · Full Payment. |

### Detalles de Depósito Dinámico

| Campo | Notas |
|---|---|
| **Deposit Card Type** | Tarjeta de Débito / Crédito. |
| **Use Deposit from Rule** | Si está marcado, el depósito lo calcula automáticamente una [regla configurada](./08-tarifas-seguros-depositos.md#c-reglas-de-deposito). |
| **Deposit Rule** | Sólo lectura. Indica qué regla se aplicó. |
| **Calculated Deposit** | Sólo lectura. Importe calculado. |
| **Invoice Item** | Producto que se usará para facturar el alquiler. |
| **If any deposit** | Checkbox; si está activo permite editar el depósito manual. |
| **Deposit** | Importe. Editable sólo si NO usa regla y aún no hay factura de depósito. |

### Detalles de la Compañía

- **Company** (multicompañía).
- **Responsible** — usuario responsable del contrato.

### Bloque inferior (firma)

- **Date** — fecha de firma (no puede ser anterior a hoy).
- **Signature** — pad táctil para que el cliente firme con dedo/lápiz.

---

## Pestañas del contrato

### 1. Vehicle Payment Details

Calendario de **cuotas (installments)** generadas a partir del *Payment Type*.

Por cada cuota:
- Ítem de factura (producto).
- Nombre (ej. *Installment 3*).
- Fecha.
- Importe.
- Factura asociada.
- Estado de pago (badge de color).
- Botón **Create Invoice** por cuota para emitir su factura.

### 2. Términos de Alquiler

- **Acuerdo de Alquiler** (rental.agreement.terms) — al seleccionarlo, rellena automáticamente:
- **Terms & Conditions** — HTML editable.

### 3. Trip Expenses

Sólo si hay **conductor obligatorio** y **gastos de viaje activados**. Edita gastos (`hr.expense`) ligados al contrato.

### 4. Extra Services

Líneas de servicios extras: producto, cantidad, descripción, importe y total. Se suma en *Total Service Charge*.

### 5. Vehicle Images

Galería kanban de fotos del vehículo al inicio del alquiler (con indicador del tamaño del archivo).

### 6. Insurance Policy

Pólizas de seguro asociadas: número, nombre, descripción, importe y archivo PDF.

### 7. Cancellation Policy

**Sólo visible cuando el estado es Cancelado**. Contiene política, cargo, términos y motivos de cancelación.

### 8. Daños del Vehículo

Visor de la "imagen pintada" de daños — un canvas donde el operario marca los golpes. Tiene:
- **Abrir Editor de Daños** (lanza el lienzo).
- **Limpiar Imagen**.

Ver [11 · Daños y rayones](./11-danos-y-rayones.md).

### 9. Vehicle Damages

**Sólo visible cuando ya hay factura de daños**. Muestra importe, descripción y galería de fotos del daño.

---

## Validaciones

Qué impide guardar o pasar de estado:

- **Fecha de inicio**: no puede ser anterior a hoy.
- **Fecha de firma**: no puede ser anterior a hoy.
- **Drop-off ≥ Pick-up**: si no, error *"Drop-off Date debe ser mayor"*.
- **Duración mínima por tipo de alquiler**:
  - Week → mínimo 7 días.
  - Month → 28 días.
  - Year → 365 días.
- **Renta > 0** según tipo (mensaje *"The Rent per Day/Week/... must be greater than zero"*).
- **Total KM > 0** y **Total Millas > 0**.
- **Si cargos extras activos**: contador (días/semanas/etc.) y precio extra deben ser > 0.
- **Si requiere conductor**: cargo del conductor (si *Excluding*) debe ser > 0.
- **Driver = empleado**: el conductor debe ser uno de los empleados permitidos.
- **Sin solapamiento**: al pulsar *In Progress* se verifica que NO exista otro contrato En Progreso o Devuelto para el mismo vehículo en el período. Si lo hay: *"No se puede activar este contrato porque las fechas se solapan con otro(s) contrato(s) en curso para el mismo vehículo: REF1 (dd/mm/yyyy → dd/mm/yyyy)..."*.
- **Activación sin tipo de alquiler o sin fechas**: notificación amarilla (*"Elija su unidad de alquiler preferida"* / *"Debe definir las fechas de recogida y devolución antes de activar el contrato"*).
- **Odómetro**: el último odómetro debe ser mayor que el actual del vehículo al hacer *Update Vehicle Data*.
- **Diario de ventas**: si la compañía no tiene diario de tipo *Ventas*, las acciones de facturar abortan con mensaje guía hacia *Contabilidad → Configuración → Diarios*.
- **FLEXIRENT**: si los días totales no llegan al mínimo configurado, el sistema cambia automáticamente la tarifa a Estándar y muestra aviso amarillo.

---

## Líneas que se facturan

Un contrato **NO es una factura única**: genera **varias facturas separadas**, cada una con su propio estado de pago y badge:

| Tipo de factura | Producto | Cuándo se genera |
|---|---|---|
| **Cuotas del alquiler** | `vehicle_rent_charge` | Manualmente con *Create Invoice* en cada cuota, o automáticamente por el cron diario. |
| **Depósito** | `vehicle_rent_deposit` | Al inicio del alquiler. Si viene de Redsys, se crea y paga automáticamente. |
| **Devolución del depósito** | `vehicle_rent_deposit` (out_refund) | Al devolver, vía wizard *Return Deposit*. |
| **Cargos extras** | `vehicle_rent_extra_charge` | Al pulsar *Extra Charge Invoice*. |
| **Servicios extras** | Producto de la línea | Al pulsar *Create Extra Service Invoice*. |
| **Daños** | `vehicle_damage_amount` | Al pulsar *Damage Invoice* (sólo Devuelto). |
| **Cancelación** | `vehicle_contract_cancellation_charge` | Al pulsar *Cancellation Charge* (sólo Cancelado). |

Todas se ven desde el botón estadístico **Invoices** del contrato.

> Cuando una factura pasa a *paid*, se publica automáticamente un mensaje en el chatter del contrato: *"💰 Factura Pagada — La factura X por importe Y ha sido pagada completamente"*.

---

## Generación de facturas

### Cuotas — manual

1. Contrato en estado *En Progreso*.
2. Pulsar **Create Installment** en la cabecera (sólo aparece si no hay cuotas creadas).
3. El sistema genera el calendario según el *Payment Type*:
   - **Daily**: una cuota por día.
   - **Weekly**: una cuota por semana.
   - **Monthly**: una cuota por mes.
   - **Quarterly**: una cuota por trimestre.
   - **Yearly**: una cuota anual.
   - **Full Payment**: una sola cuota por el importe total.
4. En la pestaña *Vehicle Payment Details*, pulsar **Create Invoice** en cada cuota cuando llegue su fecha.

### Cuotas — automático (cron diario)

Existe una **tarea programada** (`action_create_rent_payment_invoice`) que cada día revisa los contratos *En Progreso* y factura automáticamente las cuotas cuya fecha es hoy. Esto evita tener que crear las facturas manualmente.

Esa cuota generada incluye **automáticamente todas las líneas** del contrato en este orden:

1. **Alquiler del vehículo** — con descripción que incluye matrícula, fechas, días y km.
2. **Seguro** — precio/día × días totales (con etiqueta "Conductor Especial" si aplica).
3. **Depósito de Seguridad** — el calculado por la regla si está activo, o el manual.
4. **Km Extra FLEXIRENT** — si el alquiler ha excedido el paquete.
5. **Servicios Extras** del contrato.
6. **Cargos Extras** — por superación del tiempo contratado.

El importe total de la factura sustituye al *Payment Amount* de la cuota.

### Cargos extras

- Botón **Extra Charge Invoice** en el bloque *Extra Charges Details*.
- Genera una factura con el producto `vehicle_rent_extra_charge` por las unidades extras × precio extra.

### Servicios extras

- Botón **Create Extra Service Invoice** en la cabecera.
- Genera una factura con una línea por cada producto/servicio extra cargado.

### Daños

- Botón **Damage Invoice** en la cabecera (sólo Devuelto).
- Abre wizard para indicar importe y descripción.
- Genera la factura con el producto `vehicle_damage_amount`.

### Cancelación

- Botón **Cancellation Charge** en la cabecera (sólo Cancelado).
- Genera la factura del cargo de cancelación según política.

### Devolución de depósito

- Botón **Return Deposit Invoice** en la cabecera (sólo Devuelto y con depósito previo).
- Abre wizard para indicar importe a devolver.
- Genera una **nota de crédito** (`out_refund`).

> Todas las facturas se crean **en estado borrador**, ligadas al contrato (`vehicle_contract_id` en `account.move`), con el cliente del contrato, fecha = hoy, vencimiento = hoy y en el diario de ventas de la compañía. El usuario después las valida/publica como cualquier otra factura de Odoo.

---

## Ampliación de contrato

### ¿Qué es?

Una **Ampliación** (`vehicle.contract.extension`) sirve para **alargar la fecha de fin** de un contrato ya activo (ej. el cliente decide seguir tres días más).

Genera:
- Un **anexo firmable**.
- Una **factura de ampliación** propia.
- **Actualiza automáticamente la fecha de fin del contrato** al facturar.

### Cómo se crea

Desde el contrato:
- Botón **Nueva ampliación** (acción del modelo, `action_new_extension`), o
- Desde el menú **Vehicles Rental → Ampliaciones → Crear**.

Al abrirse el formulario, el sistema **sugiere por defecto la tarifa diaria del contrato** como "Tarifa por día".

### Campos del formulario

| Campo | Notas |
|---|---|
| **Contrato** | Referencia al contrato origen (obligatorio). |
| **Cliente** | Hereda del contrato (sólo lectura). |
| **Fecha fin original** | Sólo lectura. |
| **Nueva fecha de fin** | Obligatoria. Debe ser **posterior** a la original. Si no, error: *"La nueva fecha de fin debe ser posterior a la fecha de fin original del contrato"*. |
| **Días de ampliación** | Calculado automáticamente (diferencia en días). |
| **Tarifa por día** | Editable. Por defecto, la del contrato. |
| **Importe ampliación** | Calculado: días × tarifa. |
| **Estado** | Borrador / Enviado a firmar / Firmado / Facturado / Cancelado. |
| **Factura ampliación** y **Estado pago** | Sólo lectura. |

### Botones de la ampliación

| Botón | Estado requerido | Qué hace |
|---|---|---|
| **Enviar a firmar** | Borrador | Si Odoo Sign está instalado, abre el wizard de Sign con la ampliación como referencia y el cliente como firmante. Estado pasa a *Enviado a firmar*. Si Sign no está, marca como *Enviado* manualmente. |
| **Marcar como enviado** | Borrador | Para indicar manualmente que se envió por otro canal. |
| **Marcar como firmado** | Borrador o Enviado | Marca como *Firmado* sin pasar por Sign (firma presencial). |
| **Cancelar** | Cualquier estado salvo Facturado | Pasa a *Cancelado*. |
| **Crear factura de ampliación** | Sólo Firmado | Genera factura al cliente con producto *Vehicle Rent Charge*, cantidad = días, precio = tarifa diaria. Pasa a **Facturado** **y reemplaza la fecha de fin del contrato** por la nueva. |
| **Ver factura** | Si existe | Abre la factura. |

> Cuando Odoo Sign devuelve la firma del cliente, el estado se actualiza **automáticamente** a *Firmado* sin intervención manual.

### Anexo PDF

El anexo se renderiza desde el botón *Enviar a firmar* como documento de Sign y queda asociado al contrato/cliente. También se puede imprimir suelto con el reporte `action_report_vehicle_contract_extension_addendum`.

### Ejemplo

> **Caso:** Contrato hasta el 15 de junio. El cliente decide quedarse hasta el 20.
>
> 1. Crear ampliación. Fecha original: 15-jun. Nueva fecha: 20-jun. Tarifa: 35 €/día (hereda del contrato). Importe: 5 × 35 = 175 €.
> 2. Pulsar *Enviar a firmar* → cliente firma online.
> 3. Pulsar *Crear factura de ampliación* → se genera la factura por 175 €.
> 4. La fecha de fin del contrato se actualiza a 20-jun automáticamente.

---

## Contratos en grupo

### ¿Para qué sirven?

Un **Grupo de Contratos** (`vehicle.contract.group`) permite que un mismo cliente tenga **varios contratos a la vez** (ej. una flota de 5 coches alquilados en bloque) y **se facture todo de forma consolidada en una sola factura**.

Se generan automáticamente al usar [Reserva Múltiple](./06-reservas.md#b-reserva-multiple), o se pueden crear a mano desde el menú **Contratos Grupo**.

### Campos del grupo

| Campo | Notas |
|---|---|
| **Referencia** | Autogenerada. |
| **Cliente** | Obligatorio. |
| **Compañía y Moneda** | Heredadas. |
| **Fecha Inicio / Fin** | Para sincronizar contratos hijos. |
| **Tipo de Pago** | Diario / Semanal / Mensual / Trimestral / Anual / Pago Completo. |
| **Tipo de Tarifa** | Tarifa Estándar / FLEXIRENT. |
| **Tipo de Tarjeta** | Débito / Crédito (al cambiar, propaga la elección a todos los contratos hijos). |
| **Impuestos** | Aplicables. |
| **Contratos** | Lista de contratos hijo. |
| **Nº Contratos** | Contador. |
| **Importe Total** y **Depósito Total** | Sumatorios. |
| **Facturas Consolidadas** | La factura única generada. |
| **Observaciones** | Texto libre. |

### Estado del grupo (calculado automáticamente)

| Estado | Cuándo |
|---|---|
| **Borrador** | Sin contratos o todos en borrador. |
| **Activo** | Al menos un contrato en progreso. |
| **Devolución Parcial** | Coexisten contratos en progreso y devueltos. |
| **Finalizado** | Todos los contratos devuelto/cancelado (los no cancelados son todos devuelto). |
| **Cancelado** | Todos los contratos cancelados. |

### Botones del grupo

| Botón | Qué hace |
|---|---|
| **Ver contratos del grupo** | Lista los contratos hijo. |
| **Ver facturas del grupo** | Lista las facturas consolidadas. |
| **Añadir Vehículo al Grupo** | Abre el [Asistente de Reserva](./06-reservas.md) precargado con cliente, fechas, tipo de tarifa y vinculado a este grupo. El nuevo contrato queda ligado automáticamente. |
| **Activar todos** | Recorre todos los contratos *Borrador* del grupo y los pasa a *En Progreso* (valida solapamientos, fechas, tipo de alquiler). Si algún contrato falla, muestra la lista de errores. Después crea automáticamente las cuotas en cada uno. |
| **Facturación consolidada** | Genera **UNA SOLA factura** al cliente con todas las líneas, prefijadas con el vehículo entre corchetes: ej. `[Tesla M3 - 1234ABC] Installment 1`. Incluye cuotas pendientes, depósitos no facturados, seguro × días, servicios extras de cada contrato. Si no hay nada que facturar → error *"No hay conceptos pendientes de facturar"*. |

### Ejemplo

> **Caso:** Empresa de mensajería alquila 5 furgonetas durante 6 meses.
>
> 1. Crear *Reserva Múltiple* con cliente "Mensajería Sevilla S.L." y 5 líneas (una por furgoneta).
> 2. Confirmar → se generan grupo + 5 contratos.
> 3. Cada mes, pulsar **Facturación consolidada** → factura única con 5 líneas (una por furgoneta) que se manda al cliente.

---

## Firma digital

El módulo se apoya en **Odoo Sign** (módulo `sign`, ya declarado como dependencia) para firmar:

### Contratos

- El botón **Enviar Contrato** **no es Sign directamente**: renderiza el PDF, lo adjunta a un email con la plantilla `email_template_vehicle_contract` y abre el compositor para que el operario lo envíe al cliente.
- Adicionalmente, dentro del contrato, el cliente puede dejar su **firma** en el campo *Signature* (widget táctil) al pie, con la *Date* justo encima.

### Ampliaciones

- Botón explícito **Enviar a firmar**.
- Si Odoo Sign está activo, abre la acción `sign.action_sign_send_request` con la ampliación como `reference_doc` y el cliente como firmante.
- Cuando el cliente firma en Sign, el módulo lo detecta automáticamente y cambia el estado de la ampliación de *Enviado a firmar* a *Firmado*.

### Sustituciones

- La sustitución guarda **dos firmas binarias** (`customer_signature` y `company_signature`) que se rellenan en el paso 4 del [wizard de sustitución](./10-mantenimiento-y-sustituciones.md#e-sustituciones) y quedan estampadas en el anexo PDF.

---

## Imprimir y enviar contrato

### Botón "Imprimir Contrato"

(Icono impresora en la cabecera.)

Lanza el reporte `vehicle_rental.vehicle_contract_report` y descarga el PDF.

### Botón "Enviar Contrato"

(Icono sobre, verde, en la cabecera.)

1. Genera el PDF.
2. Lo guarda como adjunto `Contrato_<REF>.pdf` en el contrato.
3. Abre el compositor de email con la plantilla `email_template_vehicle_contract` ya cargada, destinatario = cliente.
4. El operario revisa y envía.

### Qué muestra el PDF

Título: **CONTRATO DE ALQUILER DE VEHÍCULO**.
Cabecera: "SUNSET RENT - Alquiler de Vehículos".

Contenido:

- **Datos del Cliente**: nombre, email, teléfono, DNI/NIE + fecha de expiración, domicilio, carnet + fecha de expedición + fecha de caducidad.
- **Datos del Vehículo**: vehículo, modelo, categoría, matrícula y datos de flota.
- **Fechas de recogida y devolución** + direcciones.
- **Tarifa y cálculo del total**.
- **Condiciones de alquiler** (términos seleccionados).
- **Política de cancelación** si aplica.
- **Firma del cliente y de la empresa** al pie.

Ver más detalles en [14 · Emails y reportes](./14-emails-y-reportes.md).

---

## Recorrido típico de un alquiler (resumen)

1. Operador abre **Reservar Vehículo** → elige cliente, fechas, categoría, duración, km, tarifa → ve precio → elige vehículo → *Crear contrato*.
2. Se abre el **contrato en Borrador** → completa DNI, carnet, dirección, seguro, fotos, términos.
3. Pulsa **In Progress** → contrato activo y se abre email de confirmación al cliente.
4. Pulsa **Create Installment** → genera cuotas según *Payment Type*.
5. Día a día, las cuotas se facturan (automáticamente con el cron o manualmente con *Create Invoice* por cuota).
6. *(Opcional)* Si el cliente quiere extender → crear **Ampliación** → enviar a firmar → al firmar y facturar, se mueve la fecha de fin.
7. *(Opcional)* Si hay avería → **Sustituir Vehículo** (wizard 4 pasos) → cambio de vehículo + addendum + ajuste de precio.
8. Al final → **Return** → registrar odómetro/daños → **Damage Invoice** si hay daños → **Return Deposit Invoice** para devolver el depósito.
9. Si hay cancelación: **Cancel** → rellenar política → **Cancellation Charge**.

---

## Relacionado

- [06 · Reservas](./06-reservas.md) — cómo se crea un contrato desde cero.
- [05 · Consultas web y leads](./05-consultas-web-y-leads.md) — cómo se crea desde una reserva web.
- [08 · Tarifas, seguros y depósitos](./08-tarifas-seguros-depositos.md) — de dónde salen los precios.
- [09 · Pagos y Redsys](./09-pagos-y-redsys.md) — formas de cobro.
- [10 · Mantenimiento y sustituciones](./10-mantenimiento-y-sustituciones.md) — sustituciones de vehículo.
- [11 · Daños y rayones](./11-danos-y-rayones.md) — el painter de daños y la facturación.
- [12 · Devolución y facturación final](./12-devolucion-y-facturacion.md) — proceso de cierre del contrato.
- [14 · Emails y reportes](./14-emails-y-reportes.md) — plantillas y PDFs.

---

[← Volver al índice](./README.md) · Anterior: [06 · Reservas](./06-reservas.md) · Siguiente: [08 · Tarifas, seguros y depósitos →](./08-tarifas-seguros-depositos.md)
