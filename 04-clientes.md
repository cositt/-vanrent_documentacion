# 04 · Clientes

[← Volver al índice](./README.md)

---

## ¿Qué es?

Los **clientes** son los contactos (`res.partner` en términos de Odoo) que alquilan vehículos. Pueden ser:

- **Particulares** — personas físicas con DNI/NIE.
- **Empresas** — personas jurídicas con CIF.

El módulo no añade un modelo nuevo de cliente: reutiliza el de Odoo (**Contactos**). Pero exige unos datos extra para el alquiler que se rellenan en el contrato y se conservan asociados al cliente: **DNI/NIE, carnet de conducir, dirección, fecha de nacimiento**.

Además, cada contrato puede llevar **documentos escaneados del cliente** (foto del carnet, del DNI…) que se almacenan en un repositorio centralizado.

## Cómo acceder

**Vehicles Rental → Clientes**

Esta entrada de menú abre la **lista de contactos estándar de Odoo** (la misma que aparece en el menú "Contactos"). Es decir, todos los contactos de Odoo, no sólo los clientes de alquiler.

> Si quieres filtrar sólo los que han tenido contratos de alquiler, puedes aplicar el filtro **"Contratos > 0"** en la barra de búsqueda.

---

## Alta de un cliente

### Paso a paso (manual)

1. **Vehicles Rental → Clientes → Crear** (botón "Nuevo").
2. Rellenar el **nombre** (persona o empresa).
3. Marcar **Individual** o **Empresa** según el caso.
4. Cumplimentar:
   - **Email**.
   - **Teléfono**.
   - **Dirección**: calle, ciudad, código postal, provincia, país.
   - **NIF / DNI** (en el campo VAT/CIF estándar de Odoo, o en un campo personalizado del contrato).
5. **Guardar**.

> En el alquiler, lo más eficiente es **no crear el cliente desde aquí**. Es mejor que se cree automáticamente al procesar una [consulta de reserva web](./05-consultas-web-y-leads.md) o desde el [asistente de reserva](./06-reservas.md). Ambos crean el contacto automáticamente si no existe (buscan primero por email/teléfono).

### Alta automática desde la web

Cuando un cliente paga una reserva en la web pública con Redsys:

1. El sistema busca un contacto existente por **email** y, si no, por **teléfono**.
2. Si lo encuentra: actualiza la dirección con los datos del formulario.
3. Si NO lo encuentra: crea un contacto nuevo con `customer_rank = 1` (marcado como cliente).

De esta forma no se duplican contactos cuando un mismo cliente alquila varias veces.

---

## Datos requeridos para alquilar

Cuando se crea un contrato de alquiler, **además de los datos básicos**, hacen falta estos campos:

### Identificación

- **DNI / NIE / Pasaporte**.
- **Fecha de expiración del DNI**.
- **Fecha de nacimiento** (importante: determina si es un *conductor especial* de menos de 25 o más de 60 años, lo que afecta al precio del seguro).

### Carnet de conducir

- **Número de carnet**.
- **Fecha de expedición**.
- **Fecha de caducidad**.

> Estos campos se rellenan dentro del propio contrato (en el bloque "Carnet de Conducir") y se conservan asociados al cliente para futuras reservas.

### Dirección postal

- Calle (línea 1), Calle 2 (opcional), Ciudad, Código Postal, Provincia, País.

---

## Documentos del cliente

### ¿Qué es?

Cada contrato puede llevar adjuntos los **escaneos o fotos** de los documentos del conductor: copia del DNI, del carnet de conducir, del pasaporte… Es un repositorio centralizado para que cualquier operario pueda recuperarlos sin tener que abrir los correos del cliente.

### Cómo acceder

**Acceso directo**: dentro de cada contrato hay un botón estadístico **Documents** que abre los documentos vinculados.

**Acceso global**: no hay una entrada de menú propia; los documentos viven dentro del contrato y se gestionan en línea desde la pestaña/lista correspondiente.

### Tipos de documento

El campo "Tipo de documento" tiene estos valores predefinidos:

| Valor interno | Etiqueta visible |
|---|---|
| `driving_license` | Carnet de conducir |
| `passport` | Pasaporte |
| `aadhaar_card` | Aadhaar card (India) |
| `voter_id_card` | Voter ID card |
| `ration_card` | Ration card |
| `photo_id_card` | Documento con foto / DNI |

> Los tipos *Aadhaar*, *Voter ID* y *Ration card* son los típicos de la India (el módulo original es de un proveedor indio). En la práctica, los relevantes en España son **Carnet de Conducir**, **Pasaporte** y **Photo ID / DNI**.

