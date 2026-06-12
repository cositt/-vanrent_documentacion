# 13 · Portal Web Público

[← Volver al índice](./README.md)

---

## ¿Qué es?

El **portal web público** son las **páginas accesibles desde Internet** sin necesidad de iniciar sesión, donde los clientes finales pueden:

- Ver la **flota disponible** y las categorías de vehículo.
- Consultar **delegaciones** (Madrid, Barcelona, Valencia).
- Leer **información de servicios** y **seguros**.
- Rellenar un **formulario de contacto**.
- **Hacer una reserva online** con pago por tarjeta (Redsys).

El módulo expone **dos marcas** con experiencia web propia:

| Marca | Especialidad | URLs |
|---|---|---|
| **Sunset Rent a Car** | Turismo / alquiler general (coches) | `/` · `/sunset/*` |
| **Pinveco** | Furgonetas profesionales | `/pinveco/*` |

Ambas marcas comparten la misma base de datos y se distinguen por **compañía** (`res.company`) y por **URLs**.

---

## A. Páginas Sunset

### Home

- **`/`** y **`/sunset`** → página principal (editable desde *Website* de Odoo).

### Catálogo de flota

- **`/sunset/flota`** — listado de la flota Sunset.
- Atajos: **`/test/flota`**, **`/simple/flota`** (para pruebas y versiones simplificadas).

### Categoría concreta

- **`/sunset/categoria/<id>`** — página de una categoría (Tipo A, Tipo C…).

### Delegaciones

- **`/sunset/delegacion/<ciudad>`** — página de una delegación.

### Servicios

- **`/sunset/servicios/<servicio>`** — servicio individual.
- **`/sunset/seguros`** — información de seguros.
- **`/our-services`** — página de servicios general, con bloques pre-cargados:
  - **Servicios principales**: Alquiler de Vehículos · Servicio de Chófer · Transporte Corporativo (cada uno con icono, descripción y 4 ventajas).
  - **Servicios adicionales**: Seguro Completo · Entrega a Domicilio · Asistencia en Carretera · Mantenimiento.
  - **Testimonios** de clientes (María González, Carlos Rodríguez, Ana Martín) con valoración.

### Contacto

- **`/contactus`** — página de contacto con:
  - **Datos de la empresa**: Sunset Rent a Car, Calle Principal 123 (Madrid), +34 91 123 45 67, info@sunsetrentacar.com.
  - **Horario**: L-V 8:00-20:00 · Sábados 9:00-18:00 · Domingos 10:00-16:00.
  - **Tres delegaciones**: Madrid Centro · Barcelona · Valencia (cada una con dirección, teléfono y email propios).
  - **Formulario de contacto**: nombre, email, teléfono, asunto, mensaje (los cuatro primeros obligatorios).

### Envío del formulario de contacto

- **`/contactus/enviar`** (POST) → procesa el formulario:
  1. Valida que no falten campos obligatorios. Si faltan, devuelve la página con los datos y el aviso *"Faltan campos obligatorios…"*.
  2. **Crea un lead** en CRM (`crm.lead`) con nombre, email, teléfono, descripción del asunto/mensaje. Marca origen *"Website"* si está disponible.
  3. Renderiza la página de éxito (*"Gracias, te hemos recibido el mensaje…"*).

Estos leads aparecen en el CRM estándar de Odoo (no en *Consultas de Reserva*).

---

## B. Páginas Pinveco

### Home

- **`/pinveco`** → home de Pinveco.

Muestra:

- **Catálogo de categorías** de furgonetas:
  - Furgoneta Pequeña.
  - Furgoneta Mediana.
  - Furgoneta Grande.
  - Furgón Isotermo.
  - Furgoneta 9 Plazas.
  - Furgón Plataforma.
- **Totales en cabecera** (30 vehículos / 25 disponibles / 800 contratos como ejemplo de cifras).

### Catálogo dinámico de flota

- **`/pinveco/flota`** → catálogo de la flota Pinveco.

Filtra los vehículos cuya compañía es "Pinveco" y los muestra con:

- Nombre (limpio, sin matrícula).
- Modelo, matrícula, color, año, categoría.
- Pasajeros (asientos), precio orientativo.
- Características (Aire acondicionado, GPS, Carga útil).
- Icono de furgoneta.

