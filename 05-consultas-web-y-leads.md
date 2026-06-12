# 05 · Consultas Web y Leads

[← Volver al índice](./README.md)

---

## ¿Qué es una Consulta de Reserva?

Una **Consulta de Reserva** es un Lead u Oportunidad del CRM de Odoo enriquecido con datos específicos del alquiler (vehículo, categoría, fechas, datos del conductor). Sirve para tres cosas:

1. **Recoger una petición de la web pública**: cuando un cliente reserva y paga online, el sistema crea automáticamente una consulta para que el equipo la procese.
2. **Permitir el alta manual**: el comercial puede crear una consulta antes de generar el contrato (útil para reservas pendientes de confirmar por teléfono).
3. **Bloquear huecos de disponibilidad**: una consulta abierta con categoría + fechas reserva virtualmente un hueco de esa categoría, evitando sobre-reservas.

> Una consulta **NO es un contrato**. Es interés/intención de alquilar. El paso del lead al contrato lo hace el operario expresamente.

---

## Cómo acceder

**Vehicles Rental → Consultas de Reserva**

Se abre una vista **Kanban + Formulario** filtrada automáticamente para mostrar sólo los Leads cuyo nombre contenga `Consulta de Reserva` y estén en la **primera etapa** del pipeline (las consultas pendientes de procesar).

Es la "bandeja de entrada" de reservas web.

---

## ¿Cómo llegan las consultas?

### Origen 1 — Desde la web pública

Cuando un cliente:

1. Entra en `https://<tu-dominio>/web/booking-enquiry`.
2. Selecciona una categoría de vehículo.
3. Elige fechas, ubicación y rellena el formulario completo (datos de contacto, DNI, carnet, dirección, tarjeta).
4. Paga el **depósito** con Redsys.
5. Al volver de la pasarela en `/rental/success`, el sistema:
   - Crea/recupera el **contacto** (busca primero por email, luego por teléfono).
   - Genera el **Lead/Oportunidad** llamado `Consulta de Reserva - <Nombre del cliente>`.
   - Crea automáticamente la **factura del depósito** y la marca como pagada.

Ver [13 · Portal web público](./13-portal-web-publico.md) para el detalle del flujo web.

### Origen 2 — Manual desde el backend

El comercial puede crear un Lead desde:

- **CRM → Pipeline → Crear** (lead estándar de Odoo).
- O directamente desde **Consultas de Reserva → Crear**.

Y rellenar a mano todos los campos del alquiler.

### Origen 3 — Formulario de contacto web

Si el cliente usa el formulario genérico de **`/contactus`** (no el de booking), también se genera un Lead, pero **sin datos de alquiler**. Es un lead "frío" que requiere contactar al cliente para sacar info.

---

## Campos específicos del módulo

Dentro de la ficha del Lead, en una pestaña adicional llamada **"Detalles de Consulta de Reserva"**, se ven estos campos extra:

### Bloque "Detalles"

| Campo | Significado |
|---|---|
| **Vehículo** | Vehículo concreto solicitado (sólo se permite elegir uno en estado *Disponible*). |
| **Categoría** | Se rellena sola al elegir el vehículo. |
| **Nº de asientos** | Informativo, heredado del vehículo. |
| **Fecha de recogida** | Día de inicio del alquiler. |
| **Fecha de devolución** | Día de fin del alquiler. |
| **Hora de inicio / Hora de fin** | Texto, formato `09:00`. |
| **Contrato** | Sólo aparece si ya se generó el contrato (sólo lectura). |
| **Categoría seleccionada** | La que escogió el cliente en la web. Permite asignar luego cualquier vehículo de esa categoría. |
| **Precio de Reserva** | Lo que pagó el cliente online. |
| **Ubicación de Recogida** | Ciudad/delegación donde recoge. |

### Bloque "Datos de Identificación del Cliente"

| Campo | Significado |
|---|---|
| **DNI / NIE** | Documento de identidad. |
| **Fecha de expiración del DNI** | Para validar que está vigente. |
| **Domicilio** | Dirección completa. |
| **Fecha de nacimiento** | Para determinar si es *conductor especial* (<25 o >60). |
| **Carnet de Conducir** | Número de carnet. |
| **Fecha expedición del carnet** | |
| **Fecha de caducidad del carnet** | |

Cada Lead recibe además un **token de acceso interno** (no se ve en pantalla) usado para construir enlaces seguros del cliente.

---

## Estados del lead

El módulo usa la **pipeline estándar de CRM** de Odoo (Nuevo · Cualificado · Propuesta · Ganado · …) con algunos matices:

