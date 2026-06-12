# 14 · Emails y Reportes

[← Volver al índice](./README.md)

---

## ¿Qué cubre este capítulo?

Los dos canales de **comunicación con el cliente** y de **documentación impresa**:

- **Emails automáticos** que envía el sistema (plantillas).
- **Reportes PDF**: contrato de alquiler, anexo de ampliación, anexo de sustitución, informes de rayones.

---

## A. Emails automáticos

### Plantilla 1 — "Vehicle Rental Booking Mail Template"

**Identificador interno**: `vehicle_rental_booking_mail_template`

| Atributo | Valor |
|---|---|
| **Modelo** | `vehicle.contract` |
| **Asunto** | *"Important: Vehicle Rental Contract Information for Your Upcoming Journey"* |
| **Remitente** | Email de la compañía del contrato |
| **Destinatario** | El cliente (`customer_id`) |
| **Auto delete** | Sí (se borra del log tras enviar) |

#### Contenido del email

Email HTML formal que incluye:

- **Saludo** al cliente por nombre.
- **Tabla con los datos de la reserva**:
  - Número de referencia.
  - Vehículo.
  - Fecha y dirección de recogida (calle, ciudad, provincia, CP, país).
  - Fecha y dirección de devolución (mismo desglose).
- **Recordatorios**:
  - Revisar términos y condiciones (con enlace a la web de la compañía).
  - Detalles sobre seguro, depósito reembolsable, revisión visual del vehículo antes del viaje.
- **Datos de contacto** de la compañía (nombre, teléfono, email, web).
- **Firma** con el *Responsable* del contrato.

#### Disparador típico

**Confirmación de reserva**, automática cuando se pulsa **In Progress** en el contrato:

- El botón *In Progress* del contrato abre el compositor de email con esta plantilla ya cargada.
- El operario revisa y envía.

---

### Plantilla 2 — "Contrato de Vehículo"

**Identificador interno**: `email_template_vehicle_contract`

| Atributo | Valor |
|---|---|
| **Modelo** | `vehicle.contract` |
| **Asunto** | *"Contrato de Alquiler de Vehículo"* |
| **Idioma** | Español |
| **Auto delete** | No (queda registro en el historial del contrato) |

#### Contenido del email

Email para enviar el **PDF del contrato** al cliente. Incluye:

- Encabezado *"Estimado/a [nombre o 'cliente']"*.
- *"Adjunto encontrará el contrato de alquiler de vehículo correspondiente a su reserva."*
- **Detalles de la Reserva**:
  - Número de Contrato.
  - Vehículo.
  - Modelo.
  - Fecha de Inicio.
  - Fecha de Fin.
  - Total Alquiler en euros.
- Petición de revisar términos y condiciones.
- Despedida y firma del *"equipo de SUNSET RENT"*.
- Aviso de mensaje automático al pie.

#### Disparador típico

Cuando el operario pulsa el botón **Enviar Contrato** (icono sobre, verde) en la ficha del contrato.

---

### Plantilla 3 — Anexo de ampliación