### Categoría de Pinveco

- **`/pinveco/categoria/<id>`** — categoría concreta de furgoneta. Lista los vehículos de la compañía Pinveco que pertenecen a esa categoría.

### Delegaciones Pinveco

- **`/pinveco/delegacion/<ciudad>`** — página de delegación Pinveco.

Las tres preconfiguradas:

| Ciudad | Dirección | Teléfono | Email | Horario | Rating |
|---|---|---|---|---|---|
| **Madrid** | C/ Alcalá 200 | +34 913 221 100 | madrid@pinveco.com | L-V 7:00-21:00 · S 8:00-15:00 | 4.8 |
| **Barcelona** | Av. Diagonal 500 | +34 933 445 566 | barcelona@pinveco.com | — | 4.9 |
| **Valencia** | C/ Colón 85 | +34 963 778 899 | valencia@pinveco.com | — | 4.7 |

> Si el cliente teclea una ciudad que no existe, devuelve un **404**.

---

## C. Diferencias entre las dos marcas

| Aspecto | Sunset | Pinveco |
|---|---|---|
| **Producto** | Coches (turismo, premium) | Furgonetas profesionales |
| **Color identitario** | Amarillo Sunset | Azul Pinveco |
| **Servicios añadidos** | Chófer, transporte corporativo | — |
| **Tres delegaciones** | Madrid, Barcelona, Valencia | Madrid, Barcelona, Valencia |
| **Compañía Odoo** | Compañía Sunset | Compañía Pinveco |
| **URL principal** | `/`, `/sunset/*` | `/pinveco/*` |

El sistema detecta el dominio desde el que accede el cliente y muestra automáticamente la marca correspondiente: color, imágenes, textos.

---

## D. Proceso de reserva online

Toda la reserva web vive en el dominio público (Sunset o Pinveco) y se compone de **tres pantallas + pasarela**:

### Pantalla 1 — Selección de categoría

**URL**: `/web/booking-enquiry` → "Selecciona tu Categoría de Vehículo"

- Se ve una **rejilla de tarjetas**, una por categoría (Tipo A, B, C, D, E, F, K, T, V, W, X, Z).
- Cada tarjeta lleva su **imagen** (combi pequeño, mediano, furgón grande, patinete eléctrico…) y una **descripción corta** (*"Furgonetas compactas ideales para la ciudad"*, etc.).
- El sistema **excluye automáticamente** las categorías: *"Furgoneta"*, *"Tipo E - Bici Eléctrica"*, *"Tipo E"*, *"Bici Eléctrica"*.
- El color (amarillo Sunset o azul Pinveco) y las imágenes se eligen automáticamente según el dominio.
- Al hacer clic en una tarjeta → `/web/vehicle-detail/<id>`.

### Pantalla 2 — Detalle de categoría y selección de tarifa

**URL**: `/web/vehicle-detail/<id>` → ficha de la categoría.

- **Cabecera** con nombre del tipo, descripción larga, plazas, A/C sí/no, volumen.
- **Selector de tipo de alquiler** (dos botones radio):
  - **Ofertas Fijas**: tarjetas con paquetes pre-definidos:
    - `4h / 100km`
    - `24h / 350km` etiquetada como **"Más Popular"**.
    - `24h / 500km`
    - El cliente clica una.
  - **Tarifas Dinámicas**:
    - Dos desplegables:
      - **Duración del alquiler**: `4h` · `1-2d` · `3-5d` · `6-10d` · `11-20d` · `21-29d`.
      - **Kilometraje incluido**: dinámico vía AJAX (`/web/get-valid-km-options`).
    - Precio calculado con `/web/get-dynamic-pricing` mostrado en una tarjeta destacando el **€ por día**.
- **Selector de ubicación**: desplegable con las ubicaciones donde hay vehículos. Si elige una sin disponibilidad → aviso *"No hay vehículos disponibles"*.
- **Llamada AJAX** a `/web/get-available-vehicles` para mostrar disponibilidad real:
  - Total · Ocupados (contratos en borrador o curso) · Reservados (otras consultas web abiertas) · Disponibles.
