# 11 · Daños y Rayones

[← Volver al índice](./README.md)

---

## Índice del capítulo

- [Visión general](#vision-general)
- [A · Wizard de daños](#wizard-de-danos)
- [B · Painter de daños (visual)](#painter-de-danos)
- [C · Painter para sustituciones](#painter-para-sustituciones)
- [D · Informes de rayones](#informes-de-rayones)
- [E · Cómo se cobra al cliente](#como-se-cobra-al-cliente)

---

## Visión general

El módulo gestiona los **daños y rayones** del vehículo con **tres herramientas complementarias**:

| Herramienta | Para qué |
|---|---|
| **Painter de daños** | Marcar visualmente **dónde** está el daño (sobre el dibujo del coche). |
| **Wizard de Daños** | Indicar **cuánto cuesta** el daño y emitir la factura al cliente. |
| **Informes de Rayones** (Scratch Reports) | Mantener un **histórico permanente** de daños conocidos por vehículo (referencia previa al alquiler). |

La idea es que ante una devolución con daños, el operario:

1. Compara con el **scratch report** previo (¿el rayón ya estaba?).
2. Si es nuevo, lo marca con el **painter** sobre el dibujo del coche.
3. Lanza el **wizard de daños** para emitir la factura al cliente.

---

## A. Wizard de Daños

### ¿Cuándo se usa?

Cuando se devuelve un vehículo con **desperfectos** que hay que cobrar al cliente. Es la forma de transformar un daño físico en una **factura emitida**.

### Cómo se invoca

Desde la ficha del **contrato** en estado **Devuelto**:

- Pulsar el botón **Damage Invoice** en la cabecera.

(Sólo aparece si todavía no se ha facturado daños y el contrato está en estado *Devuelto*.)

### Qué pide el wizard

Es una ventana modal (`vehicle.damage`) con:

| Campo | Notas |
|---|---|
| **Damage Amount** | Importe del daño (obligatorio). Lo decide el operario. |
| **Currency** | Moneda. |
| Pestaña **Descriptions** | Editor enriquecido (HTML) para describir el daño detalladamente: zona afectada, gravedad, tipo de reparación necesaria. |

Botones: **Create Invoice** y **Cancel**.

### Qué pasa al pulsar "Create Invoice"

1. Si el importe está vacío → aviso *"Please add the proper amount"*.
2. Guarda la descripción y el importe en el contrato (campos `damage_amount` y `damage_description`).
3. Crea una **factura de cliente** (`account.move` tipo `out_invoice`) en el journal de ventas del contrato, con una línea **"Vehicle Damage Amount"** por el importe indicado.
4. La factura se **publica automáticamente** (acción `action_post`).
5. La factura queda **enlazada al contrato** y el contrato se marca con `is_invoice_done = True`.

> Tras la factura, la **pestaña "Vehicle Damages"** del contrato se hace visible y muestra el importe, la descripción y la galería de fotos del daño.

---

## B. Painter de Daños

### ¿Qué es?

Una **herramienta visual sobre lienzo** (canvas HTML) que permite **señalar gráficamente** dónde está el daño en el dibujo del vehículo. Es como un editor de imagen ligero, dentro de Odoo, que se abre como ventana modal asociada al contrato.

### Cómo se accede

Desde el contrato:

- Pestaña **"Daños del Vehículo"** → botón **Abrir Editor de Daños**.

### Barra de herramientas del painter

| Herramienta | Para qué |
|---|---|
| **Marcador** (pincel) | Trazar líneas/manchas marcando golpes y rayones. |
| **Texto** | Anotar texto sobre la imagen (ej. "Abolladura puerta trasera"). |
| **Borrador** | Eliminar trazos. |
| **Paleta de colores** | **Naranja** (#FF8C00) — para daños menores. **Amarillo** (#FFD700) — para daños mayores. (Convención visual). |
| **Grosor** | Control deslizante 1 a 20. |
| **Tamaño de texto** | Control deslizante 12 a 48. |
| **Descargar** | Guarda la imagen anotada en local (PNG). |

### Lienzo

- Tamaño: **800 × 600 píxeles**.
- Cursor en cruz para precisión.
- **Imagen base**: carga la imagen previamente pintada si ya existía (para añadir nuevos daños sobre los anteriores); si no, un dibujo en blanco del coche.

### Pie del modal

| Botón | Qué hace |
|---|---|
| **Guardar** | Cierra el modal y persiste la imagen en el campo del contrato `painted_damage_image`. Hace commit explícito a base de datos para asegurar que se guarda. Notifica éxito o error. |
| **Cancelar** | Descarta cambios. |

### Cómo se usa en la práctica

1. El cliente devuelve el coche.
2. El operario abre el contrato → pestaña **Daños del Vehículo** → **Abrir Editor de Daños**.
3. Se abre el lienzo con el dibujo del coche.
4. Con el **marcador**, traza una línea sobre la zona dañada (ej. puerta trasera derecha).
5. Con la herramienta **Texto**, escribe una nota *"Abolladura ~5cm"*.
6. **Guardar**. La imagen queda asociada al contrato.
7. Lanza el **Wizard de Daños** para emitir la factura.

### Botón "Limpiar Imagen"

En la misma pestaña hay un botón **Limpiar Imagen** que **borra la imagen pintada** del contrato (vuelve al dibujo en blanco). Útil si te confundes y quieres rehacer.

---

## C. Painter para sustituciones

### ¿Qué es?

Variante específica del painter (`vehicle.substitution.damage.painter`) para cuando el cliente **cambia de vehículo durante un alquiler** (sustitución). Permite documentar daños **tanto del coche que devuelve como del coche que recoge**.

Ver [10 · Mantenimiento y sustituciones](./10-mantenimiento-y-sustituciones.md#e-sustituciones) para entender cuándo se usa el flujo de sustitución.

### Cuándo aparece

Dentro del **wizard de sustitución** (`vehicle.substitution.wizard`):

- **Paso 1 (Inspección del Vehículo Actual)** → botón **Abrir Editor de Daños - Vehículo Devuelto** (sólo si está marcado *¿Tiene daños?*).
- **Paso 3 (Inspección del Vehículo Sustituto)** → botón **Abrir Editor de Daños - Vehículo Sustituto** (sólo si está marcado *¿Tiene daños previos?*).

### Campos clave

| Campo | Significado |
|---|---|
| **Vehículo** | Vehículo concreto que se está revisando. |
| **Tipo de Daño** | *Vehículo Devuelto* (`old`) o *Vehículo Sustituto* (`new`). |
| **Imagen Base del Vehículo** | Se carga desde la imagen del vehículo si la hay. |

### Qué pasa al guardar

La imagen pintada se almacena en el wizard de sustitución como:

- `old_vehicle_painted_damage_image` (si era el devuelto).
- `new_vehicle_painted_damage_image` (si era el sustituto).

Y se marca el flag `..._has_damage = True` correspondiente, de modo que el proceso de sustitución **recuerda dónde tiene golpes cada vehículo**.

Estas imágenes acaban en el **anexo PDF de sustitución** que firma el cliente.

---

## D. Informes de Rayones (Scratch Reports)

### ¿Qué son?

Un **histórico permanente** (`vehicle.scratch.report`), **por vehículo**, de fotos de arañazos y desperfectos. Pensado para guardar como referencia el estado "anterior" del coche.

> Útil para que el equipo de devoluciones pueda **consultar rápidamente** si un golpe ya existía antes del alquiler en curso (y por tanto NO se cobra al cliente actual).

### Cómo acceder

**Vehicles Rental → Configuraciones → Informes de Rayones**

Vista lista con Nombre / Vehículo / Compañía y buscador por nombre.

### Campos del formulario

| Campo | Notas |
|---|---|
| **Name** | Obligatorio. Título descriptivo. Ej. *"Rayón puerta trasera derecha"* o *"Estado del coche el 01/05/2026"*. |
| **Vehicle** | Obligatorio. Selector contra `fleet.vehicle`. |
| **Compañía** | Obligatorio. |
| **Avatar / Imagen** | Obligatorio. Tamaño recomendado 250 × 250. Foto del rayón o del estado del coche. |

### Cómo se usa en la práctica

#### Antes del alquiler

1. Al recoger un coche del taller o de un alquiler anterior, hacer una **inspección 360°** y fotografiar cualquier daño previo.
2. **Vehicles Rental → Configuraciones → Informes de Rayones → Crear**.
3. Subir la foto, indicar vehículo y darle un nombre descriptivo.
4. Guardar. Ese rayón ya está registrado **a nombre del coche**, no de ningún cliente.

#### Cuando vuelve un cliente

1. Devuelve el coche con rayones.
2. Operario abre los **Scratch Reports del vehículo** (filtrar por `Vehicle`).
3. Compara: ¿este rayón está en los reports? Si sí → **NO se cobra**, ya existía.
4. ¿Es nuevo? → se documenta con el **painter** y se factura con el **wizard de daños**.

### Vinculación con el contrato

Dentro del contrato (campo *Custom Scratch Report* + *Scratch Report*), el operario puede **anexar el report relevante** al iniciar el alquiler como prueba documental del estado del vehículo antes de entregarlo al cliente.

---

## E. Cómo se cobra al cliente

### Flujo completo de cierre con daños

1. **El cliente devuelve el coche** → pulsar **Return** en el contrato → estado pasa a *Devuelto*.
2. **Inspección** del coche por el operario.
3. **Comparar con el Scratch Report** previo (si lo hubiera) para descartar daños preexistentes.
4. Si hay **daños nuevos**:
   - Abrir el **Painter** desde la pestaña *Daños del Vehículo* y marcar la zona.
   - Hacer fotos del daño y subirlas (al wizard o como adjuntos del contrato).
5. Pulsar **Damage Invoice** en la cabecera del contrato.
6. En el wizard, indicar:
   - **Damage Amount** (importe a cobrar).
   - **Descripción** detallada.
7. **Create Invoice** → factura emitida automáticamente al cliente.
8. Si además hay que **descontar del depósito**, ir al wizard **Return Deposit Invoice** y poner el importe a devolver = depósito − daños. Ver [12 · Devolución y facturación final](./12-devolucion-y-facturacion.md).

### Si el daño es alto y supera el depósito

- El **depósito se devuelve a 0 €** (o se mantiene como compensación parcial).
- La **factura de daños** queda emitida por el importe total.
- El cliente debe **pagar la diferencia** (depósito ya retenido + diferencia pendiente).

### Si el daño viene cubierto por el seguro

- El módulo **NO gestiona la reclamación al seguro automáticamente**. Es una gestión administrativa que se hace fuera de Odoo (con la compañía aseguradora).
- En el contrato puedes facturar el daño al cliente igualmente (que él reclame a su seguro) o no facturarlo (asumir el coste).
- Lo que sí queda **documentado** en Odoo es:
  - La **imagen pintada** del daño.
  - La **descripción** en el campo de daños.
  - La **factura** si la hay.

---

## Vista de los daños desde el contrato

Una vez registrados los daños, en la ficha del contrato verás:

### Pestaña "Daños del Vehículo"

Visor de la imagen pintada con el dibujo del coche y las marcas del operario. Acompañado de los botones:

- **Abrir Editor de Daños** — para volver a editar.
- **Limpiar Imagen** — para empezar de cero.

### Pestaña "Vehicle Damages"

Visible **sólo cuando ya hay factura de daños**. Contiene:

- **Damage Amount** (importe facturado).
- **Damage Description** (descripción HTML).
- **Galería de fotos** del daño (subidas como adjuntos).

---

## Validaciones y restricciones

- **Damage Amount > 0** para poder generar la factura.
- **No se puede facturar dos veces** los daños del mismo contrato (el botón *Damage Invoice* desaparece tras la primera factura).
- **Sólo en estado Devuelto** se pueden facturar daños. En *En Progreso* no aparece el botón.
- **Imagen pintada**: si el navegador del usuario es muy antiguo y el canvas falla, el painter avisa con error en el chat de Odoo.
- **El scratch report exige imagen**: no se puede guardar sin foto.

---

## Errores frecuentes

| Mensaje / Síntoma | Causa | Solución |
|---|---|---|
| *"Please add the proper amount"* | Damage Amount vacío al pulsar Create Invoice | Rellenar el importe. |
| El painter no carga imagen | Caché del navegador o problema de assets | Refrescar (Ctrl+F5). Si persiste, contactar a IT. |
| La imagen pintada no se guarda | Conexión interrumpida o sesión caducada | Volver a abrir, repetir, guardar (hay commit explícito). |
| El daño no aparece en el PDF del contrato | El PDF se generó antes de pintar | Volver a imprimir el PDF para que incluya la nueva imagen. |

---

## Ejemplos de uso

### Daño leve detectado en la devolución

> **Caso:** El cliente devuelve el coche con un pequeño abollón en el parachoques trasero.

1. Operario pulsa **Return** en el contrato.
2. Abre la pestaña *Daños del Vehículo* → **Abrir Editor de Daños**.
3. En el lienzo, marca con el rotulador **naranja** el parachoques trasero.
4. Añade texto *"Abolladura 3cm, parachoques trasero, sin pintura levantada"*.
5. Guarda.
6. Pulsa **Damage Invoice** en la cabecera. Importe: 80 €. Descripción: pega la nota del párrafo anterior.
7. **Create Invoice** → factura de 80 € emitida.
8. Pulsa **Return Deposit Invoice**. Importe a devolver: depósito (150 €) − daños (80 €) = **70 €**.
9. Cliente recibe nota de crédito de 70 € + factura de daños de 80 €. Saldo neto: 10 € (mantiene esa parte el cliente paga 0, recibe 70 del depósito, pero le emiten una factura de 80 → debe 10).

### Daño preexistente que aparece en el scratch report

> **Caso:** Cliente devuelve con un rayón en la puerta. Operario sospecha que ya estaba.

1. Filtra **Scratch Reports** por el vehículo.
2. Encuentra un report del mes pasado *"Rayón puerta trasera derecha"* con foto.
3. Compara: es el mismo rayón.
4. **NO emite factura de daños**.
5. Devuelve el depósito íntegro al cliente.

### Daño grave durante el alquiler que motiva sustitución

> **Caso:** A mitad del alquiler, el coche choca y queda inutilizable. Se sustituye.

1. Se sigue el flujo de [sustitución](./10-mantenimiento-y-sustituciones.md#e-sustituciones).
2. En el **paso 1** del wizard de sustitución, se marca *Tiene daños* → se pinta sobre el painter de sustitución → se indica un **monto estimado** del daño.
3. Al confirmar, **el sistema crea automáticamente la factura de daños** sin intervención manual adicional.
4. El alquiler continúa con el coche sustituto.

---

## Relacionado

- [07 · Contratos](./07-contratos.md#pestanas-del-contrato) — pestañas *Daños del Vehículo* y *Vehicle Damages*.
- [10 · Mantenimiento y sustituciones](./10-mantenimiento-y-sustituciones.md#e-sustituciones) — painter para sustituciones.
- [12 · Devolución y facturación final](./12-devolucion-y-facturacion.md) — proceso completo de cierre.
- [14 · Emails y reportes](./14-emails-y-reportes.md) — reporte PDF de scratch report.

---

[← Volver al índice](./README.md) · Anterior: [10 · Mantenimiento y sustituciones](./10-mantenimiento-y-sustituciones.md) · Siguiente: [12 · Devolución y facturación final →](./12-devolucion-y-facturacion.md)
