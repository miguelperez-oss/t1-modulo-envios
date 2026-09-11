# PRD · Retorno sobre la misma guía madre (T1envíos · Mis envíos)

| | |
|---|---|
| **Producto** | T1envíos · módulo Mis envíos |
| **Autor** | UX (ux@t1pagos.com) |
| **Fecha** | 11 de septiembre 2026 |
| **Estado** | Listo para definición técnica (tech lead: Juan Carlos) |
| **Prototipos** | [Propuesta A · control en chip](https://t1-modulo-envios-miguelperez-oss-projects.vercel.app/propuesta-a) · [Propuesta B · control en texto](https://t1-modulo-envios-miguelperez-oss-projects.vercel.app/propuesta-b) |
| **Diseño** | Figma `T1-Envíos-2026` — listado `17014-81933`, detalle retorno `17008-234054`, detalle origen `17014-82962` |
| **Sistema de diseño** | NEXUS V2.0 (contexto dashboard) |

> **Cómo leer este documento.** Las secciones 1–8 son producto: definen qué debe ver y
> entender el vendedor. La **§9 es el espacio de la definición técnica y la completa el
> tech lead** (este PRD solo declara lo que el front necesita recibir, no cómo se resuelve).
> La **§10** lista lo que sigue abierto del lado de producto/operación.

---

## 1. Contexto de negocio

Cuando una entrega no se concreta, la paquetería regresa el paquete al remitente y **cobra
por ese retorno**. Antes ese retorno era invisible en la plataforma: la guía de retorno
llegaba **por correo electrónico al equipo de Ops**, después de conciliar con la
paquetería, y el seguimiento se hacía a mano. El vendedor no tenía cómo verlo en T1envíos.

Hoy los retornos ya viven en **Mis envíos**, y al exponerlos quedaron a la vista dos
comportamientos distintos según la paquetería:

| Caso | Comportamiento de la paquetería | Paqueterías | Estado |
|---|---|---|---|
| **A · Guía independiente** | Genera una **guía de retorno con número propio**, distinta de la guía madre | DHL · FedEx · 99 minutos | **Ya en producción.** Lógica de cobro definida por Juan Carlos. |
| **B · Misma guía madre** | **No genera guía nueva**: el retorno es un **estado adicional de la misma guía madre**, con el mismo número | UPS · Paquete Express · T1envíos · T1 Economic · AMPM | **Este requerimiento** |

### El problema del caso B

1. **Trazabilidad.** Como el número de guía es el mismo, la plataforma no distingue el
   tramo de ida del tramo de retorno. El vendedor no sabe que su paquete va de regreso,
   ni en qué estado va ese regreso.
2. **Transparencia de cobro.** El retorno genera un **segundo cobro** sobre la misma guía.
   Hoy se mezcla con el cobro original: el vendedor ve un cargo que no reconoce, no puede
   asociarlo al tramo que lo originó, y la aclaración la absorbe soporte de T1.

### Por qué importa

Cada retorno mal explicado se vuelve un ticket de aclaración y desconfianza sobre el
cobro. Resolverlo baja carga de soporte y cierra en plataforma el trabajo manual que Ops
hacía por correo. **Volumen y costo actual: pendiente de data — §10.**

---

## 2. Objetivo

Que el vendedor entienda, sin salir de Mis envíos, **que su envío fue retornado, en qué
estado va ese retorno y cuánto le costó**, cuando la paquetería retorna **sobre la misma
guía madre**.

### Métricas

- **Primaria:** % de tickets/aclaraciones por cobros de retorno no reconocidos (bajar).
- Secundarias: tiempo para llegar de un retorno a su guía madre; % de retornos consultados
  en las primeras 24 h; uso del filtro "Tipo de guía"; CSAT del módulo.
- **Baseline: pendiente — §10.**

---

## 3. Alcance

### Dentro

- **Listado Mis envíos**: identificar la guía madre retornada y desplegar su tramo de
  retorno (mismo número) con estado, fecha y costo propios.
- **Detalle de la guía**: una sola card que declare que el retorno viaja en la misma guía,
  cronograma con el hito de inicio del retorno, y resumen de cobro con los dos cobros
  separados.
- Las 5 paqueterías del caso B: UPS, Paquete Express, T1envíos, T1 Economic, AMPM.

### Fuera

- **Caso A (guía independiente).** Ya está en producción; aquí solo sirve de referencia
  para mantener el mismo lenguaje visual. Si al implementar el caso B se detectan
  inconsistencias con lo ya liberado, se levantan como ajuste aparte.
- **Lógica de cobro del retorno.** Ya definida por Juan Carlos para las paqueterías en
  producción; este requerimiento **la reutiliza** y solo pide exponerla en la UI del caso
  B (ver §6.3).
- Disputa o aclaración del cobro en línea; reprogramar entrega para evitar el retorno;
  agrupación de retornos por lote; vista móvil (los prototipos son desktop — la tabla pasa
  a cards según Nexus).

---

## 4. Actores

| Actor | Necesidad |
|---|---|
| **Vendedor / merchant** | Ver que el envío fue retornado, seguir el regreso y entender el cobro extra. |
| **Ops** | Dejar de conciliar y reenviar guías por correo; que la plataforma sea la fuente única. |
| **Soporte** | Explicar el cargo con la pantalla, sin pedir data a la paquetería. |

---

## 5. Modelo conceptual del caso B

Una sola guía, **dos tramos**:

| Concepto | Qué es |
|---|---|
| **Guía madre** | El envío que salió al cliente final. Conserva su número. |
| **Tramo de retorno** | El regreso del paquete al remitente. **No es una guía nueva:** es un estado adicional de la guía madre, con sus propios eventos de rastreo, su fecha estimada de entrega y **su propio cobro**. |

> **Regla de oro de diseño:** *ninguna guía huérfana*. El retorno nunca se muestra sin su
> guía madre visible, y una guía madre retornada nunca se muestra sin acceso a su retorno.
> En el caso B esto se resuelve **sin duplicar el número de guía**.

---

## 6. Requerimiento funcional

### 6.1 Listado — identificar y desplegar el retorno

| # | Requerimiento |
|---|---|
| **L-1** | La guía madre retornada muestra en **Estado de rastreo** el chip **"Retornado"** (gris neutro; el verde queda reservado a "Entregado"). |
| **L-2** | La guía **no se duplica**: sigue siendo una sola fila. Junto al número aparece el ícono ⟳ con tooltip **"Retorno sobre misma guía"**. |
| **L-3** | La fila incluye el control **"Ver retorno" / "Ver menos"** con flecha, que despliega el tramo de retorno debajo de la guía madre. Estado abierto/cerrado independiente por fila. |
| **L-4** | El tramo desplegado muestra: **el mismo número de guía**, su **propio estado de rastreo**, su fecha/hora y **su propio costo**. No repite el nombre de la paquetería ni lleva menú de acciones. |
| **L-5** | El menú **⋯** vive solo en la fila de la guía madre: *Descargar guía · Reportar incidencia · Rastrear guía · Ver detalle*. |
| **L-6** | El filtro **"Tipo de guía"** (*Todas · Envío · Retorno*) debe considerar el tramo de retorno del caso B: al filtrar "Retorno" la guía se muestra con su tramo visible, no oculto. La etiqueta del filtro cambia al valor aplicado. |
| **L-7** | La columna **Estado** (Activa / Cancelada) es el ciclo de vida de la guía y es independiente del **Estado de rastreo**. |

### 6.2 Detalle de la guía

| # | Requerimiento |
|---|---|
| **D-1** | **Una sola card**, titulada **"Guía de envío / retorno"**: no se duplican número, paquetería ni servicio. |
| **D-2** | La card declara explícitamente el caso con la leyenda **"Guía retorno: {mismo número}"**. Sin link (apuntaría a sí misma). |
| **D-3** | A la derecha de la card: **"Fecha de entrega sobre el retorno"** + fecha estimada del tramo de retorno. |
| **D-4** | **Un solo cronograma** para los dos tramos, en orden descendente (lo más reciente arriba). |
| **D-5** | El cronograma incluye el hito **"Retorno iniciado en la misma guía"**, que marca el momento exacto en que comenzó el retorno, con su monto y hora. Es el elemento que traduce "es la misma guía, pero ya va de regreso". |
| **D-6** | Los eventos del tramo de retorno y del tramo de ida conviven en ese cronograma y deben ser distinguibles entre sí — **ver §9.2**. |

### 6.3 Resumen de cobro

> La **lógica** del cobro del retorno es la que ya definió Juan Carlos para las paqueterías
> en producción. Lo que este requerimiento pide es **exponerla** para el caso de misma guía.

| # | Requerimiento |
|---|---|
| **C-1** | El resumen muestra **los dos cobros por separado**, nunca sumados sin desglose. |
| **C-2** | Cada cobro lista **Subtotal · Cargos adicionales · Impuestos (IVA) · Total**. |
| **C-3** | Los bloques se rotulan **"Guía original"** y **"Cobro por retorno"**, y el card cierra con **"Total del envío"**. Ambos bloques corresponden al mismo número de guía. |
| **C-4** | Cuando el cobro del retorno aún no se ha aplicado se muestra **"Pendiente"** en lugar del monto. |

### 6.4 Notificación *(propuesta, decisión de producto pendiente — §10)*

Al iniciarse el retorno, avisar al vendedor por WhatsApp: qué envío no se entregó, que el
paquete va de regreso **con el mismo número de guía**, el monto del cobro y el saldo
restante, con CTA al detalle.

---

## 7. Copy canónico

La estructura es intocable; las palabras son ajustables por UX.

| Elemento | Copy |
|---|---|
| Chip de la guía madre retornada | `Retornado` |
| Tooltip del ícono ⟳ | `Retorno sobre misma guía` |
| Control del desplegable | `Ver retorno` / `Ver menos` |
| Título de la card en el detalle | `Guía de envío / retorno` |
| Leyenda de la card | `Guía retorno: {número}` |
| Fecha a la derecha | `Fecha de entrega sobre el retorno` |
| Hito del cronograma | `Retorno iniciado en la misma guía` |
| Bloques de cobro | `Guía original` · `Cobro por retorno` · `Total del envío` |
| Cobro sin aplicar | `Pendiente` |
| **Prohibido** | prometer hora exacta de entrega; "garantizamos"; nombrar el retorno como error; mostrar códigos internos de la paquetería |

---

## 8. Casos borde

| Caso | Comportamiento esperado |
|---|---|
| Retorno sin fecha estimada de la paquetería | Ocultar el bloque de fecha; no inventar una estimación. |
| Cobro del retorno aún no aplicado | Mostrar `Pendiente`; el total del envío considera solo lo cobrado. |
| La paquetería no reporta eventos del tramo de retorno | Mostrar el hito de inicio y el estado vigente, sin inventar eventos intermedios. |
| Paquetería sin comportamiento clasificado | Tratarla como caso A (guía independiente, ya en producción) y alertar a Ops. |
| Más de un retorno sobre la misma guía madre | **Pendiente de definir — §10.** |
| Guía cancelada que retorna | **Pendiente de confirmar si ocurre — §10.** |

---

## 9. Definición técnica · **a completar por el tech lead**

Este PRD no propone implementación. Solo declara **lo que el front necesita poder leer**
para cumplir la §6; el diseño de la solución (modelo, endpoints, cálculo) lo define el
tech lead y de ahí baja a front y back.

### 9.1 Lo que el front necesita del listado

Por cada guía madre retornada del caso B, un único registro que permita pintar la fila y
su tramo desplegable:

- que la guía **fue retornada** (para el chip y el ícono);
- que la paquetería opera en modo **misma guía** (para decidir desplegable en vez de fila
  aparte);
- del tramo de retorno: **estado de rastreo, fecha/hora de inicio, costo** y si el cobro
  ya se aplicó.

> Importante: en este caso **no debe llegar un segundo registro de guía** en el listado,
> para no duplicar el número.

### 9.2 Lo que el front necesita del detalle

- **Distinguir a qué tramo pertenece cada evento** del cronograma (ida vs. retorno) y el
  momento de inicio del retorno. *Es la dependencia crítica del requerimiento D-6: si hoy
  no existe esa distinción, hay que definir cómo se obtiene o se infiere y quién la calcula.*
- **Fecha estimada de entrega del tramo de retorno.**
- **Los dos cobros por separado**, cada uno con subtotal, cargos adicionales, IMPUESTOS y
  total —reutilizando la lógica ya definida para las paqueterías en producción— más el
  estado del cobro del retorno (aplicado / pendiente).

### 9.3 Clasificación de paqueterías

El front necesita saber, por paquetería, si el retorno es **guía independiente** o **misma
guía**, sin tenerlo escrito en el código. Dónde vive esa clasificación y quién la
mantiene lo define el tech lead junto con Ops.

Paqueterías del caso B a considerar: **UPS · Paquete Express · T1envíos · T1 Economic ·
AMPM** — confirmar que la lista está completa.

### 9.4 A completar por el tech lead

- [ ] Modelo de datos y endpoints (listado y detalle) para el caso B
- [ ] Cómo se distingue el tramo de cada evento de rastreo
- [ ] Reutilización de la lógica de cobro ya definida y cómo se expone separada en la UI
- [ ] Dónde vive la clasificación de paqueterías por modo de retorno
- [ ] Impacto en *Exportar* del listado (¿incluye tramo de retorno y segundo cobro?)
- [ ] Estimación y partición de tickets para front y back

---

## 10. Abierto del lado de producto / operación

1. **Cuál propuesta se implementa:** control del desplegable en **chip** (Propuesta A) o
   en **texto con flecha** (Propuesta B). Decisión de UX/Owner.
2. **Notificación al vendedor** (§6.4): ¿entra en v1? ¿WhatsApp, correo o campana?
3. **¿Se retira el correo a Ops** una vez que el flujo cierra en plataforma, o se mantiene
   como respaldo de conciliación?
4. **Multiplicidad:** ¿puede haber más de un retorno sobre la misma guía madre? ¿Puede
   retornar una guía cancelada?
5. **Fecha estimada del retorno:** ¿la entrega la paquetería o hay que calcularla?
6. **Permisos:** ¿todos los roles ven el cobro del retorno? ¿Ops necesita una vista
   distinta a la del vendedor?
7. **Rastreo:** en misma guía, ¿el tracking de la paquetería muestra los dos tramos con el
   mismo número, o hay que advertir algo al vendedor?
8. **Data para métricas:** retornos por mes, distribución por paquetería y tickets de
   aclaración, para fijar baseline y meta.
9. **Móvil:** ¿entra en este alcance o se atiende después?

---

## 11. Entregables de diseño

- Prototipos navegables con datos de ejemplo — **Propuesta A** (chip) y **Propuesta B**
  (texto); incluyen los 5 casos de misma guía y, como referencia, los 3 ya en producción.
- Spec funcional: `spec-guias-retorno.md` (estados, copy, notificaciones, casos borde).
- Guion de demo: `guion-video-retornos.md`.
- Repo: <https://github.com/miguelperez-oss/t1-modulo-envios>

## 12. Changelog

- **2026-09-11 · v1** — Primera versión.
- **2026-09-11 · v1.1** — Alcance acotado al retorno sobre la misma guía madre; el caso de
  guía independiente pasa a contexto/referencia. La lógica de cobro se declara como
  reutilización de la ya definida por el tech lead. La sección técnica se convierte en
  espacio a completar por el tech lead, con solo las necesidades del front.