| Estado | Comportamiento |
|---|---|
| **Nueva** (primera etapa) | La consulta entra aquí al crearse. Está **bloqueando un hueco** de su categoría. |
| **En cualquier etapa abierta** | Si todavía no tiene contrato, sigue bloqueando hueco. |
| **Ganado** | Deja de bloquear hueco (próximo paso: crear el contrato real). |
| **Perdido** | Libera el hueco. |
| **Descartado / Cancelado** | Libera el hueco. |

> El botón nativo morado **"Ganado"** de Odoo se oculta si la consulta ya tiene vehículo asignado, para forzar el uso del flujo propio del módulo (Convertir a Oportunidad → Crear Contrato) y evitar errores con la fecha de cierre.

---

## Acciones disponibles desde la ficha del Lead

En la cabecera del Lead aparecen botones contextuales según el estado:

| Botón | Cuándo aparece | Qué hace |
|---|---|---|
| **Convertir a Oportunidad** (gris) | Lead de tipo lead, con vehículo asignado, sin contrato | Convierte el lead en oportunidad. Si no hay contacto, lo crea (busca por email, luego teléfono, y si no existe lo da de alta). Si no había vehículo concreto pero sí categoría, **asigna automáticamente el primer vehículo disponible de esa categoría**. Marca probabilidad 100%. |
| **Crear Contrato** (resaltado) | Oportunidad ganada / lead con vehículo, sin contrato todavía | Abre el wizard de conversión a contrato (ver sección siguiente). |
| **Imprimir Contrato** (icono impresora) | Si ya hay contrato | Genera el PDF del contrato. |
| **Enviar Contrato** (icono sobre, verde) | Si ya hay contrato | Envía un email al cliente con el PDF adjunto, usando la plantilla `email_template_vehicle_contract`. Si la plantilla no existe, muestra error. |

---

## Convertir un Lead en Contrato

### Cuándo se usa

Cuando una consulta ya tiene la información mínima (cliente, vehículo o categoría, fechas) y queremos materializarla como **contrato real de alquiler**.

### Paso a paso

1. Abrir la consulta desde **Vehicles Rental → Consultas de Reserva**.
2. Revisar/cualificar los datos del cliente y del vehículo.
3. Si no tiene vehículo concreto asignado, pulsar **Convertir a Oportunidad** — el sistema asigna automáticamente el primer vehículo disponible de la categoría.
4. Pulsar **Crear Contrato**. Se abre el wizard "Crear Contrato de Vehículo".

### Qué pide el wizard

Es una ventana modal con dos bloques:

**Bloque "Información del Cliente y Vehículo"**

| Campo | Notas |
|---|---|
| **Cliente** | Obligatorio. Hereda del Lead. NO se puede crear uno nuevo desde aquí. |
| **Tipo de Vehículo** (Categoría) | Sólo lectura. La que tenía el Lead. |
| **Buscar por Marca/Modelo** | Texto libre, ej. `Mercedes` o `Sprinter`. Filtra los vehículos disponibles. |
| **Vehículo Disponible** | Obligatorio. Desplegable filtrado por estado *Disponible* + misma compañía + misma categoría. Si el Lead ya traía vehículo, se preselecciona; si solo había categoría, se elige el primer disponible. |

**Bloque "Fechas del Alquiler"**

| Campo | Notas |
|---|---|
| **Fecha de Recogida** | Obligatorio. Combina `start_date + start_time` del lead. |
| **Fecha de Devolución** | Obligatorio. Combina `end_date + end_time` del lead. |

### Qué se valida internamente

El wizard busca, **a la sombra**, la **transacción Redsys** asociada al cliente (por email, en las últimas 48h). De esa transacción extrae:

- `selected_price` → precio por día.
- `total_price` → total pagado.
- `num_days` → días reservados.
- Ubicación de recogida.

Si no encuentra precio por día explícito, lo calcula dividiendo el importe total entre los días. También detecta la provincia (state) buscando el nombre dentro de España.

### Qué genera al confirmar (botón "Crear Contrato")

1. **Un Pedido de Venta (`sale.order`)** vinculado al Lead:
   - Compañía del vehículo.
   - Producto `vehicle_rent_charge`.
   - Cantidad = días.
   - Precio unitario = tarifa diaria.
   - Descripción con marca, modelo, matrícula, periodo y nº de días.
   - **Se confirma automáticamente** (estado *sale*) porque el pago ya está realizado.

