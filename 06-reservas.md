# 06 · Reservas

[← Volver al índice](./README.md)

---

## ¿Qué encontrarás aquí?

Dos asistentes (wizards) que crean contratos desde cero:

1. **Reservar Vehículo** — para una reserva con **un único vehículo**.
2. **Reserva Múltiple** — para alquilar **varios vehículos al mismo cliente** en la misma operación.

Para procesar reservas que vienen ya pagadas desde la web, ve a [05 · Consultas web y leads](./05-consultas-web-y-leads.md).

---

## A. Asistente de Reserva (un vehículo)

### Cómo acceder

**Vehicles Rental → Reservar Vehículo**

Se abre directamente una ventana modal grande con el asistente `rental.contract.booking`.

### ¿Cuándo usarlo?

- El cliente **llama por teléfono** o viene al mostrador.
- Reserva **manual**, sin pago previo por web.
- Se quiere usar el sistema de **tarifas dinámicas** (precio calculado por categoría + duración + km).

### Estructura del asistente

El formulario tiene **tres grupos de campos** y una **pestaña con resultados**.

#### Datos básicos (obligatorios)

| Campo | Notas |
|---|---|
| **Cliente** | Many2one a Contactos. Si no existe, hay que crearlo primero desde *Contactos*. Desde este wizard NO se crea. |
| **Compañía** | Se rellena con la compañía activa del usuario. |
| **Pick-up Date** | Fecha y hora de recogida. |
| **Drop-off Date** | Fecha y hora de devolución. |

#### Datos de tarifa

| Campo | Notas |
|---|---|
| **Tipo de Vehículo** (Categoría) | Obligatorio. Filtrará los vehículos disponibles. |
| **Duración** | Desplegable dinámico: sus valores se leen de las **reglas de tarifa** activas. Típicamente: `4h`, `1-2d`, `3-5d`, `6-10d`, `11-20d`, `21-29d`. Al cambiarla, **autocompleta la fecha de fin** sumando los días mínimos a la fecha de inicio. |
| **Buscar por marca o modelo** | Texto libre, opcional. Filtra la lista de vehículos. |
| **Rango de Km** | Desplegable dinámico. Los km incluidos en el alquiler (100, 350, 500, 650, 850, sin límite). |
| **Tipo de Tarifa** | Por defecto `standard`. También puede ser `FLEXIRENT` u otros tipos definidos en las reglas. |
| **Tarifa (€/día)** | **Calculado y sólo lectura.** Se calcula cruzando categoría + duración + km + tipo contra `vehicle.pricing.rule`. Si no hay regla aplicable, queda en 0. |

> El campo Tarifa se recalcula automáticamente cada vez que cambias cualquiera de los criterios anteriores.

#### Pestaña "Vehículos Disponibles"

**Sólo aparece** si están definidos categoría y fechas. Es una tabla con:

- Nombre del vehículo.
- Modelo.
- Matrícula.
- Año.
- Transmisión.
- Combustible.
- Categoría.

Cada fila lleva un botón **"Reservar"** (azul, contorno).

### Cómo se calcula la disponibilidad

La tabla se filtra automáticamente cuando cambian los criterios. Los vehículos que aparecen cumplen TODO esto:

1. Vehículos de la **compañía activa**.
2. Estado = **Disponible** (no en mantenimiento).
3. Misma **categoría** (directa o por modelo).
4. **NO tienen contratos en curso o devueltos** que se solapen con las fechas elegidas.
5. **NO tienen contratos en borrador** con vehículo asignado solapado.
6. Si hay texto en *Buscar*, su nombre o modelo coincide parcialmente.

### Paso a paso típico

1. Abre **Reservar Vehículo**.
2. Elige el **Cliente** (lo buscas por nombre o email).
3. Indica **Pick-up Date** y **Drop-off Date**.
4. Elige **Tipo de Vehículo** (categoría).
5. Opcionalmente ajusta **Duración** y **Rango de Km** — el sistema calcula la tarifa €/día.
6. Mira la pestaña **Vehículos Disponibles**: aparece la lista filtrada.
7. Pulsa **Reservar** en la fila del vehículo que quieras.
8. El sistema crea un **contrato en Borrador** y abre directamente su ficha.

### Qué crea al pulsar "Reservar"

Genera un **contrato de vehículo (`vehicle.contract`)** con:

- Vehículo + conductor por defecto + matrícula + odómetro inicial + unidad + año + transmisión + combustible (todo heredado del vehículo).
- Cliente + teléfono + email.
- Fechas de inicio y fin del wizard.
- Compañía activa.
- **Tarifa (`rent`)** = el precio calculado.
- **Tipo de alquiler** = `días`.
- **Tipo de tarifa** = el seleccionado.

