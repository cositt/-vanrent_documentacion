# 16 · Solución de Problemas

[← Volver al índice](./README.md)

---

## Cómo usar este capítulo

Cuando algo no funciona, busca el síntoma en la tabla. Cada caso indica **causa, comprobaciones y solución**.

Si el problema es muy específico, mira también el capítulo correspondiente:

- Reservas/leads → [05](./05-consultas-web-y-leads.md) / [06](./06-reservas.md).
- Contratos → [07](./07-contratos.md).
- Pagos → [09](./09-pagos-y-redsys.md).
- Daños → [11](./11-danos-y-rayones.md).
- Devolución → [12](./12-devolucion-y-facturacion.md).
- Portal web → [13](./13-portal-web-publico.md).
- Emails/PDFs → [14](./14-emails-y-reportes.md).

---

## A. Problemas con contratos

### El botón "In Progress" no aparece

**Causa**: el contrato no está en estado *Borrador*.

**Solución**: sólo desde *Borrador* se puede activar. Si está en otro estado, no se puede volver atrás — duplica el contrato si necesitas reactivar.

---

### "No se puede activar este contrato porque las fechas se solapan con otro(s) contrato(s) en curso para el mismo vehículo: REF1 (dd/mm/yyyy → dd/mm/yyyy)..."

**Causa**: ya existe otro contrato *En Progreso* o *Devuelto* del mismo vehículo en el mismo rango de fechas.

**Solución**:
1. Anotar la referencia del contrato conflictivo.
2. Abrirlo y revisar las fechas reales.
3. Si fue un error, cancelar o ajustar uno de los dos.
4. Si las fechas son correctas, **el vehículo está físicamente ocupado** — usar otro vehículo.

---

### "Drop-off Date debe ser mayor"

**Causa**: la fecha de devolución es anterior o igual a la de recogida.

**Solución**: corregir las fechas. Si son del mismo día, asegurarse de que la hora de devolución es posterior a la de recogida.

---

### "Elija su unidad de alquiler preferida" al activar

**Causa**: el campo *Tipo de Alquiler* (`rent_type`) está vacío.

**Solución**: en el bloque *Rent Details*, elegir el tipo (Días/Semanas/Meses/Horas/Años/KM/Millas).

---

### "Debe definir las fechas de recogida y devolución antes de activar el contrato"

**Causa**: falta una de las dos fechas.

**Solución**: rellenar *Pick-up Date* y *Drop-off Date*.

---

### "The Rent per Day/Week/... must be greater than zero"

**Causa**: el campo *Precio del alquiler* está a 0.

**Solución**:
1. Comprobar que el vehículo tiene categoría asignada.
2. Comprobar que hay una **tarifa vigente** para esa categoría + duración + km. Si no, crear una en *Tarifas de Vehículos*.
3. Si no se encuentra tarifa, introducir el precio a mano y rellenar **Motivo del Descuento**.

---

### "El precio calculado automáticamente es X €/día pero el precio actual es Y €/día. Si esto es intencional, indica el motivo en Motivo del Descuento"

**Causa**: editaste el precio y el sistema detecta que difiere del calculado por las reglas.

**Solución**: rellenar el campo **Motivo del Descuento** (texto libre) en el contrato. Ejemplos: *"Cliente VIP"*, *"Promoción navidad"*, *"Precio web"*.

---

### "El vehículo seleccionado no tiene una categoría asignada"

**Causa**: el modelo del vehículo no tiene categoría.

**Solución**: ir a *Flota → Modelos de vehículos*, abrir el modelo y asignarle una categoría.

---

### "Error: Campos no válidos: Recoger ciudad, Ciudad de entrega"

**Causa**: el contrato se intenta confirmar sin completar las ciudades de recogida y entrega.

**Solución**: editar el contrato en estado *Borrador*, rellenar ambas ciudades en la sección *Detalles de Recogida y Devolución*.

---

### "No se pueden crear cuotas"

**Causa**: faltan campos obligatorios para generar el calendario.

