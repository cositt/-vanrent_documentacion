# 08 · Tarifas, Seguros y Depósitos

[← Volver al índice](./README.md)

---

## Índice del capítulo

- [A · Tarifas de vehículos](#tarifas-de-vehiculos)
- [B · Tarifas de seguros](#tarifas-de-seguros)
- [C · Reglas de depósito](#reglas-de-deposito)
- [D · Pólizas de seguro (documentales)](#polizas-de-seguro)
- [E · Opciones de pago (cuotas)](#opciones-de-pago)
- [F · Servicios extras](#servicios-extras)
- [G · Cómo se calcula el total del contrato](#calculo-del-total)

---

## A. Tarifas de vehículos

### ¿Para qué sirven?

Las **Tarifas de Vehículos** (`vehicle.pricing.rule`) definen el **precio por día (o por paquete)** del alquiler, en función de:

- La **categoría** del vehículo (Tipo A, Tipo C, Tipo F, etc.).
- Los **kilómetros incluidos**.
- La **duración** del alquiler.
- El **periodo de vigencia** (alta/baja temporada, promociones).

Sin tarifa aplicable no se calcula precio automático: el contrato exige introducirlo a mano.

### Dónde se configuran

**Vehicles Rental → Tarifas de Vehículos**

Por defecto, la pantalla aplica dos filtros automáticos: **Activas** y **Vigentes hoy**.

### Vista lista

| Columna | Significado |
|---|---|
| **Categoría de Vehículo** | Tipo A, Tipo C, etc. |
| **Tipo** | Estándar / FLEXIRENT — color azul si FLEXIRENT, gris si está desactivada. |
| **Km Límite** | Tope de km incluidos. |
| **Duración** | Rango (`1-2d`, `3-5d`, etc.). |
| **Km Incluidos** | Cantidad numérica. |
| **Días Paquete** | Solo FLEXIRENT. |
| **Precio/Día** | Con IVA. |
| **Sin IVA** | Calculado. |
| **Vigente Desde / Hasta** | Periodo de validez. |

Filtros: Activas, Inactivas, Tarifas Estándar, FLEXIRENT, Vigentes Hoy.

### Vista formulario

#### Cabecera

- **Duplicar con Nuevas Fechas** — copia la tarifa para un periodo nuevo sin rellenarla otra vez. Útil para crear tarifas de temporada.
- **Archivar/Desarchivar** (estrella) — oculta tarifas obsoletas sin borrarlas. Si está inactiva aparece un cartel rojo *"Inactiva"*.
- **Nombre** — se genera automáticamente, no se edita:
  - Estándar: `Tipo A - 100 Km - 1-2 días`.
  - FLEXIRENT: `Tipo C - FLEXIRENT 3000km / 30 días`.

#### Información General

| Campo | Notas |
|---|---|
| **Categoría de Vehículo** | Obligatorio. Tipo A, Tipo C, etc. |
| **Tipo de Tarifa** | *Tarifa Estándar* (precio por día) o *Flexirent – Larga Temporada* (paquete cerrado de días con km incluidos). |
| **Compañía** | En entornos multicompañía. |

#### Vigencia

- **Válido Desde** — obligatorio. Por defecto, hoy.
- **Válido Hasta** — vacío = sin caducidad.

#### Pestaña "Configuración Estándar" (sólo si Estándar)

- **Rango de Kilometraje**: 100 Km · 350 Km · 500 Km · 650 Km · 850 Km · Sin límite.
- **Rango de Duración**: 4h (mañana o tarde) · 1-2 días · 3-5 días · 6-10 días · 11-20 días · 21-29 días.
- **Precio (IVA inc.)** y **Precio sin IVA** (divide entre 1,21).
- Aviso azul: *"Esta tarifa se aplica por día de alquiler. El total se calcula multiplicando este precio por el número de días."*

#### Pestaña "Configuración FLEXIRENT" (sólo si FLEXIRENT)

- **Kilometraje Total Incluido**: ej. 3.000 km para un paquete de 30 días.
- **Duración (días)**: por defecto 30.
- **Precio por Km Extra**: por defecto 0,21 €. Se cobra cuando el cliente excede los km incluidos.
- **Precio Total Paquete** (IVA inc. y sin IVA).
- Aviso amarillo: *"FLEXIRENT: Paquete de larga temporada. El precio es total del paquete, no por día. Los km adicionales se cobran a X €/km + IVA."*

#### Pestaña "Notas y Condiciones"

Campo libre para condiciones especiales o restricciones.

### Cómo se aplican al crear un contrato

Cuando rellenas un contrato y Odoo conoce **vehículo + fechas + km totales**, busca la tarifa con estos criterios:

1. Misma **categoría** que el vehículo del contrato.
2. **Tipo estándar** (la búsqueda principal usa siempre estándar; FLEXIRENT requiere selección expresa).
3. **Rango de km** que cubre el total (≤100, ≤350, ≤500, ≤650, ≤850 o sin límite).
4. **Rango de duración** que encaja con los días del alquiler.
5. Que **hoy** esté dentro de *Válido Desde / Válido Hasta*.
6. **Activa**.

La tarifa encontrada se muestra como **"Tarifa Aplicada"** en el contrato y su precio rellena el campo **"Precio Calculado (Vehículo)"**.

> Si el usuario **cambia el precio a mano**, Odoo lo detecta (`price_manually_modified = True`) y **exige rellenar el campo "Motivo del Descuento"**.

Si no se encuentra tarifa, no bloquea el formulario — sólo deja el precio a 0 y registra un aviso.

### Validaciones

- Fecha "Válido Hasta" debe ser **posterior** a "Válido Desde".
- Precio **mayor que cero**.
- Tarifa estándar exige Km y Duración; FLEXIRENT exige Km Total.
- **No puede haber dos tarifas** con la misma combinación de categoría + tipo + km + duración + fecha de inicio + compañía.

### Ejemplos

- **Alta temporada (julio-agosto)**: duplicas las tarifas activas con el botón *Duplicar con Nuevas Fechas*, subes precio y pones `Válido Desde 2026-07-01 / Válido Hasta 2026-08-31`.
- **Baja temporada**: tarifas con precios más bajos para "11-20 días" y "21-29 días" del Tipo A.
- **Descuento por duración**: la propia tabla ya está pensada así — el precio/día baja cuando el rango de duración sube.
- **FLEXIRENT**: paquete cerrado tipo "3.000 km / 30 días = 1.200 €", todo lo que exceda se cobra a 0,21 €/km.

---

## B. Tarifas de seguros

### Tipos de seguro disponibles

Sólo hay **dos coberturas**:

- **Seguro Básico – Franquicia 300 €**: el cliente paga los primeros 300 € de daños.
- **Seguro Sin Franquicia**: cobertura total de daños. El cliente no paga nada.

### Dónde se configuran

**Vehicles Rental → Tarifas de Seguros**

Filtros automáticos: *Activas + Vigentes Hoy*.

### Vista lista

| Columna | Significado |
|---|---|
| **Tipo de Seguro** | Verde = Básico · Naranja = Sin Franquicia. |
| **Rango de Duración** | 1-3 días, 4-7 días, etc. |
| **Tipo de Conductor** | Normal / Especial. |
| **Precio por Día** | Con IVA y sin IVA. |
| **Válido Desde / Hasta** | Periodo. |

### Vista formulario — campos

| Campo | Notas |
|---|---|
| **Tipo de Seguro** | Básico (Franquicia 300€) / Sin Franquicia. |
| **Rango de Duración** | 1-3 días · 4-7 días · 8-15 días · 16-30 días · 31-60 días · 61-180 días · 181-365 días. |
| **Tipo de Conductor** | *Normal* o *Especial (<25 o >60 años)* — el especial paga más caro. |
| **Precio por Día (IVA incluido)** | Editable. |
| **Precio por Día (sin IVA)** | Calculado. |
| **Válido Desde / Hasta** | Periodo. |
| **Notas y Coberturas** | Texto libre. |

Una **bandera lateral verde "Sin Franquicia"** aparece cuando se elige esa modalidad.

**Avisos contextuales** dentro del formulario:

- Si tipo = Básico → cartel azul con coberturas y aviso de franquicia.
- Si tipo = Sin Franquicia → cartel verde "Cobertura total de daños sin franquicia".
- Si conductor = Especial → cartel amarillo recordando el motivo de la tarifa más alta.

### Cómo se asignan

**El seguro NO se asigna por vehículo ni por categoría**. Se aplica a **todos los contratos** — es **obligatorio**.

La tarifa que aplica al contrato se decide cruzando tres parámetros que el operario indica en el contrato:

1. **Tipo de seguro** elegido (Básico / Sin Franquicia).
2. **Días totales** del alquiler → determina el rango de duración.
3. **¿Conductor especial?** (checkbox del contrato — se rellena según fecha de nacimiento del cliente).

Es decir, hay **una tarifa por cada combinación (tipo × duración × tipo conductor)**. Cualquiera que sea el vehículo, se cobra el precio de esa fila.

### Cómo aparece en el contrato

Bloque **"🛡️ Seguro y Cobertura"** del contrato:

| Campo | Notas |
|---|---|
| **Tipo de Seguro** | Obligatorio. Por defecto *Básico*. Editable sólo en Borrador. |
| **Conductor Especial (-25 o +60 años)** | Checkbox. |
| **Precio Seguro/Día** | Calculado. Sólo lectura. |
| **Total Seguro** | = precio/día × días totales. Sólo lectura. |

Debajo aparece un cartel azul (Básico) o verde (Sin Franquicia) recordando la cobertura.

### Precio de respaldo

Si no se ha definido tarifa para la combinación buscada, se aplica un **precio de respaldo**:

- **8 €/día para Básico**.
- **12 €/día para Sin Franquicia**.

### Validaciones

- Precio **mayor que cero**.
- Fecha Hasta posterior a Desde.
- No puede haber dos tarifas con (tipo + duración + conductor + fecha inicio + compañía) repetidos.

---

## C. Reglas de depósito

### ¿Qué es el depósito dinámico?

Es el sistema que calcula **automáticamente la fianza** que debe pagar el cliente al alquilar, según:

1. La **categoría del vehículo**.
2. El **tipo de tarjeta** con la que va a pagar (débito / crédito).
3. Opcionalmente, un **porcentaje sobre el precio del alquiler**.

> Las tarjetas de **crédito** suelen tener depósito menor (porque garantizan el cobro mejor). Las de **débito** llevan depósito mayor.

En la **web pública**, el sistema detecta el tipo de tarjeta de forma automática consultando los **6 primeros dígitos (BIN)** contra el servicio externo **Freebinchecker**, y muestra al cliente el depósito recalculado en tiempo real.

### Dónde se configura

**Vehicles Rental → Reglas de Depósitos**

### Vista lista

| Columna | Significado |
|---|---|
| **Nombre** | Auto: "Tipo A - Tarjeta de Débito". |
| **Categoría de Vehículo** | A qué categoría aplica. |
| **Tipo de Tarjeta** | Etiqueta naranja = Débito · Azul = Crédito. |
| **Depósito Fijo** | Importe fijo. |
| **% Alquiler** | Porcentaje sobre el alquiler. |
| **Válido Desde / Hasta** | Periodo. |
| **Activa** | Interruptor. |

### Vista formulario

#### Identificación

- Botón estrella **Archivar**.
- **Categoría de Vehículo** (obligatoria).
- **Tipo de Tarjeta**: Débito / Crédito.

#### Configuración de Depósito

| Campo | Significado |
|---|---|
| **Depósito Fijo** | Importe en €. |
| **Porcentaje sobre Alquiler (%)** | Ej. 15 = 15 % del precio del alquiler. |
| **Aplicar Ambos (Fijo + Porcentaje)** | Si marcado: depósito = fijo **+** porcentaje. Si no: se toma **el mayor de los dos**. |
| **Depósito Mínimo** | Nunca cobrar menos que esto. |
| **Depósito Máximo** | Nunca cobrar más que esto (0 = sin tope). |

#### Vigencia

- **Válido Desde / Hasta**.

#### Información Adicional

- Moneda, Compañía, Notas.

### Cómo se aplica al crear el contrato

En el contrato hay un selector **"Tipo de Tarjeta para Depósito"** (débito/crédito). Cuando se rellena **vehículo + tipo de tarjeta**, Odoo:

1. Busca la regla vigente para esa categoría y tipo de tarjeta.
2. Calcula el depósito sobre el precio total del alquiler.
3. Muestra el resultado en el campo **"Depósito Calculado (Automático)"**.

Si está activo el flag **"Usar Depósito de Regla"** (por defecto sí), ese importe se usa en la facturación. Si se desactiva, el usuario puede introducir un depósito manual.

### Ejemplos

**Ejemplo 1 — Depósito fijo simple**:
- Tipo A · Tarjeta Débito → Fijo 50 € · Mínimo 50 € · Máximo sin límite.
- Tipo A · Tarjeta Crédito → Fijo 25 € · Mínimo 25 € · Máximo sin límite.

**Ejemplo 2 — Depósito por porcentaje con horquilla**:
- Tipo C · Débito → Fijo 0 € · Porcentaje 15 % · Aplicar Ambos = No · Mínimo 30 € · Máximo 150 €.
- Resultado para un alquiler de 100 € → 15 % = 15 € → se aplica el **mínimo (30 €)**.
- Resultado para un alquiler de 1.500 € → 15 % = 225 € → se aplica el **máximo (150 €)**.

### Devolución del depósito

El contrato dispone, además del depósito normal, de:

- **Factura de Depósito** (`deposit_invoice_id`) con su estado de pago.
- **Factura de Devolución de Depósito** (`return_deposit_invoice_id`) con su estado.

Es decir, se cobra al inicio con una factura, y al cerrar se emite una nota de crédito para reintegrar al cliente (total o parcial si hubo daños).

Cuando el cliente paga la reserva por web con **Redsys**, el sistema crea **automáticamente** la factura del depósito y la marca como **pagada y conciliada** contra el diario bancario de Redsys.

Ver [12 · Devolución y facturación final](./12-devolucion-y-facturacion.md) para el detalle del cierre.

### Validaciones

- Al menos uno de Fijo o Porcentaje > 0.
- Mínimo ≤ Máximo (si Máximo > 0).
- No puede repetirse (categoría + tarjeta + fecha + compañía).

---

## D. Pólizas de seguro

### ¿Para qué sirven?

Las **Pólizas de Seguro** (`insurance.policy`) permiten **archivar pólizas de seguro asociadas a un contrato concreto**: número, importe, documento PDF/imagen escaneada.

> **NO se usan para tarificar** — eso lo hacen las [Tarifas de Seguros](#tarifas-de-seguros). Las pólizas son un **registro documental**: la póliza real del vehículo o del contrato.

### Campos

| Campo | Notas |
|---|---|
| **Policy Number** | Número de póliza. Obligatorio. |
| **Name** | Nombre de la póliza. Obligatorio. |
| **Description** | Descripción libre. |
| **File Name** | Nombre del archivo adjunto. |
| **Document** | Campo binario: PDF/imagen de la póliza. |
| **Policy Amount** | Importe de la póliza. Debe ser > 0. |
| **Compañía** y **Moneda** | Heredadas. |
| **Vehicle Contract** | Contrato al que pertenece. Se borra en cascada si se borra el contrato. |

Las pólizas viven **dentro del contrato** como lista adjunta (pestaña *Insurance Policy*). No tienen menú propio.

---

## E. Opciones de pago

### ¿Para qué sirven?

Las **Opciones de Pago** (`vehicle.payment.option`) definen el **calendario de cobros del contrato**: las cuotas en las que se le va a facturar al cliente.

### Modalidades disponibles

El contrato tiene un campo **Tipo de Pago** con estas opciones:

| Valor | Significado |
|---|---|
| **Daily** | Diario — una cuota por día. |
| **Weekly** | Semanal — una cuota por semana. |
| **Monthly** | Mensual — una cuota por mes. |
| **Quarterly** | Trimestral — una cuota por trimestre. |
| **Yearly** | Anual — una cuota por año. |
| **Full Payment** | Pago único — una sola cuota por el total. |

Combinado con el **Tipo de Tarifa** (`rent_type`) del contrato (Horas / Días / Semanas / Meses / Años / Kilómetros / Millas).

### Cómo se eligen en el contrato

En la pestaña **Vehicle Payment Details** del contrato se ve la lista de cuotas generadas. Cada cuota tiene:

| Campo | Significado |
|---|---|
| **Name** | Nombre/concepto, traducible (ej. *Cuota Enero*, *Pago Inicial*). |
| **Payment Date** | Fecha de cobro. Obligatoria. |
| **Payment Amount** | Importe. |
| **Invoice Item** | Producto que aparecerá en la factura. |
| **Invoice** | Factura generada (vínculo). |
| **Payment State** | Sincronizado desde la factura: No pagado / Parcial / Pagado / Reembolsado. |

### Qué pasa al pulsar "Crear factura de cuota"

Al ejecutar **Create Invoice** sobre una cuota, Odoo genera una factura del cliente que incluye **automáticamente, en este orden**:

1. **Alquiler del vehículo** (con el importe de la cuota).
2. **Seguro** (precio/día × días totales).
3. **Depósito de Seguridad** (el calculado de la regla si está activo, o el manual).
4. **Km Extra (FLEXIRENT)** si el alquiler ha excedido el paquete.
5. **Servicios Extras** del contrato.
6. **Cargos Extras** (horas/días/semanas/meses extras pasados sobre lo contratado).

El importe total de la factura sustituye al *Payment Amount* de la cuota.

> Si la cuota tiene importe 0 → aviso amarillo *"Please add the proper payment amount"* y no se crea.

---

## F. Servicios extras

### Catálogo

Los **Servicios Extras** (`extra.service`) son **productos** del catálogo de Odoo marcados como tales: silla bebé, GPS, conductor adicional, cadenas, portaesquís, etc.

Se crean desde **Inventario / Ventas → Productos** y se añaden al contrato como líneas de servicio.

### Campos por línea (dentro del contrato)

| Campo | Notas |
|---|---|
| **Product** | Producto. Obligatorio. Se selecciona del catálogo. |
| **Description** | Descripción libre que sustituye al nombre del producto en la factura. |
| **Quantity** | Por defecto 1. |
| **Amount** | Precio unitario. Al elegir un producto se autocompleta con su precio de tarifa (`lst_price`) y se puede ajustar. |
| **Sub Total** | Calculado = cantidad × importe. |
| **Moneda** | Heredada de la compañía. |
| **Vehicle Contract** | Contrato al que pertenece. |

### Cómo se añaden

En el contrato, pestaña **Extra Services**:

1. Pulsar **Añadir línea**.
2. Elegir el producto.
3. Ajustar cantidad y descripción si quieres.
4. El precio se rellena solo desde el catálogo.

El total se suma al campo **Total Cargos por Servicios Extras** (`extra_service_charge`) y entra en el **Total General con IVA**.

Cuando se genera la factura (manual o automática), **cada extra produce una línea propia** con su descripción, cantidad, precio y los impuestos del propio producto.

### Ejemplos

- **GPS Navegador** — 5 €/día.
- **Silla de Bebé Grupo 1** — 3 €/día.
- **Conductor Adicional** — precio fijo por contrato.
- **Cadenas de Nieve** — 8 €/día.

---

## G. Cómo se calcula el total del contrato

La **lógica de tarificación** (`vehicle.contract.pricing`) junta todo lo anterior y calcula el total. El usuario en pantalla sólo ve los campos resultado, pero conviene entender el cálculo paso a paso.

### Paso 1 — Precio del alquiler del vehículo

- `calculated_rent` ← buscar tarifa estándar más reciente activa para la categoría del vehículo.
- Si el campo *Renta* del contrato está vacío, se autoasigna.
- Si el usuario lo edita a mano, se marca como modificado y se exige **Motivo del Descuento** (salvo que venga de reserva web).

### Paso 2 — Subtotal Vehículo

`subtotal_vehicle_rent = renta × total_días + coste_km_extra`

> El coste km extra sólo aplica si la tarifa es **FLEXIRENT** y el cliente ha pasado el paquete: `extra_km_used × precio_km_extra`.

### Paso 3 — Coste del Seguro

- Se busca la tarifa de seguro por (tipo + rango de días + conductor especial).
- `total_insurance_cost = precio_seguro_por_día × total_días`.
- Si no hay tarifa: precio por defecto 8 € (Básico) o 12 € (Sin Franquicia).

### Paso 4 — Depósito

- Si **"Usar Depósito de Regla"** está activo → `calculated_deposit = regla.calcular_depósito(subtotal_vehículo)`.
- Si no → el depósito es el manual introducido por el usuario.

### Paso 5 — Servicios Extras

Suma de todas las líneas de extras (`extra_service_charge`).

### Paso 6 — Cargos Extras (penalizaciones)

Por horas, días, semanas, meses o años contratados de más.

### Paso 7 — Totales finales

- **Total General (IVA incluido)** = Subtotal Vehículo + Total Seguro + Servicios Extras.
- **Total General (sin IVA)** = el anterior dividido entre 1,21.
- **El depósito y los cargos extras** aparecen como líneas separadas en la factura final.

### Reglas especiales y avisos en pantalla

- **Aviso amarillo** si cambias el precio del alquiler: *"El precio calculado automáticamente es X €/día pero el precio actual es Y €/día. Si esto es intencional, indica el motivo en Motivo del Descuento"*.
- **Bloqueo** si el vehículo no tiene categoría asignada: *"El vehículo seleccionado no tiene una categoría asignada"*.
- **Botón "Recalcular Precio"** en el contrato: aplica de nuevo las tarifas vigentes. Notificación verde *"Precio Recalculado"* o amarilla *"No se encontró tarifa"*.
- **Botón "Ver Tarifa Aplicada"**: abre la ficha de la regla de tarificación que se está usando.
- **Aviso FLEXIRENT** (amarillo) cuando se han usado km extras: *"Se han utilizado N km adicionales"*.
- **Carteles informativos** según tipo de seguro (azul Básico / verde Sin Franquicia).

### Botón "Crear Factura Automática"

Genera una factura del cliente con:

- Línea 1: **Alquiler del vehículo**, descripción con matrícula, fechas, días y km.
- Línea 2: **Seguro** (siempre, con etiqueta "Conductor Especial" si aplica).
- Línea 3: **Km Extra FLEXIRENT** si los hay.
- Línea 4 y siguientes: una línea por cada **servicio extra**.

La factura queda vinculada al contrato y aparece en el panel de control de alquileres.

---

## Resumen rápido para el operario

| Tarea | Menú |
|---|---|
| Cambiar precio del alquiler de Tipo A en alta temporada | Vehicles Rental → Tarifas de Vehículos |
| Subir el precio del seguro Sin Franquicia para alquileres largos | Vehicles Rental → Tarifas de Seguros |
| Cambiar el depósito que se cobra a tarjetas débito | Vehicles Rental → Reglas de Depósitos |
| Añadir GPS o silla de bebé a un contrato | Contrato → pestaña *Servicios Extras* |
| Recalcular precio si cambias fechas o km | Contrato → botón *Recalcular Precio* |
| Ver qué tarifa se está aplicando | Contrato → botón *Ver Tarifa Aplicada* |

---

## Relacionado

- [07 · Contratos](./07-contratos.md) — donde se ven aplicados los precios calculados.
- [09 · Pagos y Redsys](./09-pagos-y-redsys.md) — cobro online del depósito y de las cuotas.
- [12 · Devolución y facturación final](./12-devolucion-y-facturacion.md) — devolución del depósito.
- [03 · Flota y vehículos](./03-flota-y-vehiculos.md) — el campo *Categoría* del vehículo es crítico para que aplique tarifa.

---

[← Volver al índice](./README.md) · Anterior: [07 · Contratos](./07-contratos.md) · Siguiente: [09 · Pagos y Redsys →](./09-pagos-y-redsys.md)
