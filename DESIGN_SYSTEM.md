# DESIGN_SYSTEM.md — DM Store

> **Fuente de verdad:** los mockups de Cloud Design en [`_design_handoff/DM Store.dc.html`](_design_handoff/DM Store.dc.html) (incluye una pantalla "Guía de marca" con la paleta, tipografía, espaciado, radios y sombras oficiales — líneas 490-628 de ese archivo).
> Este documento **no inventa** ningún valor: es la transcripción de esos mockups a una referencia usable durante el desarrollo. Si algo acá no coincide con el mockup, el mockup gana y este documento se corrige.
>
> Este sistema se conoce en el código como **"Cloud Design"** o **`.rd`** (clase CSS que activa el scope — ver `<style>` de `gestion_dm_v9_17.html`, sección `/* Rediseño Dashboard / Metricas */`).

## Estado de adopción (importante)

El archivo `gestion_dm_v9_17.html` hoy conviene **dos sistemas visuales en paralelo**:

| Sistema | Dónde vive | Pantallas |
|---|---|---|
| **Cloud Design (`.rd`)** — este documento | Todo dentro de un contenedor con clase `.rd` | Sidebar, Topbar, Dashboard, Métricas |
| **Legado** (Space Grotesk, paleta `--bg/--text/--green/--red`, `font-family:monospace` genérica) | El resto del `<style>` | Caja, Cargar movimiento, Movimientos, Celulares, Tiendas, Servicio Técnico, Proveedores, Deudores, Configuración |

**Regla:** ninguna pantalla nueva o migrada debe usar tokens del sistema legado. Toda pantalla nueva se construye 100% con los tokens y componentes de este documento. La migración del resto de las pantallas es un trabajo aparte, pendiente, que se hace pantalla por pantalla — no se toca lógica de negocio al hacerlo (ver `UI_RULES.md`).

---

## 1. Color Tokens

Definidos como CSS custom properties dentro del scope `.rd` (`gestion_dm_v9_17.html`, regla `.rd{...}`):

| Token | Valor | Uso |
|---|---|---|
| `--rd-bg` | `#131316` | Fondo de la app (detrás de todo) |
| `--rd-card` | `#1a1b1f` | Superficie de cards, sidebar, inputs, filas de tabla |
| `--rd-border` | `rgba(255,255,255,0.07)` | Borde por defecto de cards y separadores |
| `--rd-border-hi` | `rgba(255,255,255,0.16)` | Borde en hover / estado destacado |
| `--rd-text` | `#f4f4f2` | Texto primario |
| `--rd-text2` | `#94969c` | Texto secundario |
| `--rd-text3` | `#5c5e66` | Texto terciario / labels / captions |
| `--rd-green` | `#3ecf8e` | Brand · positivo · ingreso |
| `--rd-green-lt` | `rgba(62,207,142,0.14)` | Fondo tenue para estados positivos (chips, hover de nav activo) |
| `--rd-red` | `#ef7b63` | Negativo · egreso (es un coral, no un rojo puro) |
| `--rd-red-lt` | `rgba(239,123,99,0.14)` | Fondo tenue para estados negativos |

Colores adicionales que aparecen en los mockups pero **todavía no tienen variable CSS** en el código (agregar si se migran más pantallas):

| Color | Valor | Uso |
|---|---|---|
| Info / vínculos | `#5b9bf2` | Datos neutrales, enlaces, badge "USD" |
| Advertencia | `#e8b84b` | Pendientes, alertas de stock, badge "PENDIENTE" |

### Reglas de color (verbatim de la Guía de marca)

> **"El color informa, no decora."** Verde = positivo/ingreso. Coral = negativo/egreso. Azul = información neutral. Nunca se usan de forma decorativa — si un elemento no tiene un significado semántico, va en gris (`--rd-text2` / `--rd-text3`), nunca en color de marca.

---

## 2. Typography Scale

Dos familias, cada una con un trabajo fijo — **nunca se mezclan ni se usan cursivas**:

| Familia | Google Fonts | Uso |
|---|---|---|
| **Inter** | `wght@400;500;600;700;800` | Títulos, interfaz, labels, botones, navegación. Usar 600+ para jerarquía. |
| **JetBrains Mono** | `wght@400;500;600;700` | **Todo** monto, fecha, ID o métrica. Alinea columnas y comunica "esto es un dato exacto". |

### Escala

