# 15 · Configuración

[← Volver al índice](./README.md)

---

## ¿Qué hay aquí?

El menú **Vehicles Rental → Configuraciones** agrupa la configuración de catálogos auxiliares:

- Políticas de Cancelación.
- Términos de Acuerdo de Alquiler.
- Informes de Rayones (ya cubiertos en [11 · Daños y rayones](./11-danos-y-rayones.md#d-informes-de-rayones-scratch-reports)).
- Horarios de Mantenimiento (ya cubiertos en [10 · Mantenimiento y sustituciones](./10-mantenimiento-y-sustituciones.md#b-horarios-de-mantenimiento)).

Adicionalmente, otras configuraciones que no están bajo este menú pero conviene conocer:

- Secuencias de numeración (contratos, grupos, ampliaciones, etc.).
- Productos especiales que usa el módulo.
- Permisos y grupos de seguridad.

---

## A. Políticas de Cancelación

### ¿Qué son?

Las **Políticas de Cancelación** (`cancellation.policy`) son el **catálogo de plantillas de condiciones** que regulan qué pasa si el cliente cancela una reserva o contrato.

### Cómo acceder

**Vehicles Rental → Configuraciones → Políticas de Cancelación**

### Campos

| Campo | Notas |
|---|---|
| **Title** | Nombre comercial (obligatorio). Ej.: *"Política Flexible"*, *"Política No Reembolsable"*, *"Política 48h"*. |
| **Compañía** | A qué compañía aplica (multi-empresa). |
| **Terms & Conditions** | Editor HTML con el texto completo de la cláusula que verá el cliente. |

### Cómo se aplican

1. Crear las políticas necesarias en este menú.
2. Al crear un contrato (manual o desde la web), elegir la política aplicable en la pestaña **Términos de Alquiler** o **Cancellation Policy**.
3. El texto aparece en el **contrato PDF** y en los **emails al cliente**.
4. Si el cliente cancela el contrato (estado *Cancelado*), la política rige las condiciones del reembolso.

### Ejemplos de políticas típicas

#### Política Flexible

> *"Si el cliente cancela antes de las 24 horas previas a la fecha de inicio, se devolverá el 100 % del importe pagado. Cancelaciones posteriores se descontará el equivalente a 1 día de alquiler."*

#### Política Estándar 48h

> *"Cancelaciones antes de 48h: reembolso del 100 % del alquiler (mantenido el depósito). Cancelaciones entre 24-48h: reembolso del 50 %. Cancelaciones con menos de 24h: sin reembolso."*

#### Política No Reembolsable

> *"Una vez confirmada la reserva, el importe no es reembolsable. El depósito se devuelve en cualquier caso si no hay incidencias."*

### Uso desde el contrato

Cuando un contrato pasa a **Cancelado**, en su pestaña *Cancellation Policy*:

1. Eliges la **Policy** del catálogo.
2. El campo **Terms & Conditions** se autorrellena con el texto de la política.
3. Indicas el **Cancellation Charge** (importe a cobrar).
4. Pulsas **Cancellation Charge** → factura emitida al cliente con el cargo de cancelación.

---

## B. Términos de Acuerdo de Alquiler

### ¿Qué son?

Las **Cláusulas Generales** del contrato de alquiler (`rental.agreement.terms`) — el "letra pequeña" que se muestra en el PDF del contrato y, cuando procede, en la web pública.

### Cómo acceder

**Vehicles Rental → Configuraciones → Términos de Acuerdo de Alquiler**

### Campos

| Campo | Notas |
|---|---|
| **Title** | Nombre del bloque de términos (obligatorio). Ej.: *"Condiciones Generales SUNSET 2025"*, *"Términos Furgonetas Pinveco"*. |
| **Compañía** | Compañía emisora (obligatorio). |
| **Terms and Conditions** | Editor HTML con todas las cláusulas (responsabilidad civil, kilometraje, combustible, depósito, sanciones, etc.). |

### Cómo se usan

1. Crear los textos desde este menú con el editor enriquecido (negrita, listas, enlaces, imágenes).
2. Al crear un contrato, en la pestaña **Términos de Alquiler**:
   - Elegir el **Acuerdo de Alquiler** del catálogo.
   - Al seleccionarlo, **rellena automáticamente** el campo *Terms & Conditions* del contrato.
3. El texto aparece en el **PDF del contrato** que se imprime y se envía al cliente.

### Cláusulas típicas que debe contener

- **Responsabilidad civil del cliente**: qué pasa si causa un accidente.
- **Kilometraje**: límite incluido y precio del km extra.
- **Combustible**: política de "lleno a lleno" o equivalente.
- **Depósito**: importe, condiciones de retención y devolución.
- **Sanciones de tráfico**: a cargo del cliente; recargo administrativo por gestión.
- **Limpieza extrema**: cuándo aplica un cargo de limpieza.
- **Daños**: cómo se valoran y se cobran.
- **Conductor adicional**: si se permite y a qué coste.
- **Edad mínima del conductor** y **antigüedad del carnet**.
- **Devolución fuera de horario**.
- **Robo o pérdida de las llaves**.
- **Jurisdicción aplicable**.

### Ejemplo de uso

> **Caso:** Tienes dos textos legales: uno para Sunset (coches) y otro para Pinveco (furgonetas profesionales, con cláusulas específicas de carga).

1. Crear *"Condiciones Generales Sunset 2026"* — asignar a la compañía Sunset.
2. Crear *"Condiciones Generales Pinveco 2026"* — asignar a la compañía Pinveco.
3. Cada vez que se crea un contrato en una compañía u otra, eliges el acuerdo correspondiente.

---

## C. Horarios de Mantenimiento

Ya cubiertos en [10 · Mantenimiento y sustituciones · B](./10-mantenimiento-y-sustituciones.md#b-horarios-de-mantenimiento).

### Resumen

**Vehicles Rental → Configuraciones → Horarios de Mantenimiento**

Cada horario es un "patrón" del tipo "Cada X días" que se asigna a un vehículo para que el cron diario genere automáticamente nuevas órdenes de mantenimiento.

---

## D. Informes de Rayones

Ya cubiertos en [11 · Daños y rayones · D](./11-danos-y-rayones.md#d-informes-de-rayones-scratch-reports).

### Resumen

**Vehicles Rental → Configuraciones → Informes de Rayones**

Catálogo fotográfico de daños conocidos por vehículo. Se consulta antes de cada devolución para descartar daños preexistentes.

---

## E. Otras configuraciones

### Categorías de Vehículo

Las **categorías** (`fleet.vehicle.category` extendido por el módulo) — Tipo A, Tipo C, Tipo F, etc. — son **el pilar** sobre el que se montan las [tarifas](./08-tarifas-seguros-depositos.md#a-tarifas-de-vehiculos), [seguros](./08-tarifas-seguros-depositos.md#b-tarifas-de-seguros) y [reglas de depósito](./08-tarifas-seguros-depositos.md#c-reglas-de-deposito).

#### Cómo acceder

**Flota → Configuración → Categorías de Vehículo** (en la app *Flota* estándar de Odoo, no en *Vehicles Rental*).

#### Campos

- **Nombre**: identificador (*Tipo A*, *Tipo C*…).
- **Tipo**: Coche / Furgoneta / etc.
- **Compañía**.

> **Importante**: una vez en producción, **no cambies el nombre interno** de una categoría que ya tenga tarifas asignadas, o las relaciones se pueden romper.

### Modelos de Vehículo

**Flota → Configuración → Modelos de vehículos**

El módulo añade el **tipo "Furgoneta"** a los modelos estándar de Odoo. Cuando el tipo es *Coche* o *Furgoneta*, se muestran los bloques *Modelo* (asientos, color, año) y *Motor* (combustible, transmisión, potencia, cilindrada, emisiones).

Ver [03 · Flota y vehículos](./03-flota-y-vehiculos.md#modelos-de-vehiculo).

### Secuencias de numeración

Las secuencias (`ir.sequence`) definen el formato y el contador de los códigos automáticos:

| Secuencia | Formato | Uso |
|---|---|---|
| **Vehicle Contract** | `Contracts/0001`, `Contracts/0002`... | Referencia del contrato. |
| **Vehicle Contract Group** | `Group/0001`... | Referencia del grupo. |
| **Multi Booking** | `BR/00001`... | Referencia de la reserva múltiple. |
| **Vehicle Substitution** | `SUB/0001`... | Referencia de la sustitución. |
| **Vehicle Extension** | `EXT/0001`... | Referencia de la ampliación. |

#### Cómo cambiar el formato o el contador

1. **Activar modo desarrollador**.
2. **Ajustes → Técnico → Secuencias**.
3. Buscar la secuencia por nombre.
4. Editar **Prefijo**, **Sufijo**, **Tamaño del número**, **Padding**, **Próximo número**.

> Cuidado al cambiar el contador hacia atrás: puedes generar referencias duplicadas.

### Productos especiales

El módulo usa varios **productos** internos que deben existir en el catálogo:

| Producto | XML ID | Uso |
|---|---|---|
| **Vehicle Rent Charge** | `vehicle_rent_charge` | Líneas de cuotas y ampliaciones. |
| **Vehicle Rent Deposit** | `vehicle_rent_deposit` | Depósito y devolución (nota de crédito). |
| **Vehicle Rent Extra Charge** | `vehicle_rent_extra_charge` | Cargos por exceso de tiempo/km. |
| **Vehicle Damage Amount** | `vehicle_damage_amount` | Importe de daños. |
| **Vehicle Contract Cancellation Charge** | `vehicle_contract_cancellation_charge` | Cargo de cancelación. |

#### Cómo verificarlos

**Inventario → Productos** (o **Ventas → Productos**).

Si alguno no existe, **el módulo falla** al intentar facturar. Hay que crearlo o reinstalar el módulo.

### Diario contable

Cada compañía necesita un **Diario de tipo "Ventas"** configurado para que el módulo pueda emitir facturas.

#### Cómo verificarlo

**Contabilidad → Configuración → Diarios** — debe existir al menos uno con tipo *Sales*.

> Si no hay, todas las acciones de facturar abortan con mensaje guía hacia *Contabilidad > Configuración > Diarios*.

### Permisos y grupos

El módulo respeta los grupos de permisos estándar de Odoo:

| Grupo | Capacidades |
|---|---|
| **Vehicle Rental / User** | Operativa básica: contratos, devoluciones, consultas. |
| **Vehicle Rental / Manager** | Además: configurar tarifas, políticas, términos, reglas de depósito, categorías. |
| **Portal** | Cliente externo: ve sólo sus facturas y consultas. |

Configuración desde **Ajustes → Usuarios y compañías → Usuarios** → pestaña *Permisos de acceso*.

Reglas más detalladas en `security/ir.model.access.csv` (no se modifica habitualmente).

### Pasarela Redsys

**Ajustes → Pasarelas de Pago → Redsys**

Campos típicos:

- Merchant Code.
- Terminal.
- Clave secreta.
- Entorno: Test / Producción.
- URLs de retorno.

Ver [09 · Pagos y Redsys](./09-pagos-y-redsys.md) para el detalle del flujo.

### Servidor de correo

**Ajustes → Servidores de correo**

Necesario para que los emails se envíen al cliente. Si está mal configurado, los emails se quedan en cola.

---

## F. Categorías excluidas del portal web

Por defecto, en el portal web (`/web/booking-enquiry`) se **excluyen** las siguientes categorías:

- *Furgoneta*
- *Tipo E - Bici Eléctrica*
- *Tipo E*
- *Bici Eléctrica*

Si quieres incluir o excluir más categorías, se modifica en el código del controller `pinveco_home_controller.py` / `sunset_home_controller.py` — esta operación requiere intervención técnica.

---

## G. Multi-compañía: Sunset vs Pinveco

El módulo está pensado para operar **dos marcas simultáneamente**:

- **Sunset Rent a Car** — Compañía Odoo "Sunset".
- **Pinveco** — Compañía Odoo "Pinveco".

Cada compañía:

- Tiene su propia configuración contable (diarios, impuestos, banco).
- Tiene sus propias tarifas, seguros, depósitos, políticas.
- Tiene sus propios vehículos en la flota.
- Tiene sus propios contratos.

El usuario puede cambiar de compañía activa desde el **selector superior derecho** de Odoo.

### Cómo dar de alta una segunda compañía

**Ajustes → Compañías → Crear**

1. Nombre, dirección, NIF.
2. Logo.
3. Configurar contabilidad.
4. Configurar pasarela de pagos (si aplica).
5. Asignar usuarios a esa compañía.

---

## H. Datos iniciales recomendados

Cuando se instala el módulo por primera vez, recomendado configurar **en este orden**:

1. **Compañías** (si hay multi-marca: Sunset y Pinveco).
2. **Diarios contables** de ventas para cada compañía.
3. **Productos especiales** (verificar que existen los 5 productos listados arriba).
4. **Categorías de vehículo** (Tipo A, Tipo C, etc.).
5. **Modelos de vehículo**.
6. **Vehículos** (la flota).
7. **Horarios de mantenimiento** y asignarlos a los vehículos.
8. **Tarifas de vehículos** (precios por categoría/temporada).
9. **Tarifas de seguros** (Básico/Sin Franquicia × duración × conductor).
10. **Reglas de depósito** (por categoría y tipo de tarjeta).
11. **Políticas de cancelación**.
12. **Términos de acuerdo de alquiler**.
13. **Pasarela Redsys** (si se va a cobrar online).
14. **Servidor de correo**.
15. **Plantillas de email** (verificar que están cargadas y traducidas).
16. **Usuarios y permisos**.

Una vez configurado todo lo anterior, **ya se pueden crear los primeros contratos**.

---

## I. Multi-idioma

El módulo incluye traducciones en:

- **Español (es)** — traducción principal.
- **Francés (fr)** — completa.
- **Italiano (it)** — completa.
- **Árabe (ar_001)** — completa.

### Cómo activar otro idioma

1. **Ajustes → Traducciones → Idiomas → Activar idioma**.
2. Recargar la página.
3. El idioma del usuario se cambia desde **Preferencias → Idioma**.

---

## Relacionado

- Todos los demás capítulos: la configuración alimenta todo el sistema.
- [08 · Tarifas, seguros y depósitos](./08-tarifas-seguros-depositos.md) — el núcleo de la configuración funcional.

---

[← Volver al índice](./README.md) · Anterior: [14 · Emails y reportes](./14-emails-y-reportes.md) · Siguiente: [16 · Solución de problemas →](./16-solucion-de-problemas.md)