2. **Un Contrato de Vehículo (`vehicle.contract`)** con:
   - Cliente + teléfono + email.
   - DNI, expiración del DNI, dirección, nº de carnet, fechas de expedición y caducidad (todo copiado del Lead).
   - Vehículo + modelo + año + transmisión + combustible.
   - Fechas de recogida y devolución.
   - Ciudad/provincia/país (basadas en la ubicación de la web).
   - Tarifa por día + tipo `días`.
   - **Motivo de descuento**: `Precio de reserva web` (para evitar la validación que pide motivo si el precio difiere del calculado).
   - Vinculación al Lead (`crm_lead_id`) y al Sale Order (`sale_order_id`).

3. **Una línea de pago Redsys** dentro de los "Detalles de pago del vehículo" del contrato, con:
   - Referencia de la transacción.
   - Fecha de hoy.
   - Importe total pagado.

4. El **Lead queda enlazado al contrato** (`contract_id`). A partir de aquí ya no aparece el botón "Crear Contrato" y sí aparecen "Imprimir Contrato" y "Enviar Contrato".

Al terminar, **se abre directamente la ficha del contrato recién creado**.

---

## Errores y validaciones comunes

| Error | Causa | Solución |
|---|---|---|
| El cliente y/o vehículo está vacío al pulsar Crear Contrato | Faltan datos en el Lead | Rellenarlos manualmente o usar Convertir a Oportunidad. |
| No se crea el Pedido de Venta | No existe el producto `vehicle_rent_charge` en la base | Crear el producto (ver [Configuración](./15-configuracion.md)). El contrato se crea igualmente. |
| Contrato sin precio o sin línea de pago | La transacción Redsys no se encontró (cliente sin email, o > 48h desde el pago) | Ajustar precio a mano en el contrato y registrar manualmente el pago. |
| El botón "Enviar Contrato" da error | La plantilla `email_template_vehicle_contract` no está cargada | Verificar instalación; reinstalar o cargar el dato `data/email_templates.xml`. |

---

## Ejemplos de uso

### Cliente que pagó por web el viernes a las 23:00

> **Caso:** El sábado por la mañana, el operario abre **Consultas de Reserva**.

1. Ve un lead nuevo "Consulta de Reserva - Pedro Sánchez" con etiqueta naranja.
2. Lo abre y revisa: tiene vehículo de la categoría Tipo C, fechas 10-15 de junio, DNI 12345678X, carnet con fecha de caducidad 2031, dirección completa, depósito de 150 € ya pagado.
3. Pulsa **Convertir a Oportunidad** → el sistema asigna un Renault Megane (primer disponible de Tipo C).
4. Pulsa **Crear Contrato** → se abre el wizard precargado, confirma fechas, **Crear**.
5. El contrato sale en estado *Borrador* con todos los datos rellenos y la línea de pago Redsys ya registrada.
6. Revisa, pulsa **In Progress** para activarlo, **Enviar Contrato** para mandar el PDF al cliente, y le concierta la cita de recogida.

### Cliente que llama por teléfono pidiendo presupuesto

> **Caso:** Una empresa quiere alquilar 2 furgonetas durante 3 meses.

1. Recepción abre **CRM → Pipeline → Crear** (lead nuevo).
2. Tipo: **Lead**.
3. Rellena nombre de la empresa, teléfono, email.
4. En la pestaña "Detalles de Consulta de Reserva", indica categoría (Tipo K, Tipo W) y fechas aproximadas.
5. Guarda. Llama al cliente, le pasa presupuesto, el cliente acepta.
6. **Convertir a Oportunidad**, **Crear Contrato** (o, mejor, **Reserva Múltiple** si son varios coches — ver [06 · Reservas](./06-reservas.md#b-reserva-multiple)).

### Consulta web fallida (pago rechazado)

Si Redsys rechaza el pago, el cliente vuelve a `/rental/error` y **NO se crea el lead**. Si quieres rescatar al cliente, mira en **Configuración técnica → Transacciones de pago** y busca su email/teléfono — desde ahí puedes contactarlo manualmente.

---

## Relacionado

- [04 · Clientes](./04-clientes.md) — alta automática del contacto al recibir el lead.
- [06 · Reservas](./06-reservas.md) — el asistente equivalente para reservas manuales sin lead web.
- [07 · Contratos](./07-contratos.md) — el contrato que se genera al convertir el lead.
- [09 · Pagos y Redsys](./09-pagos-y-redsys.md) — la transacción de pago que alimenta al lead.
- [13 · Portal web público](./13-portal-web-publico.md) — el formulario que el cliente rellena.

---

[← Volver al índice](./README.md) · Anterior: [04 · Clientes](./04-clientes.md) · Siguiente: [06 · Reservas →](./06-reservas.md)