| Nivel | Tamaño | Peso | Familia | Uso |
|---|---|---|---|---|
| H1 | 26px (Topbar usa 22px) | 700 | Inter | Título de página (`.page-title`) |
| H2 | 19px | 700 | Inter | Título de tarjeta / sección grande |
| Body | 14px | 500 | Inter | Texto de interfaz, labels de campos |
| Caption / eyebrow | 10–11px | 600–700 | Inter | `letter-spacing:0.07–0.08em`, `text-transform:uppercase`, color `--rd-text3` — es el patrón `.rd-label` |
| Dato numérico | 12.5–24px según contexto | 700 | JetBrains Mono | Montos, KPIs, fechas en tablas |

**Regla dura:** si el valor es cuantitativo (plata, porcentaje, fecha, cantidad), va en JetBrains Mono. Si es texto/label/UI, va en Inter. No hay excepciones.

---

## 3. Spacing System

Escala de **4px**, verbatim de la Guía de marca:

```
4 · 8 · 12 · 16 · 24 · 32 · 48 · 64
```

Usos típicos observados en los mockups:
- `gap` entre cards de una grilla: **16px** (KPIs, unidades) o **14px** (acciones rápidas, grillas más densas)
- `padding` interno de card estándar: **16–20px** horizontal / **16–22px** vertical
- `padding` de card grande (Caja, Guía de marca): **24–28px**
- Separación entre secciones verticales del Dashboard: **20–22px**
- Padding de topbar: **22–36px** horizontal

> **"Aire con propósito."** Densidad alta en tablas, respiro generoso entre secciones — nunca ambas cosas en el mismo lugar.

---

## 4. Radius System

Tres niveles fijos, verbatim de la Guía de marca:

| Radio | Uso |
|---|---|
| **6px** | Chips, badges, botones pequeños |
| **8px** | Inputs, botones, barra unificada de topbar |
| **14px** | Cards (el contenedor "grande" de cualquier bloque de contenido) |

Adicional observado en componentes específicos:
- **10–12px**: variante intermedia usada en filas de tarea (`.rd-task`) y quick-actions (`.rd-quick`) — tratarlo como un radio de "card chica interactiva", no como un cuarto nivel de la escala.
- **50%**: siempre para elementos circulares (dots de estado, checkbox de tarea completada, avatar).

---

## 5. Shadow System

Tres niveles, verbatim de la Guía de marca (los mockups son mayormente **flat**, sin sombra, y reservan la sombra para elevación real):

| Nivel | Valor | Uso |
|---|---|---|
| **Flat** (default) | ninguno — solo `border:1px solid rgba(255,255,255,0.07/0.08)` | Estado de reposo de cualquier card |
| **Hover** | `0 2px 6px rgba(0,0,0,0.3)` | Cuando un elemento se eleva ligeramente en hover (combinado con `transform:translateY(-2px)`) |
| **Dropdown / overlay** | `0 8px 24px rgba(0,0,0,0.45)` | Tooltips, dropdowns, menús flotantes, modales |

**Regla:** nunca usar sombra como decoración de reposo. Las cards en reposo se distinguen del fondo solo por el borde sutil (`--rd-border`) y el cambio de superficie (`--rd-bg` → `--rd-card`), no por sombra.

---

## 6. Transition System

| Propiedad | Duración | Easing | Uso |
|---|---|---|---|
| `transform` (hover elevación) | `0.15s` | `ease` | Cards, quick actions, unit cards, task rows |
| `border-color` (hover) | `0.15s` | `ease` | Acompaña siempre a la transición de transform |
| `color` (nav, toggle) | `0.12–0.2s` | `ease` | Item de menú, opción de toggle |
| Toggle switch (thumb deslizante) | `0.25s` | `cubic-bezier(.4,0,.2,1)` | Único componente con easing "premium" explícito — es el que más se nota, por eso tiene su propia curva |
| Progress bar (ancho) | `0.4s` | `ease` | Barra de "Progreso de hoy" |
| Refresh icon (rotación) | `0.7s` | `ease` | Ícono de "Actualizar" mientras refresca |
| Toast in/out | `0.25s` | implícito (translate + opacity) | Aparición/desaparición de toast |

**Regla:** las animaciones son siempre sutiles — nunca bounce, nunca > 0.4s salvo el refresh icon, nunca en propiedades que produzcan reflow costoso (usar `transform`, no `top/left/width` salvo la progress bar que anima `width` intencionalmente porque es una barra de progreso real).

---

## 7. Icon Guidelines

