# 01 · Introducción

[← Volver al índice](./README.md)

---

## ¿Qué es el módulo Sunset / Vehicle Rental?

Es una aplicación de gestión integral de alquiler de vehículos integrada en Odoo 19 Enterprise. Permite a una empresa de rent-a-car gestionar **todo el ciclo del negocio en un solo sitio**:

- Mantener una **flota de vehículos** con sus precios, fotos, kilometraje y estado.
- Recibir **consultas y reservas** desde la página web pública o registrarlas manualmente.
- Generar **contratos de alquiler** con cálculo automático de tarifas, seguros y depósito.
- **Cobrar online** al cliente mediante Redsys (TPV virtual).
- Controlar el **mantenimiento** de los vehículos, **daños** que se producen y **sustituciones** durante un alquiler activo.
- Registrar el **estado físico** de cada coche con un *painter* visual de rayones y daños.
- Emitir **facturas** automáticas, anexos PDF de ampliación y sustitución, y enviar **emails** automáticos al cliente.
- Visualizar el negocio en un **dashboard** con métricas en tiempo real y un **cronograma Gantt** de todos los contratos.

El módulo es una **aplicación** completa de Odoo: aparece como un icono propio en el menú principal y reemplaza/extiende los módulos estándar de Flota, Ventas, Facturación y CRM con la lógica específica del alquiler.

---

## ¿Para quién es esta guía?

Para cualquier persona que vaya a operar el sistema desde su cuenta de Odoo:

- **Recepción / oficina**: dar de alta clientes, contratar, devolver vehículos, cobrar.
- **Gerencia**: ver el panel, métricas, facturación.
- **Taller / mantenimiento**: registrar mantenimientos, daños, rayones.
- **Administración**: facturas, pagos, conciliaciones.

No hace falta saber programar ni entender la estructura interna de Odoo.

---

## Recorrido por el menú principal

Al entrar en Odoo verás un icono **Vehicles Rental** en el menú lateral o superior. Al hacer clic se despliega la siguiente estructura:

```
Vehicles Rental
├── Panel                          → Dashboard con métricas y gráficos
├── Consultas de Reserva           → Leads CRM entrantes desde la web
├── Disponibilidad                 → Vista de disponibilidad por fechas
├── Reservar Vehículo              → Asistente para crear una reserva
├── Reserva Múltiple               → Reservar varios vehículos al mismo cliente
├── Vehículos                      → La flota completa
├── Contratos                      → Todos los contratos de alquiler
├── Contratos Grupo                → Agrupaciones de contratos del mismo cliente
├── Ampliaciones                   → Ampliaciones de contratos activos
├── Clientes                       → Listado de contactos
├── Solicitudes de mantenimiento   → Órdenes de mantenimiento
├── Sustituciones de Vehículos     → Cambios de vehículo durante un alquiler
├── Tarifas de Vehículos           → Precios por vehículo / temporada
├── Tarifas de Seguros             → Pólizas y precios de seguro
├── Reglas de Depósitos            → Cálculo dinámico del depósito
└── Configuraciones
    ├── Políticas de Cancelación
    ├── Términos de Acuerdo de Alquiler
    ├── Informes de Rayones
    └── Horarios de Mantenimiento
```

Cada entrada del menú está documentada en su archivo correspondiente de esta guía (ver el [índice](./README.md)).

---

## Flujos de trabajo típicos

### Flujo 1 — Reserva manual de un cliente que llega presencial

1. **Disponibilidad** → comprobar que el coche está libre las fechas pedidas.
2. **Reservar Vehículo** → asistente paso a paso que crea contrato + cliente.
3. **Contrato** → confirmar, imprimir/enviar PDF, firmar (digital o en papel).
4. *(Opcional)* Enviar **enlace de pago Redsys** al cliente.
5. Cuando vuelve el coche → botón **Devolver** → registrar daños y cargos.
6. **Devolución de depósito** → wizard de cierre.