**Solución**: verificar que están rellenos:
- Tipo de Alquiler.
- Fechas de inicio y fin.
- Renta > 0.
- Tipo de Pago.

---

### El botón "Create Installment" no aparece

**Causa**: o no estás en *En Progreso*, o ya hay cuotas creadas.

**Solución**: si ya hay cuotas, ir a la pestaña *Vehicle Payment Details* para verlas. Si no, primero pasar a *En Progreso*.

---

### "Esta ampliación ya tiene factura creada"

**Causa**: ya pulsaste *Crear factura de ampliación* anteriormente.

**Solución**: ir a la factura existente con el botón *Ver factura*. Si quieres anularla, va por Contabilidad.

---

### "La nueva fecha de fin debe ser posterior a la fecha de fin original del contrato"

**Causa**: en una ampliación, has puesto una fecha igual o anterior a la original.

**Solución**: poner una fecha posterior a la actual de fin del contrato.

---

## B. Problemas con la flota

### No puedo pasar el vehículo a "Under Maintenance"

**Causa**: tiene un contrato activo.

**Solución**: devolver primero el contrato (botón *Return* en el contrato) o sustituir el vehículo en ese contrato.

---

### El odómetro no se actualiza

**Causa**: el botón *Update Vehicle Data* exige odómetro nuevo > odómetro actual.

**Solución**: introducir un valor superior al actual del vehículo en flota.

---

### Un vehículo no aparece en el asistente de reserva

**Causas posibles**:
1. Está en estado *En Mantenimiento*.
2. Tiene un contrato solapado en las fechas seleccionadas.
3. Su categoría no coincide con la elegida en el wizard.
4. Es de otra compañía.

**Solución**: comprobar estado, contratos solapados (pestaña *Rental Contracts* del vehículo), categoría y compañía.

---

## C. Problemas con tarifas y depósitos

### No se aplica tarifa automática

**Causas posibles**:
1. No hay regla de tarifa vigente para la categoría + duración + km.
2. La regla está archivada (inactiva).
3. La regla está fuera de su rango *Válido Desde / Hasta*.
4. El vehículo no tiene categoría.

**Solución**: ir a *Tarifas de Vehículos*, quitar los filtros automáticos (*Activas + Vigentes Hoy*) para ver todas. Crear o ajustar la regla.

---

### Depósito calculado a 0 €

**Causas posibles**:
1. No hay regla de depósito para esa categoría + tipo de tarjeta.
2. El campo *Tipo de Tarjeta para Depósito* no está rellenado en el contrato.
3. La casilla *Usar Depósito de Regla* no está marcada y no hay depósito manual.

**Solución**: revisar *Reglas de Depósitos* y rellenar el tipo de tarjeta en el contrato.

---

### El seguro sale demasiado caro

**Causa**: la casilla *Conductor Especial (-25 o +60 años)* está marcada y eleva el precio.

**Solución**: verificar la fecha de nacimiento del cliente. Si no aplica, desmarcar el checkbox.

---

## D. Problemas con facturas

### "If you don't define a journal of type 'Sales' in this company..."

**Causa**: no hay diario de ventas configurado.

**Solución**: ir a *Contabilidad → Configuración → Diarios* y crear uno con tipo *Sales*.

---

### La factura no se publica automáticamente

**Causas posibles**:
1. La factura tiene impuestos mal configurados.
2. El cliente no tiene los campos obligatorios completos.
3. Falta una cuenta contable en el producto.

**Solución**: abrir la factura en *borrador*, completar lo que falta, pulsar *Confirmar*.

---

### El cron de facturación de cuotas no se ejecuta

**Causas posibles**:
1. El cron está desactivado.
2. El servidor Odoo no está ejecutando crons (proceso `odoo-cron` parado).
3. La fecha de la cuota es futura.

**Solución**:
1. *Configuración → Técnico → Acciones Planificadas* → buscar "Vehicle Contract Invoice" y activarlo.
2. Verificar que el servidor está corriendo en modo con `-c odoo.conf` con workers.
3. Esperar a que llegue la fecha.

---

## E. Problemas con Redsys / pagos online

### Cliente paga pero no aparece el lead