Verbatim de la Guía de marca: **"Iconografía — geométrica, 1.6px stroke, sin relleno decorativo."**

- SVG inline, `viewBox="0 0 20 20"`, tamaño de render **17×17px** en nav, **12–15px** en contextos secundarios.
- `stroke-width:1.6` para íconos de línea; los íconos "rellenos" (dashboard grid, movimientos, métricas) usan `fill` sólido con un segundo tono en `opacity:0.55–0.75` para dar profundidad sin agregar un color nuevo.
- Color del ícono = color del texto que acompaña (`--rd-text2` inactivo, `--rd-green` activo). El ícono nunca lleva un color propio distinto al texto.
- Emojis se usan **solo** en contenido operativo/humano (accesos rápidos, tareas, alertas: 📦 🔧 💸 🟢 🟠) — nunca en navegación ni en botones de acción primaria.

---

## Component Anatomy

Documentación de los componentes tal como existen en el mockup + su implementación actual en código.

### Sidebar
- Ancho: **236–252px**, fondo `#121214` (más oscuro que `--rd-bg`, es un tono aparte solo para el sidebar), borde derecho `1px solid rgba(255,255,255,0.07)`.
- Secciones: eyebrow uppercase (`General`, `Unidades`) en `--rd-text3`.
- Item de nav: `padding:9px 12px`, `border-radius:8px`, `gap:10–11px`, ícono 17px + label 13–13.5px.
- Estado activo: `background:rgba(62,207,142,0.12–0.14)`, texto y borde de ícono en `--rd-green`, `font-weight:600–700`.
- Estado hover (inactivo): `background:rgba(255,255,255,0.05)`, texto `--rd-text`.

### Topbar
- Alto **64px** (contenedor) conteniendo un título de página (`.page-title`, 22px/700) a la izquierda.
- **Barra unificada** a la derecha (`.topbar-unified`): un solo contenedor con `border-radius:8–10px`, `overflow:hidden`, que agrupa en una sola pieza:
  1. Cotización del dólar (texto mono, verde, fondo `rgba(62,207,142,0.14)`)
  2. Divisor de 1px
  3. Toggle USD/ARS (ver abajo)
  4. Divisor de 1px
  5. Botón "Actualizar" (fondo sólido `--rd-text`, texto `--rd-bg`, ícono de refresh que rota al activarse)
- **Nunca** son 3-4 elementos sueltos con gaps — es una sola pieza visual continua.

### Toggle Switch (USD/ARS)
- Contenedor `104–112px × 34–38px`, dos opciones de texto superpuestas a un "thumb" que se desliza detrás.
- Thumb: fondo `--rd-text` (blanco cálido), `border-radius:6px`, `transition:transform 0.25s cubic-bezier(.4,0,.2,1)`.
- Texto activo (encima del thumb): color `--rd-bg` (oscuro, por contraste). Texto inactivo: `--rd-text2`.
- Es el **único** componente de moneda permitido en la app — nunca dos botones sueltos.

### Button
Variantes (verbatim de la sección "Componentes" de la Guía de marca):

| Variante | Estilo |
|---|---|
| **Primario** | `background:--rd-green`, texto oscuro (`#0e1512`), `font-weight:700` |
| **Secundario** | `background:--rd-bg`, `border:1px solid rgba(255,255,255,0.1)`, texto `--rd-text` |
| **Ghost** | Sin fondo ni borde, texto `--rd-text2`, se usa para acciones de bajo énfasis ("Cancelar") |
| **Destructivo** | `background:rgba(239,123,99,0.14)`, texto `--rd-red`, `font-weight:700` |

Todos: `padding:11px 20px` (botón grande de formulario) o `6px 13px` (botón chico de topbar/card, clase `.rd-btn`), `border-radius:8px`, `font-size:12.5–14px`.

### Badge / Status Chip
- `padding:4–5px 9–11px`, `border-radius:6px`, `font-size:11–11.5px`, `font-weight:700`.
- Color = fondo tenue (`-lt`) + texto sólido del mismo tono semántico. Nunca texto blanco sobre fondo sólido (rompería el "flat, sin ruido").
- Mapeo semántico: `INGRESO`→verde, `EGRESO`→coral, `PENDIENTE`→amber.

### Card (`.rd-card`)
- `background:--rd-card`, `border:1px solid --rd-border`, `border-radius:14px`, `padding:16-22px`.
- Hover: `transform:translateY(-2px)` + (si es clickeable) `border-color:--rd-green`.
- Nunca sombra en reposo (ver sección Shadows).