A partir de aquí continúa el flujo del contrato (ver [07 · Contratos](./07-contratos.md)): rellenar DNI/carnet, confirmar In Progress, etc.

### Diferencia con "Crear Contrato" desde un Lead

| Aspecto | Reservar Vehículo | Crear Contrato desde Lead |
|---|---|---|
| **Origen** | Manual, sin lead | Lead web ya pagado |
| Cliente | Hay que elegirlo | Hereda del Lead |
| DNI / carnet / dirección | NO se rellenan | Sí, copia del Lead |
| **Tarifa** | Calculada por reglas de pricing | Sale del importe Redsys pagado |
| **Genera Pedido de Venta** | No | Sí, confirmado |
| **Genera línea de pago Redsys** | No | Sí (importe pagado) |
| **Vincula Lead** | No | Sí |

---

## B. Reserva Múltiple

### ¿Qué es?

Permite alquilar a un **mismo cliente varios vehículos en la misma operación**, generalmente con condiciones comunes (mismo tipo de pago, misma tarjeta de depósito) pero con fechas y vehículos distintos. Al confirmar se generan **un Contrato Grupo "paraguas" + un contrato individual por cada vehículo**.

### Ejemplo de caso real

> **Caso:** Juan Pérez quiere alquilar un Tipo A del 1 al 7 de junio Y un Tipo K del 3 al 5 de junio.
>
> Se crea **una sola Reserva Múltiple `BR/00001`**, se añaden las dos líneas, y al pulsar "Crear Reserva Múltiple" se generan:
> - El **Contrato Grupo** (con cliente Juan, fechas 1-7 junio).
> - El **contrato del Tipo A** (1-7 junio).
> - El **contrato del Tipo K** (3-5 junio).
> - Todos a nombre de Juan, con la misma tarjeta de depósito.

### Cómo acceder

**Vehicles Rental → Reserva Múltiple**

Lista con:

| Columna | Significado |
|---|---|
| Referencia | Autonumerada (`BR/00001`...). |
| Cliente | Quien alquila. |
| Nº de vehículos | Líneas creadas. |
| Tipo de pago | Diario / Semanal / Mensual / Pago Completo / etc. |
| Estado | *Borrador* (azul) · *Completada* (verde) · *Cancelada* (gris tachado). |
| Grupo Creado | Sólo aparece cuando ya se generó. |
| Fecha de creación | Cuándo se hizo. |

### Formulario de Reserva Múltiple

#### Cabecera

- Barra de estado: `Borrador → Completada`.
- **Botones**:

| Botón | Cuándo aparece | Qué hace |
|---|---|---|
| **Añadir Vehículo** (verde, icono `+`) | Sólo en Borrador | Abre el wizard de Reservar Vehículo en *modo Reserva Múltiple*. |
| **Crear Reserva Múltiple** (verde, check) | Sólo en Borrador con ≥ 1 línea | Lanza un diálogo de confirmación: *"Se crearán el contrato grupo y los contratos individuales. ¿Continuar?"*. |
| **Cancelar** (gris) | Cualquier estado | Cambia a *Cancelada*. Confirmación previa: *"¿Cancelar esta reserva múltiple?"*. |

#### Cuerpo

| Campo | Notas |
|---|---|
| **Referencia** | Autonumerada. Hasta confirmar, muestra `Borrador`. |
| **Cliente** | Obligatorio. NO se puede crear desde aquí. Sólo lectura tras confirmar. |
| **Tipo de Pago** | Diario / Semanal / Mensual / Trimestral / Anual / **Pago Completo** (por defecto). |
| **Tipo de Tarjeta** | Débito / Crédito (por defecto Débito) — afecta al depósito de los contratos hijos. |
| **Grupo Creado** | Enlace sólo lectura al `vehicle.contract.group` resultante. |
| **Observaciones** | Textarea libre. Sólo visible/editable en Borrador. |

#### Tabla "Vehículos seleccionados"

| Columna | Notas |
|---|---|
| Vehículo | El que se va a alquilar. |
| Matrícula | Auto. |
| Categoría | Auto. |
| Fecha de recogida | De ese vehículo. |
| Fecha de devolución | De ese vehículo. |
| Tipo de tarifa | Estándar / FLEXIRENT. |
| Precio/día | Calculado por reglas. |

> Las líneas **no se pueden crear ni editar directamente en la tabla**. Se añaden con el botón **Añadir Vehículo** (que abre el asistente con el cliente prefijado). Sí se pueden **borrar** desde aquí mientras estamos en Borrador.

### Flujo paso a paso

