# Módulo de envíos · Guías de retorno (T1envíos)

Prototipos de UX para el rediseño de **Mis envíos**, enfocados en cómo se muestran las
guías de retorno, los intentos de entrega y los cobros consecuentes.

## Qué hay aquí

| Archivo | Qué es |
|---|---|
| `propuesta-a.html` | **Propuesta A · Hilo agrupado** — el retorno vive dentro de su envío (subfila anidada) y el detalle es único por envío. Es la línea que se está iterando. |
| `propuesta-b.html` | **Propuesta B · Guías vinculadas** — cada guía conserva su fila y su detalle, con vínculos cruzados. |
| `spec-guias-retorno.md` | Spec funcional: reglas de diseño, estados, copy canónico, notificaciones, métricas, edge cases y changelog. |
| `AES/` | Kit interno "Agente UX de T1" — no versionado. Pídelo al equipo de UX antes de iterar. |
| `serve.mjs` | Servidor local para revisar en `http://localhost:4173`. |
| `dist/` | Copias publicadas como Artifacts. |

## Cómo trabajarlo

Los prototipos son **HTML de un solo archivo** (sin build, sin dependencias): ábrelos con
doble clic o sírvelos en local.

```bash
node serve.mjs   # http://localhost:4173/propuesta-a.html
```

En la Propuesta A el listado y el detalle se renderizan desde el arreglo `ENVIOS` que está
en el `<script>` al final del archivo: ahí se editan los casos, los cobros y el cronograma.
Los logos de paqueterías están incrustados como `<symbol>` SVG — no hay imágenes externas.

## Casos de retorno incluidos (datos de ejemplo)

| Paquetería | Comportamiento | Guías |
|---|---|---|
| AMPM | Guía de retorno nueva | AMP0459812 → AMP0461377 |
| DHL | Guía de retorno nueva | 43567890082 → 7374310930 |
| 99 minutos | Retorno en la misma guía + segundo cobro | 99M240815330 |
| FedEx | Retorno en la misma guía + segundo cobro | 794658219873 |

## Reglas que gobiernan el diseño

1. **Ninguna guía huérfana** — todo retorno muestra su guía origen y viceversa.
2. **El cobro se explica donde se ve** — cada cargo junto a la guía que lo generó.
3. **El retorno informa, no alarma** — es estado del proceso, no error.

Sistema de diseño: **NEXUS V2.0**, contexto dashboard (Manrope, rojo `#DB3B2B`, cards
planas con borde, radius 10 px). Todos los datos son ficticios.