El **anexo de ampliación** se envía al cliente al pulsar **Enviar a firmar** desde una [Ampliación de contrato](./07-contratos.md#ampliacion-de-contrato):

- Si **Odoo Sign está instalado**, se abre el wizard de Sign → se envía un email automático con el anexo PDF para firmar online.
- Si Sign no está instalado, se marca como *Enviado* y se debe enviar manualmente por otro canal.

---

### Otros emails del flujo

El sistema también dispara correos apoyándose en plantillas estándar de Odoo:

| Email | Origen | Cuándo |
|---|---|---|
| **Acuse de envío del formulario web de contacto** | Plantilla estándar CRM | El cliente envía `/contactus/enviar`. Se crea un lead y se notifica opcionalmente. |
| **Factura al cliente** | Plantilla estándar de Customer Invoice | Cuando se crea una factura y se pulsa *Enviar e Imprimir*. Incluye PDF adjunto. |
| **Enlaces de pago / pasarela** | Plantillas de payment providers (Redsys) | Cuando el cliente paga la reserva online. |
| **Recordatorios de factura vencida** | Estándar Odoo | Cuando una factura está fuera de plazo. |
| **Acceso al portal** | Estándar Odoo | Cuando el operario concede acceso al portal a un cliente. |

---

### Cómo personalizar una plantilla

1. **Activar modo desarrollador** en Odoo (Ajustes → Activar Modo Desarrollador).
2. **Configuración → Técnico → Email → Plantillas**.
3. Buscar la plantilla por nombre.
4. Editar el cuerpo HTML, el asunto, el idioma o el destinatario.
5. Guardar.

> Las plantillas usan **variables QWeb** (`{{object.customer_id.name}}`, etc.). No modifiques las variables si no sabes lo que haces — puede romper el envío.

---

### Notificaciones internas en el chatter

Independientemente del email al cliente, el contrato muestra eventos en el **chatter** (panel inferior del formulario):

- *"💰 Factura Pagada — La factura X por importe Y ha sido pagada completamente"* — cuando una factura asociada al contrato pasa a *Paid*.
- Tabla de detalles cuando se produce una **sustitución de vehículo** (fecha, motivo, vehículo anterior + sustituto, kilometraje, diferencia de precio, enlace al addendum).
- Mensajes de cambio de estado del contrato.
- Adjuntos PDF (contrato impreso, anexos generados).

---

## B. Reportes PDF

### Reporte 1 — Contrato de Alquiler

**Identificador**: `vehicle_rental.vehicle_contract_report`

**Acción de informe** vinculada al modelo `vehicle.contract`.

#### Cómo se genera

Tres maneras:

1. **Botón "Imprimir Contrato"** en la cabecera del contrato (icono impresora) — descarga el PDF.
2. **Botón "Enviar Contrato"** (icono sobre, verde) — genera el PDF, lo guarda como adjunto `Contrato_<REF>.pdf` y lo manda por email.
3. **Menú "Print" estándar de Odoo** desde la propia ficha del contrato.

#### Contenido del PDF

- **Título**: *"CONTRATO DE ALQUILER DE VEHÍCULO"*.
- **Cabecera**: *"SUNSET RENT - Alquiler de Vehículos"*.
- **Datos del Cliente**:
  - Nombre, email, teléfono.
  - DNI/NIE y fecha de expiración.
  - Domicilio.
  - Carnet de conducir, fecha de expedición y de caducidad.
- **Datos del Vehículo**:
  - Vehículo (marca/modelo).
  - Modelo.
  - Categoría.
  - Matrícula.
  - Demás datos de flota.
- **Fechas de recogida y devolución** + direcciones completas.
- **Tarifa y cálculo del total** (precio/día × días + extras + seguro − descuentos = total).
- **Condiciones de alquiler** (los *Terms & Conditions* del contrato).
- **Política de cancelación** si aplica.
- **Firma del cliente y de la empresa** al pie.

---

### Reporte 2 — Anexo de Ampliación

**Identificador**: `action_report_vehicle_contract_extension_addendum`

#### Cómo se genera

Desde una [Ampliación de contrato](./07-contratos.md#ampliacion-de-contrato):

- **Botón "Enviar a firmar"** — lo manda como documento de Sign con el cliente como firmante.
- **Acción de impresión estándar** desde la ficha de la ampliación.

#### Contenido

- Referencia del contrato original.
- Datos del cliente.
- **Fecha de fin original**.
- **Nueva fecha de fin**.
- **Días de ampliación**.
- **Tarifa por día**.
- **Importe ampliación** (días × tarifa).
- Firmas.

Se anexa al contrato original como prueba de la ampliación.

---

### Reporte 3 — Anexo de Sustitución

**Identificador**: `action_report_vehicle_substitution_addendum`

#### Cómo se genera

- **Automáticamente** al confirmar el wizard de [sustitución de vehículo](./10-mantenimiento-y-sustituciones.md#e-sustituciones) si está marcado el checkbox *Generar Addendum al Contrato*.
- **Manualmente** desde la ficha de la sustitución → botón **Generar Addendum**.

#### Contenido

- Cabecera con datos de la empresa y del contrato.
- **Datos del cliente** (nombre, DNI).
- **Tabla del vehículo anterior**:
  - Nombre, matrícula.
  - Fecha de sustitución.
  - Km de devolución.
  - Nivel de combustible.
  - Daños (con descripción e imagen pintada).
- **Tabla del vehículo sustituto**:
  - Nombre, matrícula.
  - Km de entrega.
  - Combustible.
  - Daños previos (si los hay).
- **Motivo y notas** de la sustitución.
- **Diferencia de precio** si aplica.
- **Firmas** del cliente y de la empresa.

Queda **adjunto al chatter** del contrato y de la sustitución.

---

### Reporte 4 — Informe de Rayones (Scratch Report)

Aunque el modelo `vehicle.scratch.report` es principalmente un **registro fotográfico**, también se puede imprimir un informe individual.

#### Cómo se genera

Desde la ficha del scratch report → menú **Print**.

#### Contenido

- Nombre del informe.
- Vehículo.
- Fotografía a tamaño 250 × 250.
- Compañía.

---

## C. Tareas programadas (ir.cron)

El módulo registra **dos procesos automáticos** en *Configuración → Técnico → Acciones Planificadas* (ambos **activos por defecto**, ejecutándose **una vez al día**):

### Cron 1 — Vehicle Rental: Vehicle Contract Invoice

| Atributo | Valor |
|---|---|
| **Modelo** | `vehicle.contract` |
| **Acción** | `model.action_create_rent_payment_invoice()` |
| **Frecuencia** | Cada 1 día |

**Función**: revisa los contratos en curso y genera de forma automática las **facturas periódicas del alquiler** (cuotas que vencen hoy según el *Payment Type*).

Esto evita que un operario tenga que crear manualmente la factura cada mes/periodo.

### Cron 2 — Vehicle Rental: Vehicle Maintenance Schedule

| Atributo | Valor |
|---|---|
| **Modelo** | `maintenance.request` |
| **Acción** | `model.action_create_schedule_maintenance()` |
| **Frecuencia** | Cada 1 día |

**Función**: para cada solicitud de mantenimiento existente comprueba si hoy coincide con su *Fecha Próximo Mantenimiento*. Si coincide:

1. Calcula la siguiente fecha sumando los días del horario configurado.
2. Crea automáticamente una **nueva solicitud de mantenimiento** para el vehículo.
3. Recalibra la siguiente fecha en función del nuevo registro.

### Cron 3 — Cron de Redsys

(No incluido en `data/ir_cron.xml` sino dentro del propio módulo de pagos)

**Función**: cada **30 minutos** revisa las transacciones de Redsys de las últimas 24 horas que están en `done` pero a las que les falta el lead, e intenta crearlo automáticamente.

Evita pérdidas si fallara el webhook puntualmente.

---

## D. Resumen rápido de comunicaciones

| Tarea | Cómo se hace |
|---|---|
| Enviar email de confirmación de reserva | Botón **In Progress** del contrato lanza el compositor con la plantilla cargada |
| Enviar el PDF del contrato al cliente | Botón **Enviar Contrato** (icono sobre) |
| Imprimir el PDF del contrato | Botón **Imprimir Contrato** (icono impresora) o Print del menú |
| Enviar enlace de pago al cliente | Abrir la factura → **Enviar e Imprimir** |
| Enviar anexo de ampliación a firmar | Botón **Enviar a firmar** en la ampliación |
| Generar anexo de sustitución | Automático al confirmar el wizard, o botón **Generar Addendum** |
| Imprimir informe de rayones | Menú **Print** desde la ficha del scratch report |

---

## Validaciones y errores

| Síntoma | Causa | Solución |
|---|---|---|
| El botón *Enviar Contrato* da error | Plantilla `email_template_vehicle_contract` no cargada | Reinstalar/cargar `data/email_templates.xml`. |
| El email no llega al cliente | Servidor de correo mal configurado | Configurar SMTP en *Ajustes → Servidores de correo*. |
| El PDF sale en blanco o sin estilos | wkhtmltopdf no instalado o mal configurado | Instalar wkhtmltopdf en el servidor. |
| Las cuotas no se facturan solas | El cron está desactivado o la fecha de la cuota es futura | Revisar *Configuración → Técnico → Acciones Planificadas*. |
| Las nuevas órdenes de mantenimiento no aparecen | El vehículo no tiene *Horario de Mantenimiento* asignado | Asignar uno desde la ficha del vehículo. |

---

## Relacionado

- [07 · Contratos](./07-contratos.md) — botones *Enviar Contrato* e *Imprimir Contrato*.
- [09 · Pagos y Redsys](./09-pagos-y-redsys.md) — flujo de email con enlace de pago.
- [10 · Mantenimiento y sustituciones](./10-mantenimiento-y-sustituciones.md) — anexo de sustitución.
- [11 · Daños y rayones](./11-danos-y-rayones.md) — informe de rayones.
- [15 · Configuración](./15-configuracion.md) — políticas, términos y secuencias que alimentan los PDFs.

---

[← Volver al índice](./README.md) · Anterior: [13 · Portal web público](./13-portal-web-publico.md) · Siguiente: [15 · Configuración →](./15-configuracion.md)