**Causas posibles**:
1. El webhook de Redsys falló (problema de red entre Redsys y tu servidor).
2. La URL del webhook está mal configurada.
3. El cliente cerró la página antes de volver a `/rental/success`.

**Solución**:
1. Esperar 30 minutos: el **cron de recuperación** revisa transacciones huérfanas y crea el lead.
2. Si no ocurre, ir a *Configuración técnica → Transacciones de pago*, buscar la transacción del cliente y verificar su estado.
3. Si está en `done` pero sin lead, lanzar manualmente `/rental/manual-webhook?order_number=XXX` (sólo administradores).
4. Revisar logs con prefijo `REDSYS:`.

---

### "Tarjeta no reconocida" en el formulario web

**Causa**: el servicio Freebinchecker no reconoce el BIN (tarjeta extranjera o nueva).

**Solución**: el sistema asume *débito* por defecto. El cliente puede continuar — sólo afecta al cálculo del depósito.

---

### El TPV de Redsys da error genérico

**Causas posibles**:
1. Credenciales mal configuradas (Merchant Code, Terminal, Clave).
2. Entorno *test* vs *producción* mezclado.
3. URL de retorno mal puesta en el panel de Redsys.

**Solución**: revisar *Ajustes → Pasarelas de Pago → Redsys* y contrastar con la documentación de tu banco/TPV.

---

### Factura de depósito no se concilia

**Causa**: el diario bancario de Redsys no está bien configurado.

**Solución**: *Contabilidad → Configuración → Diarios* — verificar que existe un diario tipo *Banco* asociado a Redsys.

---

## F. Problemas con emails

### El email "Enviar Contrato" da error

**Causa**: la plantilla `email_template_vehicle_contract` no está cargada.

**Solución**:
1. *Configuración → Técnico → Email → Plantillas* — buscarla.
2. Si no existe, **actualizar el módulo** desde *Apps → Vehicle Rental → Actualizar*.
3. Esto recarga `data/email_templates.xml`.

---

### Los emails no llegan al cliente

**Causa**: servidor de correo SMTP mal configurado o saturado.

**Solución**:
1. *Ajustes → Servidores de correo* — verificar configuración.
2. *Configuración → Técnico → Email → Emails* — ver cola; si están en *Failed*, reintentar.
3. Comprobar que el dominio remitente está autorizado (SPF, DKIM).

---

### Falla el formulario de contacto en `/contactus/enviar`

**Causa**: faltan campos obligatorios.

**Solución**: el formulario muestra *"Faltan campos obligatorios..."*. Verificar nombre, email, teléfono y mensaje.

---

## G. Problemas con el PDF del contrato

### El PDF sale en blanco o sin estilos

**Causa**: wkhtmltopdf no está instalado o tiene versión incompatible.

**Solución**: instalar `wkhtmltopdf 0.12.5` (versión recomendada por Odoo) en el servidor:

```bash
# Ubuntu/Debian
sudo apt install wkhtmltopdf
```

---

### El daño pintado no aparece en el PDF

**Causa**: el PDF se generó **antes** de pintar el daño.

**Solución**: volver a imprimir el PDF (botón *Imprimir Contrato* o *Enviar Contrato*).

---

## H. Problemas con la web pública

### "No hay vehículos disponibles" para todas las fechas

**Causas posibles**:
1. Realmente no hay vehículos de esa categoría libres.
2. Todos los vehículos están en *En Mantenimiento*.
3. Hay otras consultas web abiertas (`leads` sin contrato) bloqueando los huecos.

**Solución**:
1. *Disponibilidad* del panel para ver ocupación visual.
2. *Consultas de Reserva* para ver leads abiertos: convertir a contrato o descartar los antiguos.

---

### Una categoría no aparece en `/web/booking-enquiry`

**Causa**: está en la lista de **categorías excluidas** del controller:

- Furgoneta
- Tipo E - Bici Eléctrica
- Tipo E
- Bici Eléctrica

**Solución**: requiere modificación del código del controller. Tarea técnica.

---

### La página de Pinveco da 404 para una delegación

