# 09 · Pagos y Redsys

[← Volver al índice](./README.md)

---

## ¿Qué cubre este capítulo?

Cómo se cobra al cliente:

- **Formas de pago** disponibles (cuotas, pago único).
- **Pasarela Redsys** integrada con Odoo (cobro online con tarjeta).
- **Enlaces de pago** que se mandan al cliente.
- **Seguimiento de pagos** y reconciliación contable.

---

## Formas de pago

Las **modalidades de cobro** están explicadas con detalle en [08 · Tarifas, seguros y depósitos · Opciones de pago](./08-tarifas-seguros-depositos.md#e-opciones-de-pago).

Resumen rápido del campo **Tipo de Pago** del contrato:

| Modalidad | Significado |
|---|---|
| **Daily** | Una cuota por día. |
| **Weekly** | Una cuota por semana. |
| **Monthly** | Una cuota por mes. |
| **Quarterly** | Una cuota por trimestre. |
| **Yearly** | Una cuota por año. |
| **Full Payment** | Una sola cuota por el total. |

Una vez activado el contrato con el botón **In Progress**, se pulsa **Create Installment** y se genera el calendario de cuotas según el Tipo de Pago elegido.

---

## Pasarela Redsys

### ¿Para qué se usa?

Cobrar **online al cliente** desde la web pública (reserva por internet) y desde el portal del cliente cuando se le envía un enlace de pago. La pasarela está integrada con el módulo oficial `payment_redsys` de Odoo.

### Cómo se configura

La configuración real de credenciales (Merchant Code, Terminal, Clave secreta de Redsys, entorno producción/test, URLs de retorno) se hace desde el **menú estándar de Odoo**:

**Ajustes → Pasarelas de Pago → Redsys**

En este módulo, lo único añadido es:

- Lógica para **crear automáticamente la oportunidad CRM** y la **factura del depósito** cuando el pago termina con éxito.
- **Webhook propio** para confirmar la transacción y desbloquear estados.
- Páginas de éxito y error con URL `/rental/success` y `/rental/error`.

---

## Flujo completo del pago online (reserva web)

### Paso 1 — El cliente rellena la reserva

En la web pública, en la página de detalle del vehículo (`/web/vehicle-detail/<id>`), el cliente:

1. Selecciona tipo de alquiler (oferta fija o tarifa dinámica).
2. Elige fechas, ubicación.
3. Rellena el formulario: nombre, email, teléfono, DNI/NIE, fecha de expiración del DNI, dirección, fecha de nacimiento, carnet de conducir, fechas de expedición y caducidad.
4. Introduce el **número de tarjeta** (sólo los 6 primeros dígitos / BIN son lo que ve el sistema).
5. Selecciona el **tipo de tarjeta** (débito/crédito) — automatizado por BIN.

### Paso 2 — Validación BIN y cálculo de depósito

JavaScript valida el BIN contra **Freebinchecker** (`https://lookup.binlist.net/{BIN}`):

- Autocompleta el tipo de tarjeta (Visa, Mastercard, Visa Débito, etc.).
- Recalcula el **depósito** en pantalla según las [reglas de depósito](./08-tarifas-seguros-depositos.md#c-reglas-de-deposito).
- Muestra el **total a pagar** (alquiler + depósito).

### Paso 3 — Envío del formulario

El cliente pulsa enviar. Odoo crea una `payment.transaction` con:

- **Importe** = alquiler + depósito.
- **Proveedor** = Redsys.
- **Referencia única** tipo `RENT-1716728400` (timestamp).
- **Datos de la reserva** guardados en `booking_data_json` y en sesión.

### Paso 4 — Redirección al TPV de Redsys

URL: `/payment/process/{tx_id}` → el cliente paga con su tarjeta en la pantalla de Redsys.

### Paso 5 — Redsys notifica el resultado

Por **dos canales** independientes:

#### Canal 1 — Webhook server-to-server (`/payment/redsys/webhook`)

Redsys envía `Ds_Order` y `Ds_Response`:

- Si `Ds_Response = 0000` (éxito) y la transacción está en `error`, `pending` o `draft` → se fuerza a `done`.
- Si el método estándar falla, hace fallback a actualización directa en BD.

#### Canal 2 — Redirección del usuario

- A `/rental/success` (éxito).
- A `/rental/error` (fallo).

### Paso 6 — Al confirmar el pago (`done`)

El sistema, de forma **idempotente** (no se duplica si llega dos veces):

1. **Contacto** — busca o crea `res.partner` por email o teléfono. Actualiza dirección.
2. **Verifica disponibilidad** — comprueba que haya vehículos disponibles en la categoría reservada. Si no hay → error y aviso de **reembolso automático**.
3. **Crea CRM Lead** (`Consulta de Reserva - {cliente}`) en etapa 1 con:
   - Fechas y horas de recogida/entrega.
   - Categoría seleccionada.
   - DNI, fecha de caducidad DNI.
   - Carnet de conducir + fechas expedición y caducidad.
   - Fecha de nacimiento.
   - Dirección completa.
   - Resumen económico: precio alquiler, depósito, total pagado, tipo de tarjeta, BIN.
4. **Crea factura del depósito** — la concilia contra el diario bancario de Redsys y la deja como **Pagada**.
5. Marca la transacción como `booking_created = True` para evitar duplicación.
6. Limpia la sesión del booking.

### Paso 7 — Crear el contrato (manual)

**El contrato NO se crea automáticamente** al pagar. Se crea cuando el comercial:

1. Abre el Lead en **Consultas de Reserva**.
2. Pulsa **Convertir a Oportunidad**.
3. Pulsa **Crear Contrato**.

Ver [05 · Consultas web y leads](./05-consultas-web-y-leads.md) para el detalle.

---

## Páginas de retorno

### Página de éxito `/rental/success`

- Muestra una pantalla simple verde: *"✓ ¡Pago realizado exitosamente! Tu reserva ha sido confirmada. Serás redirigido en breve…"*.
- Redirige al home a los **3 segundos**.
- En segundo plano confirma:
  - Lee `booking_data` de la sesión.
  - Busca la última transacción Redsys con `booking_created = False`.
  - La fuerza a `done` si no lo está y dispara la creación del lead.

### Página de error `/rental/error`

- HTML simple con mensaje rojo: *"✗ El pago ha sido cancelado o ha fallado"*.
- Enlace para *"Volver a reservar"*.
- Redirección al home en 5 segundos.

---

## Estados de la transacción

| Estado | Significado |
|---|---|
| **draft** | Recién creada, sin enviar a Redsys. |
| **pending** | Pendiente de respuesta del TPV. |
| **authorized** | Autorizada por el banco, sin capturar. |
| **done** | Pago capturado y confirmado. |
| **error** | Rechazo o fallo. |
| **cancel** | Cancelada por el cliente. |

Sobre el Lead/factura, el estado relevante es **`booking_created`**: `True` cuando ya se generó la oportunidad CRM tras el pago.

---

## Gestión de errores y reintentos

### Errores frecuentes

- Errores se registran con prefijo `REDSYS:` en los logs de Odoo.
- Si falta algún dato obligatorio del booking → `ValidationError` y aviso de reembolso.
- Si no hay vehículos disponibles en la categoría → `ValidationError` y se sugiere reembolso automático.
- Si la creación de la factura de depósito falla, el flujo continúa (el lead se crea igualmente) y se anota un warning.

### Endpoint manual de pruebas

`/rental/manual-webhook?order_number=XXX`

Permite **disparar a mano** el procesamiento de una transacción para testing. Sólo administradores deberían acceder.

### Cron de recuperación

Hay un **trabajo programado** (`_cron_process_orphaned_transactions`) que cada **30 minutos** revisa las transacciones de Redsys de las últimas 24 horas que están en `done` pero a las que les falta el lead, e intenta crearlo automáticamente.

Esto evita pérdidas si fallara el webhook puntualmente.

---

## Enlaces de pago al cliente

### Cómo se le envía un enlace de pago

El módulo `payment_redsys` y el sistema estándar de Odoo permiten **generar enlaces de pago** para cobros puntuales:

1. Desde la factura del contrato (cuota o factura general), abrir la factura.
2. Pulsar **"Enviar e Imprimir"** (botón estándar de Odoo).
3. Odoo prepara un email al cliente que incluye:
   - **PDF de la factura** adjunto.
   - **Enlace al portal del cliente** donde puede ver la factura.
   - **Botón "Pagar Ahora"** en el portal que abre el formulario de pago con Redsys.
4. El cliente entra al portal, elige Redsys, paga en el TPV, y al volver se le marca la factura como **Pagada**.

### Cómo se marca la factura como pagada automáticamente

Cuando llega la confirmación de Redsys (webhook o regreso del usuario), el módulo:

1. Crea un `account.payment` por el importe de la transacción.
2. Lo postea (`action_post`).
3. Llama a `register_payment` sobre la factura para conciliarlo.
4. Si fallara, hace **reconciliación manual** vinculando la línea contable del pago con la línea de cobro de la factura.

El estado **Payment State** de la factura pasa a **Pagado** y se ve reflejado en:

- La propia factura.
- La cuota (`vehicle.payment.option`) ligada a esa factura.
- El campo **Estado del Depósito** (`deposit_payment_state`) si era el depósito.
- El campo **Estado Devolución Depósito** (`return_deposit_state`) si era la devolución.

---

## Comunicación por email

| Email | Cuándo se envía | Disparador |
|---|---|---|
| **Confirmación de reserva tras pago** | Después del pago exitoso por web | Automático tras Redsys (estándar Odoo Mail). |
| **Notificación interna** al comercial | Cuando se crea el Lead desde una reserva web | Automático. |
| **Envío del contrato al cliente** | Cuando el operario pulsa *Enviar Contrato* en el contrato | Manual con plantilla `email_template_vehicle_contract`. |
| **Factura por email** | Cuando el operario pulsa *Enviar e Imprimir* en la factura | Manual con plantilla estándar de factura. |
| **Recordatorios de factura vencida** | Cuando una factura está fuera de plazo | Estándar Odoo. |

Ver el detalle de plantillas en [14 · Emails y reportes](./14-emails-y-reportes.md).

---

## Modos de cobro habituales

### Cliente que reserva por web (la mayoría)

1. Paga el **depósito + alquiler** completo online con tarjeta.
2. Lead aparece en el sistema con `booking_created = True`.
3. Operario crea el contrato; ya está todo cobrado.
4. Al devolver el coche, se gestiona la **devolución del depósito** (ver [12 · Devolución y facturación final](./12-devolucion-y-facturacion.md)).

### Cliente presencial que paga en efectivo / transferencia

1. Recepción crea el contrato manualmente con [Reservar Vehículo](./06-reservas.md).
2. Pulsa **Create Installment** → genera cuotas.
3. Pulsa **Create Invoice** en cada cuota → emite la factura.
4. Cobra al cliente manualmente (en efectivo o por transferencia) → registra el pago en la factura desde Contabilidad.

### Cliente que paga después (a crédito)

1. Recepción crea el contrato y la factura.
2. Manda la factura por email con **Enviar e Imprimir**.
3. El cliente recibe el email con el enlace al portal.
4. Paga en el portal con Redsys (o por transferencia, lo que prefiera).
5. La factura se marca como pagada automáticamente al confirmarse el pago.

---

## Validaciones y restricciones

- **Provider Redsys mal configurado** → el botón *Pagar* en el portal no aparece o sale error de conexión. Verificar credenciales en *Ajustes → Pasarelas de Pago → Redsys*.
- **BIN inválido** → la web pública avisa al cliente *"Tarjeta no reconocida"* y bloquea el envío.
- **Pago duplicado** → si el webhook llega dos veces, el sistema detecta `booking_created = True` y NO duplica el lead/factura.
- **Tarjeta declinada** → cliente vuelve a `/rental/error`, no se crea nada en el sistema.

---

## Relacionado

- [05 · Consultas web y leads](./05-consultas-web-y-leads.md) — qué se crea automáticamente al recibir un pago web.
- [08 · Tarifas, seguros y depósitos](./08-tarifas-seguros-depositos.md) — cómo se calcula el depósito que se cobra.
- [12 · Devolución y facturación final](./12-devolucion-y-facturacion.md) — cómo se devuelve el depósito al cliente.
- [13 · Portal web público](./13-portal-web-publico.md) — el formulario que el cliente rellena.
- [14 · Emails y reportes](./14-emails-y-reportes.md) — plantillas de email.
- [16 · Solución de problemas](./16-solucion-de-problemas.md) — qué hacer si un pago no llega bien.

---

[← Volver al índice](./README.md) · Anterior: [08 · Tarifas, seguros y depósitos](./08-tarifas-seguros-depositos.md) · Siguiente: [10 · Mantenimiento y sustituciones →](./10-mantenimiento-y-sustituciones.md)
