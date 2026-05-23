# Design system — gestionarequipos.com

Guía de diseño visual del sitio. Para la arquitectura técnica, consulta [ARCHITECTURE.md](ARCHITECTURE.md).

Estética: **editorial cálida**. Sensación de papel y libro de no-ficción de negocio, con calidez humana (terracota) sobre una base de credibilidad profesional (navy). Evita deliberadamente el frío corporativo (azul+gris) y el "AI slop" (gradientes morados, azul SaaS).

---

## Paleta

Tokens definidos en `assets/scss/main.scss`.

| Token | Hex | Rol |
|---|---|---|
| `$primary` (terracota) | `#aa4c26` | **Acento.** Calidez, lo humano del liderazgo |
| `$secondary` (navy) | `#1B2E4B` | Estructura, autoridad, confianza |
| `$cream` | `#faf8f4` | Fondo principal (sensación papel) |
| `$beige` | `#eadec3` | Apoyo cálido neutro |
| `$beige-dark` | `#d4c5a3` | Bordes/marcos sobre beige |
| `$black` | `#1a1a1a` | Texto cuerpo |
| `$navy-deep` | `#0f1e32` | Sombras, gradiente de la sección oscura, fondo del footer |
| `$terracotta-light` | `#c8694a` | Terracota más claro para texto/enlaces sobre fondo oscuro (mejor contraste que `$primary`) |
| `$terracotta-pale` | `#f3e8e2` | Terracota muy desaturado (lavados/fondos sutiles) |

### Regla de oro del acento

El terracota es **acento, no color base**. Si aparece en todo, deja de ser acento. Resérvalo para:

- Labels de sección (`.libro-label`, `.autor-label`, etc.) y su borde inferior
- `<strong>` dentro de titulares
- CTA principal (botón newsletter)
- Estados activos/hover (p. ej. el borde superior de `.topic-block` solo se vuelve terracota en hover)
- Enlaces

**No** lo uses para elementos repetidos en masa (viñetas de listas, bordes de todas las tarjetas a la vez). Para esos casos usa beige translúcido — aporta calidez sin saturar.

### El beige da atmósfera

El beige es el color que más "calidez de papel" aporta. Úsalo para romper la alternancia crema↔navy:
- Fondo de la sección `#autor`
- Bloque decorativo tras la portada del libro
- Viñetas y detalles cálidos sobre fondos oscuros (`rgba($beige, 0.6)`)

---

## Ritmo de fondos

Las secciones alternan fondos para crear ritmo vertical:

```
hero #libro             → navy profundo (mundo de la portada: radial 50%/-10%, #1c3354 → secondary → navy-deep)
#libro-contenido        → navy (gradiente 145deg: #0f1e32 → secondary → #1e3a5f)
#resources-summary      → blanco (#fff) / #resources-detail → cream
#newsletter             → cream, con tarjeta interior navy
#autor                  → beige
.site-footer            → navy profundo, con borde superior terracota de 3px
```

**Excepción deliberada al ritmo:** el hero y la sección de temas son ambos oscuros y forman un único "mundo del libro" inmersivo que replica la cubierta. Se separan con un hairline `rgba($beige, 0.12)`. A partir de la biblioteca se recupera la alternancia con crema. Fuera de este bloque, mantén la alternancia y evita dos fondos iguales seguidos.

El **footer** (navy profundo, borde superior terracota) cierra la página: tras el beige de `#autor` el salto a oscuro da un cierre con peso. Es el único navy que aparece dos veces (hero al inicio, footer al final) y eso es intencional: enmarca el documento.

---

## Tipografía