### Flujo 2 — Reserva entrante por la web

1. El cliente rellena el formulario en la web pública → se crea una **Consulta de Reserva** (lead CRM).
2. Recepción la abre, contacta al cliente y confirma datos.
3. Botón **Convertir en contrato** → wizard que crea el contrato.
4. Continúa igual que el flujo 1 desde el paso 3.

### Flujo 3 — Reserva con varios vehículos a la vez

1. **Reserva Múltiple** → añadir varios vehículos al mismo cliente.
2. Se generan los contratos enlazados y, opcionalmente, un **Contrato Grupo** que los agrupa para facturación conjunta.

### Flujo 4 — Sustitución durante un alquiler

1. El cliente reporta avería o el taller necesita el vehículo.
2. Desde el contrato activo → botón **Sustituir vehículo** → wizard.
3. Se genera un **anexo PDF de sustitución** que firma el cliente.
4. El contrato sigue activo con el nuevo vehículo.

---

## Glosario

| Término | Significado |
|---------|-------------|
| **Lead / Consulta de reserva** | Solicitud de información que llega normalmente desde la web. No es un contrato, es solo interés. Vive en el CRM. |
| **Contrato de alquiler** | Documento operativo y legal del alquiler. Tiene un estado (borrador, en progreso, devuelto, cancelado) y líneas de facturación. |
| **Contrato grupo** | Conjunto de contratos del mismo cliente agrupados, normalmente para facturarlos en bloque. |
| **Ampliación (extension)** | Prórroga de un contrato activo. Cambia la fecha de fin y genera un anexo PDF. |
| **Sustitución** | Cambio de vehículo durante un alquiler activo, sin cerrar el contrato. Genera un anexo PDF. |
| **Wizard / Asistente** | Ventana emergente que recoge datos para realizar una acción concreta. |
| **Painter de daños** | Herramienta visual para señalar dónde hay un rayón o daño sobre el dibujo del vehículo. |
| **Informe de rayones (scratch report)** | Registro histórico permanente de los daños conocidos de un vehículo. |
| **Depósito** | Importe en garantía que el cliente paga al inicio y se devuelve al final si todo está en orden. |
| **Depósito dinámico** | Depósito que se calcula automáticamente según reglas (categoría de vehículo, edad del conductor, duración…). |
| **Redsys** | Pasarela de pago bancaria española integrada en Odoo para cobros con tarjeta online. |
| **Categoría de vehículo** | Agrupación (económico, compacto, SUV, etc.). Las tarifas, seguros y depósitos se configuran por categoría. |
| **Tarifa (pricing rule)** | Regla que define el precio del alquiler según vehículo, fechas y duración. |
| **Política de cancelación** | Condiciones que se aplican si el cliente cancela el contrato. |
| **Términos de acuerdo** | Cláusulas legales que aparecen en el contrato impreso y/o en la web. |

---

## Permisos y usuarios

El módulo respeta los grupos de permisos estándar de Odoo. En general:

- **Usuario interno (operador de recepción)**: puede crear contratos, devolver, cobrar, gestionar consultas.
- **Manager**: además puede configurar tarifas, políticas, términos, reglas de depósito.
- **Cliente final (portal)**: ve sólo sus propias reservas/contratos desde el portal web público.

Los permisos detallados están en `security/ir.model.access.csv` y se aplican automáticamente al instalar el módulo.

---

## Sobre esta guía

Cada capítulo de la guía es independiente y arranca con un enlace al índice. Dentro de cada capítulo encontrarás:

1. **Qué es** y para qué sirve.
2. **Cómo acceder** (ruta del menú).
3. **Paso a paso** con campos y botones.
4. **Ejemplos reales** de uso.
5. **Validaciones / errores frecuentes**.
6. **Enlaces a capítulos relacionados**.

---

[← Volver al índice](./README.md) · Siguiente: [02 · Panel y disponibilidad →](./02-panel-y-disponibilidad.md)
