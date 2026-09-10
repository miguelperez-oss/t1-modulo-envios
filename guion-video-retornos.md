# Guion — video de guías de retorno (1:45 aprox.)

> Locución + qué mostrar en pantalla. Prototipo: `propuesta-a.html`
> (`t1-modulo-envios.vercel.app/propuesta-a`). Datos de ejemplo.

---

### 0:00 – 0:12 · El problema

**Locución**
> Cuando una entrega no se concreta, la paquetería devuelve el paquete y genera un cobro.
> Hoy ese retorno aparece en el listado como una guía más, sin decir de qué envío salió.

**Pantalla:** listado "Mis envíos" completo, scroll lento.

---

### 0:12 – 0:35 · El desplegable

**Locución**
> Ahora cada guía que fue retornada trae el ícono de retorno y el estatus "Retornado".
> Al abrir "Ver retorno" se despliega la guía de retorno debajo de la original: su propio
> estatus de rastreo, su fecha y su costo, sin salir del listado ni perder el contexto.

**Pantalla:** clic en "Ver retorno" de **UPS 1Z9990123456**. Señalar en la subfila:
`En camino` · `04 de Sep` · `$241.10 MXN`. Cerrar y abrir una segunda (**AMPM**).

---

### 0:35 – 0:52 · Filtro y navegación

**Locución**
> El filtro "Tipo de guía" permite ver solo retornos, y desde los tres puntos entramos
> al detalle.

**Pantalla:** filtro **Tipo de guía → Retorno**; volver a "Todas"; abrir
**⋯ → Ver detalle** en la fila de UPS.

---

### 0:52 – 1:15 · Detalle: cobros por separado

**Locución**
> En el detalle, el "Resumen de cobro" ya muestra los dos cobros por separado: el de la
> guía original y el del retorno, cada uno con su subtotal, cargos adicionales e IVA,
> y al final el total real del envío. Así el cobro extra deja de ser una sorpresa:
> se explica junto a la guía que lo generó.

**Pantalla:** columna derecha del detalle de UPS.
Señalar: **Guía original $269.52** → **Cobro por retorno $241.10** → **Total del envío $510.62 MXN**.

---

### 1:15 – 1:32 · Cuando el retorno usa la misma guía

**Locución**
> Hay paqueterías que no generan guía nueva: el retorno viaja con el mismo número.
> Para esos casos el detalle lo dice explícitamente —una sola card con la leyenda
> "misma guía" y la fecha de entrega del retorno— y el cronograma marca el momento
> exacto en que el retorno se inició sobre esa guía.

**Pantalla:** card **"Guía de envío / retorno"** con `Guía retorno: 1Z9990123456`
y `Fecha de entrega sobre el retorno · 07 sep`; bajar al hito
**"Retorno iniciado en la misma guía"**.

---

### 1:32 – 1:45 · Qué paquetería hace cada cosa

**Locución**
> Con guía nueva trabajan DHL, FedEx y 99 minutos: ahí el retorno es una guía aparte
> en el listado y desde su detalle se salta a la original.
> Con la misma guía trabajan UPS, Paquete Express, T1envíos, T1 Economic y AMPM.

**Pantalla:** detalle de **DHL** (`Guía retorno: 438427659995` → clic al link
`Guía original: 431697107624`); cerrar con el listado completo.

---

## Referencia rápida

| Comportamiento | Paqueterías | Cómo se ve |
|---|---|---|
| **Guía nueva** | DHL · FedEx · 99 minutos | Dos guías en el listado; la de retorno con ⟳ junto al número. El detalle de cada una enlaza a la otra. |
| **Misma guía** | UPS · Paquete Express · T1envíos · T1 Economic · AMPM | Una fila con desplegable "Ver retorno"; el detalle es una sola card con la leyenda "misma guía". |

**Cifras del ejemplo de UPS** (datos ficticios): guía original `1Z9990123456` — subtotal
$207.84 + cargos $24.50 + IVA $37.18 = **$269.52**; cobro por retorno — subtotal $174.58 +
cargos $33.26 + IVA $33.26 = **$241.10**; **total del envío $510.62 MXN**.

## Notas de grabación

- Grabar a 1440 px de ancho: la tabla se ve completa sin scroll horizontal.
- La barra oscura superior es control del prototipo (no es parte de la UI): conviene
  recortarla en la edición o no encuadrarla.
- Al abrir un detalle se llega desde **⋯ → Ver detalle**, para que se vea el flujo real.
- Ritmo: ~150 palabras por minuto. El guion completo son ~250 palabras (1:45).
  Si se necesita 1:00, se puede omitir el bloque de filtro (0:35–0:52).