| Uso | Familia | Notas |
|---|---|---|
| Titulares (h1–h6) | **Aleo** (serif) | weight 700–800, `letter-spacing: -0.02em` en grandes |
| Cuerpo | **Mulish** (sans) | `line-height: 1.75`, base ~1.05rem |
| Labels / overline | Mulish | 0.68–0.7rem, weight 800, `letter-spacing: 0.18em`, mayúsculas, color terracota |
| Título del libro (hero) | **Bebas Neue** (`$font-display`) | Replica la cubierta. Mayúsculas, terracota, `line-height: 0.82`. Solo para "EFECTIVOS" |
| Subtítulo serif + autor (hero) | **Cormorant Garamond** (`$font-serif-cover`) | Replica la cubierta. "gestionar equipos" en cursiva 600; byline del autor en redonda 500 |

Carga vía Google Fonts CDN (ver `head/css.html`). **Bebas Neue y Cormorant Garamond son exclusivas del bloque del libro** (mundo de la portada); no las uses en el resto del sitio, que mantiene Aleo + Mulish.

---

## Contraste y accesibilidad

Texto claro sobre navy: **no bajes de `rgba(#fff, 0.72)`** para texto pequeño (< 1rem). Por debajo de ~0.7 el contraste cae bajo AA. Patrón aplicado en las viñetas de `#libro-contenido` (0.74) y el párrafo de `#newsletter` (0.72).

> Las advertencias de `color-contrast` al compilar provienen de los botones de Bootstrap (`#198754`, `#dc3545`), no del diseño propio.

---

## Forma y profundidad

- **Sin esquinas redondeadas** por defecto: `$enable-rounded: false`, `$radius: 0`. Aspecto editorial/anguloso. Excepción puntual: el `border-radius: 6px` del `.availability-badge`.
- **Sombras** sobre fondos claros: tintadas en navy profundo, no negro (`rgba(#0f1e32, …)`). Excepción: la portada flotante del hero va sobre navy, donde el navy es invisible, así que usa negro (`rgba(#000, 0.55)`) para dar profundidad real.
- **Abanico de sombras (hero)**: `conic-gradient` desde `50% -8%` que irradia cuñas tenues hacia abajo (`.hero-rays`), con la cuña central en terracota — evoca los obeliscos de la cubierta. Enmascarado con `mask-image` para fundir arriba y abajo. Acompañado de un grano SVG (`.hero-grain`, `mix-blend-mode: overlay`).
- **Marcos decorativos**: bloque beige translúcido desplazado tras la portada (`#libro .cover-col::before`, `rgba($beige, 0.10)` sobre el hero oscuro) y marco con borde tras la foto del autor. Patrón de "objeto sobre soporte".
- **Byline del autor**: regla punto-guion terracota (`.author-rule`) + nombre en Cormorant, replicando el pie de la cubierta.
- **Hover de portada/foto**: `translateY(-6px)` con transición suave.
- **Duotono navy en las tarjetas de la biblioteca** (`a.book-item .cover-wrap::after`): en reposo las portadas se tiñen de navy (`mix-blend-mode: color`, opacidad 0.92) para que la rejilla luzca cohesionada con la paleta y no como un collage de colores dispares. Al hover/focus el velo se desvanece y revela el color real de la portada, que sube `translateY(-4px)`. **Las portadas en la página de detalle (`#resource-detail`) van a todo color, sin duotono** — ahí la portada es protagonista, no parte de una rejilla.

---

## Movimiento

Solo CSS (no hay JS). Comedido:
- `scroll-behavior: smooth`
- Transiciones de 0.2–0.35s en color, fondo y `transform` (hover)
- `@keyframes pulse` en el `.badge-dot` de disponibilidad del libro (único elemento animado de forma continua)

---

## Al añadir o modificar diseño

1. Usa los tokens de `main.scss`, nunca hardcodees hex nuevos sin justificarlos aquí.
2. Respeta la regla del acento: terracota selectivo, beige para calidez de relleno.
3. Mantén la alternancia de fondos.
4. Texto claro sobre oscuro: mínimo `rgba(#fff, 0.72)`.
5. Crea un `.scss` por sección e impórtalo en `main.scss` (ver ARCHITECTURE.md).