- Si `Disponibles > 0` → aparece el **formulario de reserva**.
- Si no → aviso *"No hay vehículos disponibles"*.

### Formulario de reserva (sección 3)

En la misma página, oculto hasta tener **tipo + ubicación + disponibilidad**.

#### Bloque "Datos de contacto"

- **Nombre completo** *(obligatorio)*
- **Email** *(obligatorio)*
- **Teléfono** *(obligatorio)*
- **Empresa** (opcional)

#### Bloque "Documentación"

- **DNI/NIE** *(obligatorio)*
- **Fecha de expiración del DNI** (mes/año) *(obligatorio)*

#### Bloque "Dirección"

- **Calle** *(obligatorio)*
- Calle 2 (opcional)
- **Ciudad** *(obligatorio)*
- **C.P.** *(obligatorio)*
- **País** *(obligatorio, desplegable)*

#### Bloque "Conductor"

- **Carnet de Conducir** *(obligatorio)*
- **Fecha de Nacimiento** *(obligatorio)*
- **Fecha Expedición Carnet** *(obligatorio)*
- **Fecha Caducidad Carnet** *(obligatorio)*

#### Bloque "Pago"

- **Número de Tarjeta** *(obligatorio)* — detecta el BIN y muestra el tipo (Visa, Mastercard, etc.).
- **Tipo de Tarjeta** (oculto, deducido del BIN).
- **Depósito de Seguridad** — se calcula automáticamente según las [reglas de depósito](./08-tarifas-seguros-depositos.md#c-reglas-de-deposito). Visible como caja info azul.
- **Total a Pagar** — alquiler + depósito, en caja naranja.

#### Bloque "Fechas"

- **Fecha de inicio** *(obligatorio)*
- **Fecha de fin** *(obligatorio)* — con validación de duración mínima según la tarifa elegida.
- Un aviso en azul recuerda *"Seleccione una tarifa para ver la duración mínima requerida"*.

---

## E. Lo que pasa al enviar el formulario

### Pantalla 4 — Pasarela Redsys

Al enviar el formulario, se crea un `payment.transaction` con `provider_code = redsys` y todos los datos de la reserva guardados en `booking_data_json`. Se **redirige a la pasarela**.

### Pantalla 5 — Página de éxito

**URL**: `/rental/success`

- Pantalla simple verde: *"✓ ¡Pago realizado exitosamente! Tu reserva ha sido confirmada. Serás redirigido en breve…"*
- Redirige al home a los 3 segundos.
- En segundo plano:
  - Busca la última transacción Redsys sin booking creado.
  - La fuerza a `done` si no lo estaba.
  - Lanza la creación del Lead.

### Pantalla 5b — Página de error

**URL**: `/rental/error`

- Pantalla roja: *"✗ El pago no pudo ser procesado. Por favor, intenta nuevamente o contacta a soporte."*
- Redirección al home en 5 segundos.

---

## F. Qué se crea realmente en Odoo

**Sólo se crea un Lead — NO un contrato.** El contrato sigue siendo paso manual desde el backend.

En concreto:

### Contacto (`res.partner`)

- Si existe por email/teléfono → se reutiliza y actualiza dirección.
- Si no → se crea nuevo con `customer_rank = 1`.

### CRM Lead (tipo oportunidad)

| Campo | Valor |
|---|---|
| **Nombre** | `Consulta de Reserva - <Nombre del cliente>` |
| **Etapa** | La primera del pipeline |
| **Cliente, email, teléfono** | Del formulario |
| **Categoría seleccionada** | NO se asigna vehículo concreto; sólo bloquea la categoría |
| **Fechas y horas de inicio/fin** | Del formulario |
| **DNI, expiración del DNI** | Del formulario |
| **Dirección completa** | Del formulario |
| **Carnet, fechas de carnet, fecha de nacimiento** | Del formulario |
| **Ubicación** | La que eligió |
| **Descripción** | Resumen detallado con tipo de vehículo, tarifa €/día, precio total, depósito, total pagado, fechas, dirección, DNI, carnet, fecha de nacimiento, referencia Redsys y validación BIN |

### Factura de depósito

Automática si el depósito > 0. Se crea y se marca como **pagada** inmediatamente.

---

## G. El operario procesa la consulta

La consulta cae en **Vehicles Rental → Consultas de Reserva** lista para revisar. El operario:

1. Abre la consulta, comprueba los datos del cliente y la ubicación.
2. Pulsa **Convertir a Oportunidad**:
   - Si no había contacto → lo crea.
   - Si no había vehículo concreto asignado → asigna automáticamente uno disponible de la categoría.
3. Pulsa **Crear Contrato**:
   - Se abre el wizard con todo precargado (cliente, vehículo, fechas, DNI, carnet, dirección…).
   - Crea contrato + Sale Order + línea de pago.
4. Pulsa **Imprimir Contrato** y **Enviar Contrato** para entregar la documentación al cliente.

Ver detalle en [05 · Consultas web y leads](./05-consultas-web-y-leads.md).

---

## H. Email de confirmación

> **Importante**: el cliente **NO recibe email automático en el momento de pagar**.

El email se envía **manualmente** desde la ficha del Lead (botón "Enviar Contrato") **una vez que el operario ha generado el contrato real**.

En ese momento:
- Se renderiza el PDF del contrato.
- Se manda con la plantilla `email_template_vehicle_contract` al email del cliente.
- Se adjunta el contrato.

Si la plantilla no está instalada o configurada, salta error.

---

## I. Endpoints AJAX usados internamente

| Endpoint | Propósito |
|---|---|
| `/web/get-valid-km-options` | Devuelve los rangos de km disponibles según categoría y duración. |
| `/web/get-dynamic-pricing` | Calcula el precio €/día según categoría + duración + km. |
| `/web/get-available-vehicles` | Cuenta total/ocupados/reservados/disponibles de una categoría en unas fechas y ubicación. |

Si la web responde lento, suele ser por **muchas llamadas a estos endpoints** desde un cliente con conexión lenta. No es un error.

---

## Validaciones y restricciones del flujo web

- **Campos obligatorios**: el formulario JS bloquea el envío si falta alguno.
- **BIN inválido**: aviso al cliente *"Tarjeta no reconocida"*.
- **Categorías excluidas**: bici eléctrica y patinete eléctrico no aparecen en `/web/booking-enquiry`.
- **Fechas pasadas**: el formulario no permite seleccionar fechas anteriores a hoy.
- **Duración mínima**: si eliges *4h*, el formulario fuerza Fecha fin = Fecha inicio + 4h.

---

## Errores frecuentes

| Síntoma | Causa | Solución |
|---|---|---|
| Cliente paga pero no aparece el lead | El webhook de Redsys falló | El cron de recuperación lo crea en máximo 30 minutos. Si no, mirar logs Redsys. |
| "No hay vehículos disponibles" | Realmente no hay, o la categoría seleccionada está mal | Comprobar inventario y categorías. |
| BIN no reconocido | Tarjeta extranjera o BIN nuevo | El sistema asume débito por defecto, depósito mayor. |
| Página de detalle de categoría sale en blanco | ID de categoría incorrecto en URL | Verificar el ID. |
| Pinveco delegación 404 | Ciudad mal escrita en URL | Sólo Madrid, Barcelona, Valencia están configuradas. |

---

## Personalización

Las páginas se pueden editar desde **Sitio web** de Odoo (modo edición visual). Los textos, imágenes y bloques de las páginas Sunset/Pinveco están en plantillas QWeb dentro de `views/templates/*.xml`.

---

## Relacionado

- [05 · Consultas web y leads](./05-consultas-web-y-leads.md) — qué se crea al recibir una reserva web.
- [09 · Pagos y Redsys](./09-pagos-y-redsys.md) — la pasarela de pago.
- [08 · Tarifas, seguros y depósitos](./08-tarifas-seguros-depositos.md) — origen de los precios y depósito que se muestran al cliente.
- [03 · Flota y vehículos](./03-flota-y-vehiculos.md) — categorías y vehículos publicados.

---

[← Volver al índice](./README.md) · Anterior: [12 · Devolución y facturación final](./12-devolucion-y-facturacion.md) · Siguiente: [14 · Emails y reportes →](./14-emails-y-reportes.md)
