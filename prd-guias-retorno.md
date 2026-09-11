# PRD · Guías de retorno en Mis envíos (T1envíos)

| | |
|---|---|
| **Producto** | T1envíos · módulo Mis envíos |
| **Autor** | UX (ux@t1pagos.com) |
| **Fecha** | 11 de septiembre 2026 |
| **Estado** | Listo para revisión técnica |
| **Prototipos** | [Propuesta A · chip](https://t1-modulo-envios-miguelperez-oss-projects.vercel.app/propuesta-a) · [Propuesta B · texto](https://t1-modulo-envios-miguelperez-oss-projects.vercel.app/propuesta-b) |
| **Diseño** | Figma `T1-Envíos-2026` — listado `17014-81933`, detalle retorno `17008-234054`, detalle origen `17014-82962` |
| **Sistema de diseño** | NEXUS V2.0 (contexto dashboard) |

> **Cómo leer este documento.** Las secciones 1–6 son producto y no se negocian sin UX.
> La 7 es una **propuesta** de contrato de datos que el tech lead debe validar o corregir
> contra el backend real. La 10 lista lo que **falta por confirmar** antes de estimar:
> sin esas respuestas el equipo puede construir sobre supuestos equivocados.

---

## 1. Contexto de negocio

Cuando una entrega no se concreta, la paquetería regresa el paquete al remitente y **cobra
por ese retorno**. Hasta hace poco ese retorno era invisible en la plataforma: la guía de
retorno llegaba **por correo electrónico al equipo de Ops**, después de conciliar con la
paquetería, y el seguimiento se hacía a mano. El vendedor no tenía forma de verlo en T1envíos.

Hoy los retornos ya viven en **Mis envíos**, y al exponerlos aparecieron dos
comportamientos distintos según la paquetería:

| Caso | Comportamiento de la paquetería | Paqueterías | Estado |
|---|---|---|---|
| **A** | Genera una **guía de retorno independiente**, con número propio | DHL · FedEx · 99 minutos | **Ya en producción** |
| **B** | **No genera guía nueva**: el retorno es un **estado adicional de la misma guía**, con el mismo número | UPS · Paquete Express · T1envíos · T1 Economic · AMPM | **Alcance de este requerimiento** |

### El problema

1. **Trazabilidad.** En el caso B la plataforma no distingue el tramo de ida del tramo de
   retorno: es el mismo número de guía, así que el vendedor no sabe que su paquete va de
   regreso ni en qué estado va ese regreso.
2. **Transparencia de cobro.** El retorno genera un **segundo cobro** que hoy se mezcla
   con el cobro original. El vendedor ve un cargo que no reconoce, no puede asociarlo al
   envío que lo originó, y la aclaración la absorbe soporte de T1 — no la paquetería.

### Por qué importa

Cada retorno mal explicado se convierte en un ticket de aclaración y en desconfianza sobre
el cobro. Resolverlo baja carga de soporte y elimina el trabajo manual que Ops hacía por
correo. **Volumen y costo actual: pendiente de data — ver §10.**

---

## 2. Objetivo

Que el vendedor entienda, sin salir de Mis envíos, **que su envío fue retornado, en qué
estado va ese retorno y cuánto le costó**, tanto si la paquetería generó una guía nueva
como si el retorno viaja en la misma guía.

### Métricas

- **Primaria:** % de tickets/aclaraciones por cobros de retorno no reconocidos (bajar).
- Secundarias: tiempo para llegar de un retorno a su guía origen; % de retornos
  consultados en las primeras 24 h; uso del filtro "Tipo de guía"; CSAT del módulo.
- **Baseline: pendiente — ver §10.**

---

## 3. Alcance

### Dentro

- Listado **Mis envíos**: identificación del retorno, desplegable del tramo de retorno,
  filtro por tipo de guía.
- **Detalle de guía**: card de guía, referencias cruzadas, cronograma con el hito del
  retorno, y resumen de cobro con los cobros separados.
- Ambos casos (A y B), con el mismo lenguaje visual.

### Fuera (v1)

- Disputa o aclaración del cobro de retorno en línea.
- Reprogramar entrega para evitar el retorno (backend no lo soporta hoy).
- Agrupación de retornos por lote o recolección.
- Vista móvil del módulo (los prototipos son desktop; la tabla pasa a cards según Nexus).

---

## 4. Actores

| Actor | Necesidad |
|---|---|
| **Vendedor / merchant** | Ver que el envío fue retornado, seguir el regreso y entender el cobro extra. |
| **Ops** | Dejar de conciliar y reenviar guías por correo; que la plataforma sea la fuente única. |
| **Soporte** | Poder explicar el cargo con la pantalla, sin pedir data a la paquetería. |

---

## 5. Modelo conceptual

Tres entidades que el front necesita distinguir:

1. **Guía original** — el envío que salió al cliente final.
2. **Tramo de retorno** — el regreso del paquete al remitente. Puede materializarse como:
   - **guía independiente** (caso A): número propio, estados propios, cobro propio;
   - **estado adicional de la misma guía** (caso B): mismo número, estados de retorno y
     **cobro propio**.
3. **Cobro** — cada tramo tiene su propio cobro con desglose: subtotal, cargos
   adicionales e IVA. El retorno **se descuenta automáticamente del saldo T1envíos**
   *(decisión de Dirección)*.

> **Regla de oro de diseño:** *ninguna guía huérfana*. Un retorno nunca se muestra sin su
> origen visible, y un origen retornado nunca se muestra sin acceso a su retorno.

---

## 6. Requerimiento funcional

### 6.1 Listado — identificación del retorno

| # | Requerimiento |
|---|---|
| L-1 | La guía que fue retornada muestra en **Estado de rastreo** el chip **"Retornado"** (gris neutro; el verde está reservado a "Entregado"). |
| L-2 | **Caso A:** la guía de retorno aparece como **fila independiente**, con el ícono ⟳ junto a su número y tooltip **"Guía de retorno"**. Se ordena por su propia fecha. |
| L-3 | **Caso B:** la guía **no se duplica**. La fila de la guía original muestra el ícono ⟳ junto al número con tooltip **"Retorno sobre misma guía"**, y un control **"Ver retorno"** que despliega el tramo de retorno debajo. |
| L-4 | El tramo desplegado (caso B) muestra: mismo número de guía, **su propio estado de rastreo**, su fecha/hora y **su propio costo**. No repite paquetería ni lleva menú de acciones. |
| L-5 | El desplegable es por fila, con estado abierto/cerrado independiente. Etiqueta: **"Ver retorno"** / **"Ver menos"** con flecha. |
| L-6 | El filtro **"Tipo de guía"** ofrece *Todas · Envío · Retorno*. Al aplicar "Retorno", la etiqueta del filtro cambia al valor aplicado. En caso B debe mostrar la guía con su tramo de retorno visible (no ocultarlo). |
| L-7 | Menú **⋯** por fila: *Descargar guía · Reportar incidencia · Rastrear guía · Ver detalle*. En caso B el menú vive solo en la fila de la guía original. |
| L-8 | La columna **Estado** (Activa / Cancelada) es el ciclo de vida de la guía y es independiente del **Estado de rastreo**. |

### 6.2 Detalle — caso A (guía independiente)

| # | Requerimiento |
|---|---|
| D-1 | **Una guía por pantalla.** El detalle de la guía original muestra la card **"Guía original"**; el de la guía de retorno, la card **"Guía retorno"**. |
| D-2 | Cada card muestra: logo de paquetería, `{Paquetería}: {número}`, servicio, y la **referencia cruzada navegable** — "Guía retorno: {n}" en la original y "Guía original: {n}" en el retorno. El link lleva al detalle de la otra guía. |
| D-3 | A la derecha de la card: **"Fecha estimada de entrega"** + fecha. |
| D-4 | **Cronograma de la guía original:** solo sus eventos, cerrando con el hito "Guía creada (Original)". **Cronograma de la guía de retorno:** eventos del retorno **y a continuación los de la original**, con los hitos "Guía creada (Retorno)" y "Guía creada (Original)". Orden descendente (lo más reciente arriba). |
| D-5 | Cada hito muestra logo + número de guía, monto y hora, sobre banda gris. |

### 6.3 Detalle — caso B (misma guía) · **el corazón de este requerimiento**

| # | Requerimiento |
|---|---|
| E-1 | **Una sola card**, titulada **"Guía de envío / retorno"**: no se duplica número, paquetería ni servicio. |
| E-2 | La card incluye la leyenda **"Guía retorno: {mismo número}"**, que deja explícito que el retorno viaja con esa guía. Sin link (apuntaría a sí misma). |
| E-3 | A la derecha: **"Fecha de entrega sobre el retorno"** + fecha estimada del tramo de retorno. |
| E-4 | El cronograma es **uno solo**, con el hito **"Retorno iniciado en la misma guía"** marcando el momento exacto en que el retorno comenzó, con su monto y hora. |
| E-5 | Los eventos de retorno y de ida conviven en el mismo cronograma, en orden descendente. **Requiere que el backend permita distinguir a qué tramo pertenece cada evento — ver §10.3.** |

### 6.4 Resumen de cobro (ambos casos)

| # | Requerimiento |
|---|---|
| C-1 | El resumen muestra **los cobros por separado**, nunca sumados sin desglose. |
| C-2 | Cada cobro lista: **Subtotal · Cargos adicionales · Impuestos (IVA) · Total**. |
| C-3 | **Caso B:** el card muestra los dos bloques — **"Guía original"** y **"Cobro por retorno"** — y cierra con **"Total del envío"**. |
| C-4 | **Caso A:** cada pantalla de guía muestra el cobro de esa guía. |
| C-5 | El cobro del retorno se **descuenta automáticamente del saldo T1envíos**; cuando aún no se ha aplicado se muestra **"Pendiente"** en lugar del monto. |

### 6.5 Notificación (propuesta, no confirmada — ver §10.8)

Al iniciarse el retorno, avisar al vendedor por WhatsApp (canal donde vive el merchant):
qué envío no se entregó, que el paquete va de regreso, el monto del cobro y el saldo
restante, con CTA al detalle. En caso B el mensaje debe decir que el retorno viaja con el
**mismo número de guía**.

---

## 7. Propuesta de contrato de datos *(a validar por el tech lead)*

No conozco el contrato real del backend; esto es lo que el front necesita, expresado como
propuesta. **Si el modelo actual ya resuelve esto con otra forma, manda el modelo actual.**

### 7.1 Catálogo de paqueterías

```json
{ "carrier_id": "ups", "name": "UPS", "return_mode": "same_guide" }
{ "carrier_id": "dhl", "name": "DHL", "return_mode": "new_guide" }
```

`return_mode` ∈ `new_guide` | `same_guide`. **Debe vivir en catálogo, no en el código
del front**, para que dar de alta una paquetería no requiera release (ver §10.4).

### 7.2 Guía en el listado

```json
{
  "guide_number": "1Z9990123456",
  "carrier_id": "ups",
  "service": "Terrestre / 3 días",
  "created_at": "2026-08-30T10:26:00-06:00",
  "sales_channel": "Mercado Libre",
  "customer": "Mauricio Beltrán",
  "cost": 269.52,
  "status": "active",                    // Activa | Cancelada
  "tracking_status": "returned",         // pinta el chip
  "return": {
    "mode": "same_guide",                // o "new_guide"
    "guide_number": "1Z9990123456",      // igual al original en same_guide
    "started_at": "2026-09-04T09:14:26-06:00",
    "tracking_status": "in_transit",
    "estimated_delivery": "2026-09-07",
    "cost": 241.10,
    "charge_status": "charged"           // charged | pending
  }
}
```

- En `new_guide` el retorno **también** llega como registro propio del listado, con
  `parent_guide_number` apuntando a la original (para pintar el ⟳ y la referencia cruzada).
- En `same_guide` **no** debe llegar un segundo registro: el front lo renderiza desde
  el objeto `return`.

### 7.3 Detalle — cronograma

```json
"timeline": [
  { "leg": "return",   "type": "milestone", "code": "return_started",
    "label": "Retorno iniciado en la misma guía", "amount": 241.10,
    "at": "2026-09-04T09:14:26-06:00" },
  { "leg": "outbound", "type": "event", "code": "returned_to_sender",
    "at": "2026-09-04T08:58:03-06:00" }
]
```

El campo **`leg`** (`outbound` | `return`) es el que hace posible el requerimiento E-5.
Si hoy no existe, es el cambio de backend más importante de este proyecto.

### 7.4 Cobros

```json
"charges": [
  { "leg": "outbound", "guide_number": "1Z9990123456", "charged_at": "2026-08-30",
    "subtotal": 207.84, "additional_charges": 24.50, "tax": 37.18, "total": 269.52 },
  { "leg": "return",   "guide_number": "1Z9990123456", "charged_at": "2026-09-04",
    "subtotal": 174.58, "additional_charges": 33.26, "tax": 33.26, "total": 241.10,
    "status": "charged" }
]
```

---

## 8. Copy canónico

La estructura es intocable; las palabras son ajustables por UX.

| Elemento | Copy |
|---|---|
| Chip de la guía retornada | `Retornado` |
| Tooltip ⟳ · caso A | `Guía de retorno` |
| Tooltip ⟳ · caso B | `Retorno sobre misma guía` |
| Control del desplegable | `Ver retorno` / `Ver menos` |
| Card detalle · caso B | Título `Guía de envío / retorno` · leyenda `Guía retorno: {número}` |
| Fecha derecha · caso B | `Fecha de entrega sobre el retorno` |
| Referencias caso A | `Guía retorno: {número}` · `Guía original: {número}` |
| Hito caso B | `Retorno iniciado en la misma guía` |
| Hitos caso A | `Guía creada (Original)` · `Guía creada (Retorno)` |
| Bloques de cobro | `Guía original` · `Cobro por retorno` · `Total del envío` |
| Cobro sin aplicar | `Pendiente` |
| **Prohibido** | prometer hora exacta de entrega; "garantizamos"; nombrar el retorno como error; mostrar códigos internos de la paquetería |

---

## 9. Casos borde

| Caso | Comportamiento esperado |
|---|---|
| Retorno sin fecha estimada de la paquetería | Ocultar el bloque de fecha; no calcular una estimación falsa. |
| Cobro de retorno aún no aplicado | Mostrar `Pendiente`; el total del envío considera solo lo cobrado. |
| Saldo insuficiente al momento del cobro | **Pendiente de definir con backend/finanzas — §10.6.** |
| Más de un retorno sobre la misma guía | **Pendiente de definir — §10.5.** |
| Paquetería nueva sin `return_mode` definido | Tratarla como `new_guide` y registrar alerta para Ops (propuesta). |
| La paquetería no reporta eventos del tramo de retorno | Mostrar el hito de inicio del retorno y el estado vigente, sin inventar eventos intermedios. |
| Guía cancelada que luego retorna | **Pendiente de confirmar si es posible — §10.5.** |

---

## 10. Lo que falta por confirmar *(bloquea estimación)*

Ordenado por impacto. Los primeros cuatro son los que pueden cambiar el diseño o el
alcance; el resto afina.

1. **Contrato real del backend para el caso B.** ¿Cómo expone hoy el retorno sobre la
   misma guía: un campo de estado, un objeto aparte, un `internal_family=returned`?
   Necesitamos el payload real del listado y del detalle.
2. **El cobro del retorno como dato.** ¿Llega como un registro de cobro independiente
   (con su fecha y desglose) o como un ajuste al cobro original? Sin cobro separable, el
   requerimiento C-1 no se puede cumplir.
3. **Trazabilidad por tramo en el cronograma.** ¿Existe forma de saber si un evento
   pertenece al tramo de ida o al de retorno (`leg`)? Si no existe, hay que definir cómo
   se infiere (¿a partir del evento "Devuelto al remitente"?) y quién lo calcula.
4. **Catálogo de `return_mode`.** ¿Dónde vive la relación paquetería → comportamiento?
   ¿Es configurable por Ops o requiere release? ¿Está confirmada la lista de las 5
   paqueterías del caso B y las 3 del caso A, o hay más por clasificar?
5. **Multiplicidad.** ¿Puede una guía tener más de un retorno? ¿Puede retornarse una guía
   de retorno? ¿Puede retornar una guía cancelada?
6. **Saldo insuficiente.** ¿Qué pasa si el cobro del retorno no se puede aplicar? ¿Queda
   pendiente, bloquea la guía, lo cubre Ops? ¿Existe ya un estado para eso?
7. **Fecha estimada del retorno.** ¿La entrega la paquetería o la calculamos? ¿Con qué
   dato?
8. **Notificaciones.** ¿Está en alcance avisar al vendedor? ¿Por WhatsApp, correo o
   campana? ¿Se mantiene el correo a Ops o se retira al cerrar el flujo en plataforma?
9. **Exportar.** El botón *Exportar* del listado: ¿debe incluir columnas del retorno y el
   segundo cobro? ¿Qué consume ese archivo hoy (conciliación de Ops, finanzas)?
10. **Permisos.** ¿Todos los roles ven el cobro del retorno? ¿Ops necesita una vista
    distinta a la del vendedor?
11. **Rastreo.** "Rastrear guía" en caso B: ¿el tracking de la paquetería muestra ambos
    tramos con el mismo número, o hay que advertir algo al vendedor?
12. **Data para las métricas.** Retornos por mes, distribución por paquetería, y tickets
    de aclaración por cobros no reconocidos, para fijar baseline y meta.
13. **Móvil.** ¿Entra en este alcance o se atiende después? El prototipo es desktop.

---

## 11. Entregables de diseño

- Prototipos navegables con los 8 casos y datos de ejemplo — **Propuesta A** (control en
  chip) y **Propuesta B** (control en texto); falta decidir cuál se implementa.
- Spec funcional: `spec-guias-retorno.md` (estados, copy, notificaciones, casos borde).
- Guion de demo: `guion-video-retornos.md`.
- Repo: <https://github.com/miguelperez-oss/t1-modulo-envios>

## 12. Changelog

- **2026-09-11 · v1** — Primera versión para revisión técnica.