### KPI Card
- Igual que `.rd-card` pero con estructura fija: eyebrow label arriba (`.rd-label`), valor grande en JetBrains Mono debajo (`16–32px` según jerarquía — 16px en grillas de 4, 24px en columna lateral, 32px en hero de Caja), indicador de tendencia opcional (`↑/↓ N% vs. mes` en verde/coral).

### Table / DataTable
- Contenedor: `.rd-card` sin padding (`overflow:hidden`), con filas en `display:grid` de columnas fijas + `1fr` para la descripción (no `<table>` HTML — ver Movimientos en el mockup).
- Header de columnas: mismo patrón `.rd-label` (uppercase, `--rd-text3`, 10.5px), `border-bottom:1px solid --rd-border`.
- Filas: `padding:14px 20px`, `border-bottom:1px solid rgba(255,255,255,0.05)` (más sutil que el borde de card), última fila sin borde.
- Columna numérica: siempre alineada a la derecha, siempre JetBrains Mono, siempre coloreada semánticamente si es ingreso/egreso.
- Empty state de tabla: texto centrado, `padding:40px`, color `--rd-text3`, sin ícono (solo texto descriptivo).

### Empty State (genérico, "módulo en construcción")
- Ícono outline punteado (`stroke-dasharray`) 40×40px en gris tenue (`#3a3b41`).
- Título 15px/600 en `--rd-text2`, subtítulo 13px en `--rd-text3`, `max-width:340px`, centrado.

### Progress Bar
- Track: `height:8px`, `border-radius:6px`, `background:--rd-bg`.
- Fill: `background:--rd-green`, `border-radius:6px`, `transition:width 0.4s ease`.

### Checkbox (task row)
- No chequeado: cuadrado `17×17px`, `border-radius:5px`, `border:1.6px solid rgba(255,255,255,0.22)`, sin fondo.
- Chequeado: `background:--rd-green-lt`, sin borde, ícono de check en `--rd-green` adentro.

### Toast
- Fondo sólido `--rd-text` (o `--rd-red` si es error), texto contrastante, `border-radius:8px`, entra desde abajo con `translateY + opacity`.

### Modal / Dropdown / Tooltip
- Fondo `#202127` (un tono más claro que `--rd-card`, exclusivo de overlays flotantes), `border:1px solid rgba(255,255,255,0.1)`, `border-radius:10–14px`, sombra de dropdown (`0 8px 24px rgba(0,0,0,0.45)`).

### Quick Action / Task Row / Activity Row
Todas comparten la misma anatomía base de `.rd-card` interactiva: fondo `--rd-card`, borde `--rd-border`, `border-radius:10–12px`, hover con `translateY(-1px/-2px)` + `border-color` más vivo. Se diferencian solo en el contenido interno (ícono+label vs. checkbox+label+badge vs. texto+timestamp).

---

## Layout Patterns

| Patrón | Grid | Dónde |
|---|---|---|
| KPIs de métricas | `repeat(4, minmax(0,1fr))`, gap 16px | Métricas (fila superior) |
| Gráfico + panel lateral | `1fr 240–300px`, gap 16–20px | Métricas, Dashboard (resumen) |
| Por unidad | `repeat(3, 1fr)`, gap 16px | Métricas, Caja |
| Acciones rápidas | `repeat(4, minmax(0,1fr))`, gap 14px | Dashboard |
| Caja hero | `1fr 1fr`, gap 16px | Caja |
| Form / Cargar movimiento | contenedor `max-width:460px`, campos `1fr` con un carril fijo de `110px` para el selector de moneda | Cargar movimiento |
| Tabla de movimientos | columnas fijas `80px 76px 100px minmax(0,1fr) 116px` | Movimientos |

---

## Principios de uso (verbatim, Guía de marca)

1. **Menos clics, siempre.** Toda acción frecuente (cargar movimiento, cambiar moneda) vive a un clic desde cualquier pantalla.
2. **El color informa, no decora.** Verde = positivo/ingreso. Coral = negativo/egreso. Azul = información neutral. Nunca de forma decorativa.
3. **Los números son sagrados.** Todo dato cuantitativo en monoespaciada, alineado a la derecha en tablas.
4. **Aire con propósito.** Densidad alta en tablas, respiro generoso entre secciones — nunca ambas cosas en el mismo lugar.