### Cómo subir un documento

1. Abrir el contrato del cliente.
2. Pulsar el botón estadístico **Documents** (esquina superior derecha del contrato).
3. **Crear** una nueva línea:
   - **Tipo de documento**: elegir el tipo.
   - **Documento**: arrastrar el archivo o pulsar el botón de subida (PDF, JPG, PNG…).
   - **Nombre del fichero** se rellena solo.
4. **Guardar**.

El documento queda vinculado al contrato y al vehículo. Cuando el contrato pasa a estado **Devuelto**, ya **NO se pueden añadir** más documentos a ese contrato (queda cerrado documentalmente).

### Recuperar documentos

Desde cualquier contrato del mismo cliente, los documentos que se subieron en contratos anteriores **no aparecen automáticamente** — están vinculados al contrato concreto donde se subieron. Si necesitas verlos, abre el contrato anterior del cliente.

---

## Portal del cliente

Odoo permite que cada cliente acceda a un **portal web privado** donde ve sus reservas, facturas y datos personales. El módulo Vehicle Rental hereda esta funcionalidad estándar.

### Acceso del cliente al portal

1. Desde el contacto, el operario pulsa **Acción → Conceder acceso al portal**.
2. Se introduce el email del cliente.
3. Odoo le manda un email con un enlace para crear contraseña.
4. El cliente entra en `https://<tu-dominio>/my` y ve:
   - Sus **facturas** (puede descargarlas en PDF y pagarlas con Redsys).
   - Sus **pedidos de venta** (si los hay).
   - Sus **consultas de reserva**.

### Qué NO ve el cliente en el portal

- Los **contratos de alquiler** (`vehicle.contract`) no tienen vista de portal pública por defecto en este módulo. Si necesitas que el cliente vea su contrato, mándale el PDF por email con el botón [Enviar Contrato](./07-contratos.md#boton-enviar-contrato).

---

## Ejemplos de uso

### Dar de alta a un cliente que llega presencialmente

> **Caso:** Juan García viene a alquilar un coche. No tiene cuenta todavía.

1. Lo más eficiente: ir directamente al [Asistente de Reserva](./06-reservas.md). Cuando rellenes el campo *Cliente*, escribe el nombre y pulsa **"Crear nuevo"** → Odoo abrirá la ficha de contacto para que rellenes email, teléfono y dirección.
2. Una vez creado, el asistente continúa con la reserva normal.

### Cliente recurrente con varios alquileres

> **Caso:** María López ya alquila por tercera vez.

1. En el asistente de reserva, en *Cliente*, **buscar su nombre o email**. Odoo lo encuentra inmediatamente.
2. Los datos personales (DNI, dirección…) que rellenaste la primera vez se pueden copiar a mano al nuevo contrato, pero **no se autorrellenan al 100%** porque el módulo guarda algunos datos (DNI, carnet) **en el contrato**, no en el contacto. Conviene comprobarlos cada vez.

### Cliente que reservó por web

> **Caso:** Carlos Sánchez pagó una reserva online el sábado a las 22:00.

1. El sistema le **creó solo un Lead** (Consulta de Reserva) y un contacto nuevo.
2. El lunes, recepción abre **Consultas de Reserva**, ve el lead de Carlos con TODOS sus datos (DNI, dirección, carnet, fecha de nacimiento, depósito ya pagado).
3. Pulsa **Convertir a Oportunidad** y luego **Crear Contrato** → el contrato sale ya con todos los datos del cliente precargados.

---

## Validaciones y errores

- **Email duplicado**: Odoo no obliga a que el email sea único, pero la búsqueda automática del cliente desde la web sí usa el email como criterio de identificación. Procurar no duplicar contactos.
- **DNI / Carnet caducados**: el sistema NO bloquea automáticamente si el DNI o el carnet están caducados. Es responsabilidad del operario revisar las fechas de expiración antes de cerrar el contrato.

---

## Relacionado

- [05 · Consultas web y leads](./05-consultas-web-y-leads.md) — alta automática del cliente al pagar online.
- [06 · Reservas](./06-reservas.md) — el cliente se selecciona o se crea desde el asistente.
- [07 · Contratos](./07-contratos.md) — donde se rellenan los datos completos (DNI, carnet, dirección).

---

[← Volver al índice](./README.md) · Anterior: [03 · Flota y vehículos](./03-flota-y-vehiculos.md) · Siguiente: [05 · Consultas web y leads →](./05-consultas-web-y-leads.md)