1. **Vehicles Rental → Reserva Múltiple → Crear** (botón "Nuevo").
2. Elegir **Cliente**, **Tipo de Pago** y **Tipo de Tarjeta**.
3. Pulsar **Añadir Vehículo**. Se abre el asistente `rental.contract.booking` en modo Reserva Múltiple:
   - Cliente y Compañía aparecen **bloqueados** (sólo lectura).
   - Sale un aviso azul: *"Modo Reserva Múltiple: al pulsar 'Reservar' el vehículo se añadirá a la reserva múltiple"*.
   - El operario elige categoría, fechas, duración, km y tarifa.
   - Selecciona un vehículo de la lista y pulsa **Reservar**.
   - Vuelve a la Reserva Múltiple con una **línea nueva**.
4. **Repetir** tantas veces como vehículos hagan falta. Cada vehículo puede tener fechas distintas.
5. Pulsar **Crear Reserva Múltiple** → confirmar el diálogo.

### Qué crea al confirmar

#### Un Contrato Grupo (`vehicle.contract.group`)

- **Cliente, compañía, tipo de pago, tipo de tarjeta, observaciones**.
- **Fecha de inicio** = la **más temprana** de todas las líneas.
- **Fecha de fin** = la **más tardía** de todas las líneas.

#### Un contrato `vehicle.contract` por cada línea

Todos enlazados al grupo (`group_id`), cada uno con:

- Su **vehículo** y **fechas individuales**.
- Su **tarifa**.
- **Motivo de descuento**: `Reserva múltiple <nombre del grupo>`.
- Tipo de tarjeta y tipo de pago **heredados del grupo**.
- Datos del cliente.

#### La Reserva Múltiple pasa a Completada

Queda enlazada al grupo creado, y **se abre automáticamente la ficha del Contrato Grupo**.

### Validaciones

- **No se puede añadir vehículo sin cliente** → error: *"Seleccione un cliente antes de añadir vehículos."*
- **No se puede confirmar si no hay líneas** → error: *"Añada al menos un vehículo."*
- En el asistente, si la reserva múltiple ya no existe (borrada en otra pestaña): *"La reserva múltiple ya no existe."*
- Sólo se puede editar/cancelar mientras esté en **Borrador**.

### Diferencia con Contrato Grupo manual

El **Contrato Grupo** (`vehicle.contract.group`) se puede crear también **a mano** desde el menú **Contratos Grupo** y añadir contratos uno a uno. Pero el flujo **Reserva Múltiple** es más rápido si sabes desde el principio que vas a alquilar varios coches: hace todo en una sola operación.

Ver [07 · Contratos · Contratos en grupo](./07-contratos.md#contratos-en-grupo) para más detalles del grupo.

---

## Comparativa rápida de los tres caminos

| Camino | Cuándo se usa | Pago previo | Genera |
|---|---|---|---|
| **Reservar Vehículo** | Recepción / teléfono, un coche | No | 1 contrato |
| **Reserva Múltiple** | Recepción, varios coches mismo cliente | No | 1 grupo + N contratos |
| **Convertir Lead → Contrato** | Cliente reservó por web | Sí (Redsys) | 1 contrato + 1 Sale Order + línea de pago |

---

## Errores y validaciones comunes

| Mensaje | Causa | Solución |
|---|---|---|
| "Seleccione un cliente antes de añadir vehículos" | Falta el cliente en la Reserva Múltiple | Rellenarlo antes de pulsar *Añadir Vehículo*. |
| "Añada al menos un vehículo" | Reserva Múltiple sin líneas | Añadir al menos uno antes de confirmar. |
| "El precio calculado automáticamente es X pero el precio actual es Y..." | Editaste el precio a mano y el sistema lo detecta | Rellenar el campo **Motivo del Descuento** en el contrato resultante. |
| El vehículo que esperabas no aparece en la lista | Tiene contrato solapado, está en mantenimiento o es de otra categoría | Verificar fechas, estado del vehículo y categoría. |
| "El vehículo seleccionado no tiene una categoría asignada" | El modelo del vehículo no tiene categoría | Asignar categoría en *Flota → Modelos*. |

---

## Relacionado

- [05 · Consultas web y leads](./05-consultas-web-y-leads.md) — para reservas que vienen ya pagadas.
- [07 · Contratos](./07-contratos.md) — el contrato que se crea al final.
- [07 · Contratos · Contratos en grupo](./07-contratos.md#contratos-en-grupo) — cómo funciona el grupo creado por la Reserva Múltiple.
- [08 · Tarifas, seguros y depósitos](./08-tarifas-seguros-depositos.md) — origen del precio €/día calculado.

---

[← Volver al índice](./README.md) · Anterior: [05 · Consultas web y leads](./05-consultas-web-y-leads.md) · Siguiente: [07 · Contratos →](./07-contratos.md)
