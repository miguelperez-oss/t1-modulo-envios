# Spec — Guías de retorno en Mis envíos (T1envíos)

> Acompaña a `propuesta-a.html` (hilo agrupado) y `propuesta-b.html` (guías vinculadas). Datos de ejemplo en ambos prototipos; los números reales se piden a quien los tenga.

## 1. Problema y principio de diseño

**Problema declarado:** mostrar la guía origen y la guía de retorno, los intentos de entrega y ambos cobros.

**Problema real (reencuadre):** hoy el retorno aparece como una guía más en el listado y el cobro consecuente llega sin contexto. El vendedor descubre un cargo que no reconoce, pierde la traza de qué envío lo originó y la incidencia/aclaración se la lleva T1, no la paquetería. Es una brecha de trazabilidad y de transparencia de cobro, no un problema de iconografía.

**Las 3 reglas que gobiernan el diseño:**

1. **Ninguna guía huérfana.** Toda guía de retorno se muestra siempre con referencia visible y navegable a su guía origen, y viceversa — en listado, detalle y cobro.
2. **El cobro se explica donde se ve.** Cada cargo aparece junto a la guía que lo generó, con fecha y motivo ("tras 3 intentos de entrega"). Nunca un total que mezcle cargos sin desglose por guía.
3. **El retorno informa, no alarma.** Se comunica como estado del proceso (chip verde con ⟳, tono informativo), no como error; y se pide sin prometer: fechas "estimadas", nunca hora exacta garantizada.

## 2. Lógica de estados

| Estado | Condición | Componente |
|---|---|---|
| En tránsito | Guía en movimiento | Chip azul (`blue-100`/`blue-700`) |
| Entregado | Carrier confirma entrega | Chip verde (`green-100`/`green-700`) |
| Intento de entrega | Carrier reporta intento fallido (n de N) | Chip amarillo + evento numerado en cronograma con evidencia |
| Retornado | Carrier generó guía de retorno para el envío | Chip verde con borde + icono ⟳ + tooltip enriquecido (título, subtítulo, carrier + guía vinculada) |
| Guía generada | Guía creada, sin recolección aún | Chip gris |
| Excepción | Incidencia del carrier | Chip naranja |
| Cancelado | Cancelada por el vendedor o el sistema | Chip rojo (`red-50`/`red-700`) |
| Devuelto | Paquete entregado de vuelta al remitente | Chip violeta |
| **Tipo Retorno** | `parent_guide_id != null` | Tag gris "Retorno" + icono ⟳ junto al número + referencia a la guía origen; filtrable con "Tipo de guía" |
| **Estatus de guía** (Activa / Cancelada) | Ciclo de vida de la guía, independiente del rastreo | Columna "Estado" en texto plano (alineada a DEV) + filtro "Estatus"; separada de "Estado de rastreo" (chips) |

**Momento de generación del retorno:** hito propio en el cronograma ("Guía de retorno generada — tras N intentos de entrega, HH:MM hrs") con costo y chip navegable a la nueva guía. Los intentos previos siempre visibles desde el detalle del retorno (cronograma unificado en A; sección "Historial de la guía original" en B).

## 3. Copy canónico por estado

La estructura es intocable; las palabras, ajustables.

| Elemento | Copy |
|---|---|
| Tag de tipo | "Retorno" |
| Tooltip icono ⟳ | "Guía de retorno" |
| Tooltip chip Retornado | Título "Guía de retorno" · subtítulo "Retornado — el paquete regresa al remitente" · [logo carrier] + número de guía |
| Referencia en fila origen | "Generó retorno · {guía}" |
| Referencia en fila retorno | "Retorno de · {guía}" |
| Hito de generación | "Guía de retorno generada — La paquetería generó una nueva guía tras {N} intentos de entrega sin éxito" |
| Intentos | "Intento de entrega ({n} de {N})" + motivo del carrier ("Sin quien reciba en el domicilio", "Domicilio cerrado") |
| Cobros | "Cobro 1 · Guía original / Cobro 2 · Guía de retorno" + "Cobrada el {fecha}" |
| Nota de cobro | "El cobro de la guía de retorno se generó al no concretarse la entrega y se descontó automáticamente de tu saldo T1envíos." (sin número de intentos — el conteo vive solo en el cronograma) |
| Prohibidos | hora exacta prometida, "garantizamos", etiquetas internas del modelo/carrier, tono de error para el retorno |