**Causa**: sólo Madrid, Barcelona y Valencia están configuradas en el código.

**Solución**: requiere modificación del controller `pinveco_home_controller.py` para añadir más delegaciones.

---

## I. Problemas con traducciones

### Las traducciones no se aplican

**Causa**: caché de traducciones de Odoo.

**Solución**:
1. *Ajustes → Traducciones → Cargar/Actualizar traducciones*.
2. Reiniciar el servidor Odoo.
3. Limpiar caché del navegador (Ctrl+Shift+R).

---

### Algunos textos siguen en inglés

**Causa**: cadenas no traducidas en el código del módulo, o usuario con idioma erróneo.

**Solución**:
1. *Preferencias del usuario → Idioma → Español*.
2. Si la cadena no existe en español, contactar a desarrollo para añadirla a `i18n/es.po`.

---

## J. Buenas prácticas

### Antes de pulsar "In Progress"

Verificar que todos estos campos están bien:

- Cliente con email, teléfono, DNI, carnet.
- Vehículo correcto.
- Fechas (hora incluida).
- Direcciones de recogida y devolución.
- Tipo de alquiler y precio.
- Seguro elegido.
- Depósito configurado.
- Documentos del cliente subidos.

### Antes de devolver un vehículo

1. **Inspeccionar el coche** físicamente: exterior, interior, depósito de combustible, kilometraje.
2. **Comparar con scratch reports** previos.
3. **Hacer fotos** de cualquier daño nuevo.
4. **Pintar el daño** en el painter si lo hay.
5. **Sólo entonces** pulsar *Return*.

### Antes de cancelar un contrato

1. Verificar con el cliente las condiciones de la política.
2. Confirmar el importe del cargo de cancelación.
3. Acordar la devolución del depósito.
4. Documentar el motivo en el campo *Cancellation Reasons*.

### Periodicidad recomendada de tareas

| Tarea | Frecuencia |
|---|---|
| Revisar **Consultas de Reserva** | Cada mañana. |
| Revisar **Pending Invoices** en el panel | Cada mañana. |
| Verificar **Solicitudes de mantenimiento** pendientes | Cada mañana. |
| Actualizar **Tarifas** por temporada | Mensual o trimestral. |
| Revisar **scratch reports** de la flota | Tras cada devolución. |
| Conciliar **pagos Redsys** | Diario (lo hace solo el sistema, sólo supervisar). |
| Backup de la base de datos | Diario (responsabilidad de IT). |

---

## K. Cuándo escalar a un técnico

Llamar a tu equipo de IT o al proveedor del módulo (Cositt) si:

- Los **crons no se ejecutan** después de comprobar que están activos.
- Las **facturas no se generan** y los logs muestran errores Python.
- El **portal web no carga** o devuelve 500.
- Redsys **rechaza todas las transacciones** (problema de credenciales o entorno).
- Aparecen errores con menciones a `Traceback`, `MissingError`, `ValidationError` que no entiendes.
- Necesitas **modificar lógica del módulo** (cambiar categorías excluidas, añadir delegaciones, etc.).

---

## L. Comprobaciones rápidas (checklist técnico)

Antes de cargar la culpa al módulo, comprueba lo básico:

- ✅ Servidor Odoo arrancado y respondiendo.
- ✅ Base de datos accesible.
- ✅ wkhtmltopdf instalado en versión compatible (0.12.5).
- ✅ SMTP configurado y operativo.
- ✅ Redsys configurado en modo correcto (test/producción).
- ✅ Cron de Odoo ejecutándose.
- ✅ Plantillas de email cargadas.
- ✅ Productos especiales existen en el catálogo.
- ✅ Diarios contables tipo *Sales* configurados.
- ✅ Categorías de vehículo con tarifas vigentes.

---

## Relacionado

- Todos los capítulos anteriores: cada uno tiene su propia sección de errores frecuentes.
- [01 · Introducción](./01-introduccion.md) — recordatorio del menú y flujos.

---

[← Volver al índice](./README.md) · Anterior: [15 · Configuración](./15-configuracion.md)
