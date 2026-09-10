# Guion — video de guías de retorno (1:45 aprox.)

> Locución + qué mostrar en pantalla. Prototipo: `propuesta-a.html`
> (`t1-modulo-envios.vercel.app/propuesta-a`). Datos de ejemplo.
>
> **Encuadre:** las guías de retorno antes no existían en la plataforma —se enviaban por
> correo. Hoy ya viven en Mis envíos. De ahí salen dos casos: el retorno como guía
> independiente (**ya en producción**) y el retorno como estado adicional de la misma
> guía (**lo que resuelve este front**).

---

### 0:00 – 0:18 · Por qué esto es nuevo

**Locución**
> Hasta ahora, cuando una entrega no se concretaba, la guía de retorno se le enviaba al
> vendedor por correo electrónico: no existía dentro de la plataforma.
> Hoy ya la vemos en Mis envíos, y eso abre dos escenarios distintos según la paquetería.

**Pantalla:** listado "Mis envíos" completo, scroll lento.

---

### 0:18 – 0:38 · Caso 1 · Guía independiente (ya en producción)

**Locución**
> El primero ya está en producción: paqueterías que generan una guía de retorno
> independiente de la original. Son DHL, FedEx y 99 minutos.
> Ahí el retorno es una guía más del listado, marcada con el ícono de retorno, y desde
> su detalle se salta a la guía que lo originó.

**Pantalla:** filas de **DHL** (`431697107624` Retornado y `438427659995` con ⟳).
Abrir el detalle del retorno y hacer clic en `Guía original: 431697107624`.

---

### 0:38 – 1:05 · Caso 2 · Misma guía (lo nuevo)

**Locución**
> El segundo es el que estamos resolviendo: paqueterías donde no nace una guía nueva.
> El retorno es un estado adicional de la misma guía, con el mismo número.
> Aquí el desplegable "Ver retorno" abre ese tramo debajo de la original y muestra su
> propio estatus de rastreo, su fecha y su costo, sin salir del listado.
> Las paqueterías que trabajan así son UPS, Paquete Express, T1envíos, T1 Economic y AMPM.

**Pantalla:** clic en "Ver retorno" de **UPS 1Z9990123456**. Señalar en la subfila el
mismo número, `En camino`, `04 de Sep`, `$241.10 MXN`. Abrir una segunda (**AMPM**).

---

### 1:05 – 1:25 · El detalle lo dice explícito

**Locución**
> Al entrar al detalle no inventamos una segunda guía: una sola card muestra el número
> con la leyenda de misma guía y la fecha de entrega del retorno,
> y el cronograma marca el momento exacto en que ese retorno se inició.

**Pantalla:** **⋯ → Ver detalle** en UPS. Card **"Guía de envío / retorno"** con
`Guía retorno: 1Z9990123456` y `Fecha de entrega sobre el retorno · 07 sep`;
bajar al hito **"Retorno iniciado en la misma guía"**.

---

### 1:25 – 1:45 · Cobros por separado

**Locución**
> Y como el retorno genera un segundo cobro, el resumen ahora los presenta por separado:
> el de la guía original y el del retorno, cada uno con su subtotal, cargos adicionales
> e IVA, y al final el total real del envío. El cargo extra deja de ser una sorpresa:
> queda explicado junto a la guía que lo generó.

**Pantalla:** columna derecha del detalle de UPS. Señalar
**Guía original $269.52** → **Cobro por retorno $241.10** → **Total del envío $510.62 MXN**.

---

## Referencia rápida

| Caso | Paqueterías | Estado | Cómo se ve |
|---|---|---|---|
| **Guía de retorno independiente** | DHL · FedEx · 99 minutos | Ya en producción | Dos guías en el listado; la de retorno con ⟳ junto al número. El detalle de cada una enlaza a la otra. |
| **Retorno = estado adicional de la misma guía** | UPS · Paquete Express · T1envíos · T1 Economic · AMPM | Lo que resuelve este front | Una fila con desplegable "Ver retorno" que muestra el tramo de retorno con su estatus, fecha y costo. El detalle es una sola card con la leyenda "misma guía". |

**Cifras del ejemplo de UPS** (datos ficticios): guía original `1Z9990123456` — subtotal
$207.84 + cargos $24.50 + IVA $37.18 = **$269.52**; cobro por retorno — subtotal $174.58 +
cargos $33.26 + IVA $33.26 = **$241.10**; **total del envío $510.62 MXN**.

## Notas de grabación

- Grabar a 1440 px de ancho: la tabla se ve completa sin scroll horizontal.
- La barra oscura superior es control del prototipo (no es parte de la UI): conviene
  recortarla en la edición o no encuadrarla.
- Entrar a los detalles desde **⋯ → Ver detalle**, para que se vea el flujo real.
- Ritmo: ~150 palabras por minuto; el guion son ~265 palabras (1:45).
  Para bajarlo a 1:00, recortar el caso 1 a una frase ("el caso de guía independiente ya
  está en producción") y arrancar directo en el desplegable.