## 4. Comportamiento de interacciones

- **Propuesta A:** subfila de retorno anidada bajo la origen (codo conector + ⟳), colapsable con "1 guía de retorno ∧/∨"; detalle único por envío con cadena de guías y cronograma unificado.
- **Propuesta B:** filas hermanas unidas por conector vertical; links cruzados navegables en ambas filas; detalle por guía con stepper "Recorrido del envío" (nodo actual resaltado, el otro clickeable) e historial de la otra guía colapsable.
- **Filtro "Tipo de guía"** (Envío/Retorno): en A, al filtrar Retorno la subfila se muestra standalone con su contexto ("de la guía {origen}"); en B se ocultan los conectores.
- Menú ⋯ por fila: Ver detalle / Ver guía vinculada / Imprimir / Rastrear / Cancelar (destructivo).

## 5. Notificaciones y mensajería

| Canal | Timing | Copy |
|---|---|---|
| WhatsApp (merchant) | Al generarse la guía de retorno | "Tu envío {origen} no se pudo entregar después de {N} intentos. {Carrier} generó la guía de retorno {retorno} y el paquete va de regreso a tu dirección de origen. El retorno generó un cobro de {monto}, descontado de tu saldo T1envíos. Saldo restante: {saldo}. Revisa los intentos y el cobro en T1envíos → Mis envíos." + CTA "Rastrear retorno" |
| Campana dashboard | Mismo evento | Versión corta con link al detalle |
| WhatsApp (merchant) | Al entregarse el retorno | "Tu paquete del envío {origen} ya está de regreso en {dirección}." |

Mockeada en ambos prototipos (botón "Notificación WhatsApp" en la barra de demo), sincronizada al evento de generación del retorno.

## 6. Métricas

Conectadas al KPI de Dirección de reducción de incidencias/costo de soporte. **Números pendientes — pedir a soporte/data los volúmenes actuales; no se estiman aquí.**

- **Primaria:** % de tickets/aclaraciones por cobros de retorno no reconocidos (bajar).
- Secundarias: tiempo para llegar de una guía de retorno a su guía origen; % de retornos consultados en las primeras 24 h tras la notificación; uso del filtro "Tipo de guía"; CSAT del módulo de envíos.

## 7. Backend, edge cases y feedback loop

- **Cobro del retorno (decidido):** se descuenta automáticamente del saldo T1envíos; la notificación y el resumen de cobro lo dicen y muestran el saldo restante.
- **Retorno sin guía nueva (decidido):** cuando la paquetería retorna con el mismo número, basta el chip "Retornado" en la misma fila — sin fila ni subfila extra; el cronograma muestra ambos tramos en una sola guía. El detalle sustituye la cadena de 2 guías por "Guía del envío" + bloque "Retorno · misma guía {n}" con su cobro, y el resumen lista "Cobro inicial" y "Cobro por retorno · Misma guía" (mismo número de guía en ambos). Header: "1 guía · 2 cobros".
- **Retornos múltiples (N>1):** A escala a "N guías de retorno"; B apila conectores. El cobro lista Cobro 1…N.
- **Retorno de un retorno:** la cadena se extiende; el origen del hilo siempre es la primera guía.
- **Carrier no reporta intentos:** el hito de generación se muestra sin el conteo ("la paquetería generó una nueva guía"); cero precisión falsa.
- **Saldo insuficiente para el cobro del retorno:** mostrar "Cobro pendiente" en lugar del monto (supuesto por confirmar con backend).
- **Feedback loop de data:** motivo y hora de cada intento fallido por CP/carrier alimentan el score de direcciones y la estimación de entrega; diseñado desde v1 aunque se construya después.

## 8. Fuera de alcance v1

- Disputa/aclaración del cobro de retorno en línea.
- Agrupación de retornos por lote/recolección.
- Vista móvil del módulo (los prototipos son desktop; la tabla pasa a cards según Nexus).

## 9. Changelog

