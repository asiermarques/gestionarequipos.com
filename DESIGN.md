# Design system — gestionarequipos.com

Guía de diseño visual del sitio. Para la arquitectura técnica, consulta [ARCHITECTURE.md](ARCHITECTURE.md).

Estética: **editorial cálida**. Sensación de papel y libro de no-ficción de negocio, con calidez humana (terracota) sobre una base de credibilidad profesional (navy). Evita deliberadamente el frío corporativo (azul+gris) y el "AI slop" (gradientes morados, azul SaaS).

### Concepto: el tangram

La portada del libro (opción 01, `assets/Portada_Efectivos_Revision01.pdf`) parte del tangram: siete piezas que, **sin cambiar su forma**, se organizan de distintas maneras para crear múltiples figuras. Es la metáfora del libro: cada persona aporta un valor único; la efectividad consiste en aprovechar sus fortalezas y alinearlas hacia un objetivo común.

En la web el concepto se traslada **solo a través de la propia portada y del color** (azul tinta, papel blanco, rosa cerámico), con fondos planos sin degradados. No se dibujan piezas de tangram en CSS/SVG: se probó y no aportaba.

---

## Paleta

Tokens definidos en `assets/scss/main.scss`.

| Token | Hex | Rol |
|---|---|---|
| `$primary` (terracota) | `#aa4c26` | **Acento.** Calidez, lo humano del liderazgo |
| `$secondary` (navy) | `#1B2E4B` | Estructura, autoridad, confianza |
| `$cream` | `#faf8f5` | Fondo principal (sensación papel) |
| `$navy-cover` | `#0b3a5e` | Azul tinta del título de la portada. Mundo del libro (título hero, sección de temas) |
| `$tile` | `#ecdad2` | Cerámica rosada de las piezas del tangram. Apoyo cálido (sustituye al antiguo beige) |
| `$tile-dark` | `#d6bcaf` | Bordes/marcos sobre tile, cara en sombra de la pieza |
| `$paper` | `#fdfcfb` | Blanco de la portada: fondo del hero |
| `$black` | `#1a1a1a` | Texto cuerpo |
| `$navy-deep` | `#0a2238` | Sombras, gradiente de la sección oscura, fondo del footer |
| `$terracotta-light` | `#c8694a` | Terracota más claro para texto/enlaces sobre fondo oscuro (mejor contraste que `$primary`) |
| `$terracotta-pale` | `#f3e8e2` | Terracota muy desaturado (lavados/fondos sutiles) |

### Regla de oro del acento

El terracota es **acento, no color base**. Si aparece en todo, deja de ser acento. Resérvalo para:

- Labels de sección (`.libro-label`, `.autor-label`, etc.) y su borde inferior
- `<strong>` dentro de titulares
- CTA principal (botón newsletter)
- Estados activos/hover (p. ej. hover de enlaces y botones)
- Enlaces

**No** lo uses para elementos repetidos en masa (viñetas de listas, bordes de todas las tarjetas a la vez). Para esos casos usa la cerámica (`$tile`) translúcida — aporta calidez sin saturar.

### La cerámica da atmósfera

`$tile` (y su versión más lavada `$terracotta-pale`) es el color de las piezas de la portada. Úsalo para romper la alternancia papel↔navy:
- Fondo de la sección `#autor` (`$terracotta-pale`)
- Bloque de soporte tras la portada del libro en el hero
- Viñetas y detalles cálidos sobre fondos oscuros (`rgba($tile, 0.6)`)

---

## Ritmo de fondos

Las secciones alternan fondos para crear ritmo vertical:

```
hero #libro             → papel blanco ($paper), plano (como la cubierta)
#libro-contenido        → azul tinta ($navy-cover), plano: índice del libro por partes
#resources-summary      → blanco (#fff) / #resources-detail → cream
#newsletter             → cream, con tarjeta interior navy
#autor                  → $terracotta-pale (cerámica lavada)
.site-footer            → navy profundo, con borde superior terracota de 3px
```

El hero replica la cubierta (papel blanco, título azul tinta, soporte rosa cerámico tras el libro); la sección del índice pasa al azul tinta del título. Mantén la alternancia y evita dos fondos iguales seguidos.

El **footer** (navy profundo, borde superior terracota) cierra la página: tras la cerámica de `#autor` el salto a oscuro da un cierre con peso. El azul tinta de la sección de temas y el navy profundo del footer enmarcan el contenido.

---

## Tipografía

| Uso | Familia | Notas |
|---|---|---|
| Titulares (h1–h6) | **Aleo** (serif) | weight 700–800, `letter-spacing: -0.02em` en grandes |
| Cuerpo | **Mulish** (sans) | `line-height: 1.75`, base ~1.05rem |
| Labels / overline | Mulish | 0.68–0.7rem, weight 800, `letter-spacing: 0.18em`, mayúsculas, color terracota |
| Título del libro (hero) | **Mulish 900** (`$font-display`) | Eco de Avenir Black de la cubierta. Mayúsculas, `$navy-cover`, `line-height: 0.9`. Solo para "EFECTIVOS" |
| Subtítulo serif (hero), títulos y preguntas del índice | **Crimson Pro** (`$font-serif-cover`) | Eco de Minion Pro de la cubierta. "gestionar equipos" en cursiva terracota |

Carga vía Google Fonts CDN (ver `head/css.html`). **Crimson Pro y Mulish 900 en mayúsculas son exclusivas del bloque del libro** (mundo de la portada); el resto del sitio mantiene Aleo + Mulish, en línea con el logotipo.

---

## Contraste y accesibilidad

Texto claro sobre navy: **no bajes de `rgba(#fff, 0.72)`** para texto pequeño (< 1rem). Por debajo de ~0.7 el contraste cae bajo AA. Patrón aplicado en el índice de `#libro-contenido` (capítulos 0.82, preguntas 0.86) y el párrafo de `#newsletter` (0.72).

> Las advertencias de `color-contrast` al compilar provienen de los botones de Bootstrap (`#198754`, `#dc3545`), no del diseño propio.

---

## Forma y profundidad

- **Sin esquinas redondeadas** por defecto: `$enable-rounded: false`, `$radius: 0`. Aspecto editorial/anguloso. Excepción puntual: el `border-radius: 6px` del `.availability-badge`.
- **Sombras** sobre fondos claros: tintadas en navy (`rgba($navy-cover, …)`), no negro.
- **Marcos decorativos**: bloque `$tile` desplazado tras la portada (`#libro .cover-col::before`) y marco con borde tras la foto del autor. Patrón de "objeto sobre soporte".
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
2. Respeta la regla del acento: terracota selectivo, cerámica (`$tile`) para calidez de relleno.
3. Mantén la alternancia de fondos.
4. Texto claro sobre oscuro: mínimo `rgba(#fff, 0.72)`.
5. Crea un `.scss` por sección e impórtalo en `main.scss` (ver ARCHITECTURE.md).