- **2026-08-27 · v1** — Dos propuestas iniciales (A hilo agrupado / B guías vinculadas) desde Figma T1-Envíos-2026 + Nexus V2.0.
- **2026-08-27 · v1.1** — Aplicado el kit Agente UX (barra de demo, notificación WhatsApp mockeada, `prefers-reduced-motion`, datos marcados como ejemplo) + este spec.
- **2026-08-27 · v1.2** — Decisiones de Dirección integradas: (1) el cobro del retorno se descuenta del saldo T1envíos → saldo restante en la notificación y nota en el resumen de cobro; (2) retorno sin guía nueva = chip "Retornado" en la misma fila, sin fila extra; (3) se retira la propuesta de reprogramación de entrega (sin soporte de backend por ahora).
- **2026-08-28 · v1.3** — Listado alineado al screen de DEV: columna "Estado" (Activa/Cancelada) separada de "Estado de rastreo", filtro "Estatus", header "Guía" alineado sobre el logo, clientes en mayúsculas, fecha "26 de Ago"-style. En A: se elimina el codo conector de la subfila (queda solo el círculo ⟳) y se retira la leyenda "de la guía {origen}"; el número de la subfila queda alineado a la columna.
- **2026-08-28 · v1.4 (Propuesta A, detalle)** — "Guías del envío": guías como filas planas (sin sub-cards ni resaltado rojo), chip de estado junto al número, "Total" sobre el monto, sin icono de imprimir; leyenda del conector en 2 líneas ("Retorno generado / Se creó la guía de retorno (fecha)"). Cronograma: fechas en el mismo formato que las guías ("18 de julio 2026"), iconos neutros sin color, hitos sin montos. "Resumen de cobro": cada cobro es desplegable con su desglose (Tarifa base, Cargos adicionales, IVA) — total del envío ahora $179.78 (ejemplo, cargos dentro de cada cobro). Nota de cobro sin número de intentos.
- **2026-08-28 · v1.6 (Propuesta A)** — Listado y detalle ahora se renderizan desde datos (`ENVIOS`), con 3 casos de retorno navegables desde "Ver detalle" o la barra de demo: **DHL con guía nueva** (3 intentos), **99 minutos con retorno en la misma guía** (2 intentos, el cliente no aceptó) y **FedEx con retorno en la misma guía** (domicilio incorrecto). Los dos casos de misma guía llevan segundo cobro sobre el mismo número. La notificación de WhatsApp se arma según el caso abierto. Logos incrustados como `<symbol>` SVG (sin `<img>` ni data URI).
- **2026-08-28 · v1.5 (Propuesta A, detalle)** — Hitos del cronograma en banda gris de borde a borde estilo Figma (icono plano sin círculo, guía como link subrayado con logo, línea punteada atravesando la banda). La alerta de cobro del retorno se movió arriba de las 2 guías en "Guías del envío". Se retiró el chip "2 cobros". Conector: "Se creó la guía de retorno el {fecha}" sin paréntesis.
- **2026-08-28 · v1.8** — Chips de estado sin icono (el ⟳ queda solo en el badge de la subfila, el toggle y los hitos). El bloque de guía del detalle replica la estructura de Figma: `[logo] {Paquetería}: {número}` + chip, servicio, referencia cruzada subrayada ("Guía retorno: …" / "Guía original: …", con la nota "(misma guía)" cuando aplica) y, a la derecha, "Fecha estimada de entrega" + fecha. Misma estructura para retorno con guía nueva y para retorno en la misma guía.
- **2026-08-28 · v1.9** — En el retorno sobre la misma guía, el primer bloque del detalle se rotula "Guía original" (antes "Guía del envío") y lleva el chip **Retornado**; el segundo mantiene "Guía retorno" con el estado de rastreo vigente. Así ambos casos —guía nueva y misma guía— usan las mismas etiquetas.
- **2026-08-28 · v1.10** — Nombres de servicio en español en todo el prototipo ("Mismo día" en 99 minutos, antes "Same day"). Estructura fija del bloque de guía: título "Guía original"/"Guía retorno" → "{Paquetería}: {número}" + chip → nombre del servicio → referencia cruzada.
- **2026-08-28 · v1.11** — En el retorno sobre la misma guía se elimina la línea de referencia cruzada (era redundante: mismo número en ambos bloques; el conector "Retorno en la misma guía" ya lo explica). Se conserva en el caso de guía nueva, donde es el vínculo entre guías. Servicio de 99 minutos: "Mismo día 24H".
- **2026-08-28 · v1.12** — Los dos primeros envíos del listado ahora tienen retorno con **guía nueva**: AMPM (AMP0459812 → AMP0461377, 3 intentos) y DHL (43567890082 → 7374310930). Se añade el logo AMPM como `<symbol>` en cuadro redondeado, consistente con los demás. La barra de demo suma el caso "AMPM · guía nueva".
