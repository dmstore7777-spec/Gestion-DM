# UI_RULES.md — DM Store

Reglas operativas para que todo desarrollo futuro del frontend se mantenga consistente con el Cloud Design. Complementa a [`DESIGN_SYSTEM.md`](DESIGN_SYSTEM.md).

## Regla 0 (la que manda sobre todas las demás)

**Los mockups de Cloud Design (`_design_handoff/DM Store.dc.html`) son la única fuente de verdad visual.**

- No se reinterpreta ni se "mejora" el diseño. Si una pantalla nueva no está en el mockup, se construye combinando los componentes ya documentados en `DESIGN_SYSTEM.md` — no se inventa un estilo nuevo.
- Si en algún momento el código y el mockup no coinciden, gana el mockup. Se corrige el código, no al revés.
- Ante la duda sobre un valor exacto (un color, un tamaño, un radio), se abre `_design_handoff/DM Store.dc.html` y se copia el valor tal cual aparece ahí — nunca se estima "a ojo".

## Antes de tocar cualquier pantalla

Checklist obligatorio antes de escribir código de UI:

1. ¿Existe ya esta pantalla en el mockup (`_design_handoff/DM Store.dc.html`)? Si sí, se replica tal cual (colores, tipografía, spacing, estructura).
2. ¿Existe un componente ya documentado en `DESIGN_SYSTEM.md` que resuelve esto (card, badge, botón, tabla, toggle, etc.)? Si sí, se reutiliza — no se crea una variante nueva "solo para esta pantalla".
3. ¿La pantalla está en el sistema legado o en Cloud Design? Ver la tabla de "Estado de adopción" en `DESIGN_SYSTEM.md`. Si está en Cloud Design (`.rd`), todo el CSS nuevo debe usar los tokens `--rd-*`, nunca los tokens legados (`--bg`, `--text`, `--green`, `--red`, `--surface`, etc.) ni `font-family:'Space Grotesk'` / `font-family:monospace` genérica.
4. ¿El cambio requiere lógica de negocio nueva o modificada? Si sí, se detiene y se confirma explícitamente con el usuario antes de tocar cálculos, validaciones o consultas a Supabase — ver Regla de negocio más abajo.

## Reglas de tokens

- **Tipografía:** Inter para todo lo que es interfaz/texto. JetBrains Mono para todo lo que es un dato cuantitativo (monto, fecha, ID, porcentaje, KPI). Nunca Space Grotesk/Space Mono en pantallas Cloud Design (esas fuentes son exclusivas del sistema legado y se van a retirar cuando se termine la migración).
- **Color:** solo se usa color de marca (verde/coral/azul/amber) cuando comunica un significado semántico real (positivo, negativo, info, pendiente). Todo lo demás va en la escala de grises (`--rd-text`, `--rd-text2`, `--rd-text3`).
- **Radio:** 6px (chips/badges), 8px (inputs/botones), 14px (cards). No se introduce un cuarto valor sin actualizar `DESIGN_SYSTEM.md` primero.
- **Sombra:** ninguna en reposo. Solo en hover elevado (`0 2px 6px rgba(0,0,0,0.3)`) o en overlays/dropdowns (`0 8px 24px rgba(0,0,0,0.45)`).
- **Espaciado:** siempre un múltiplo de 4 (4/8/12/16/24/32/48/64). No se usan valores sueltos como `13px` o `17px` de padding salvo que ya existan en un componente documentado.
- **Transiciones:** `transform`/`color`/`border-color` en 0.12–0.25s con `ease` (o la curva `cubic-bezier(.4,0,.2,1)` reservada para el toggle de moneda). Nada de `bounce`, nada de animaciones > 0.4s salvo el ícono de refresh.

## Reglas de componentes

- **No se crea UI custom si ya existe un componente Cloud Design que resuelve el caso.** Antes de escribir un `<div style="...">` a mano, revisar si ya hay una clase `.rd-*` (`.rd-card`, `.rd-label`, `.rd-btn`, `.rd-quick`, `.rd-task`, `.rd-select`, `.rd-unit-card`, etc.) que aplica.
- **El toggle de moneda (USD/ARS) es un componente único y reutilizable.** Nunca se vuelve a implementar como dos botones sueltos en ninguna pantalla nueva — se reutiliza el mismo patrón de `.curr-toggle`.
- **Las tablas de datos usan grid de columnas fijas + `1fr`, no `<table>` HTML**, siguiendo el patrón de Movimientos en el mockup (más control de alineación y responsive).
- **Los números en tablas/KPIs siempre van alineados a la derecha y en JetBrains Mono**, coloreados semánticamente si representan ingreso/egreso.
- **Los emojis se usan solo en contexto operativo/humano** (tareas, alertas, accesos rápidos) — nunca en botones de acción primaria ni en navegación (ahí van SVG geométricos de 1.6px de stroke).

## Regla de negocio (no negociable)

**Ningún trabajo de UI/diseño modifica lógica de negocio, cálculos, validaciones, cruces de datos ni consultas/conexiones a Supabase, salvo que el usuario lo pida explícitamente.**

- Migrar una pantalla al Cloud Design es un cambio de **presentación y ubicación**, nunca de **cómo se calcula un valor**.
- Si al migrar una pantalla se detecta que hace falta un dato o cálculo que hoy no existe (como pasó con "Margen" y "Ticket promedio" al construir Métricas), se implementa como una función nueva de solo lectura sobre datos ya cargados — nunca se altera una función existente que ya esté en uso en otra pantalla.
- Antes de tocar una función compartida entre una pantalla legada y una pantalla Cloud Design (ej. `deudaInfo()`, `calcCajaDisponible()`), confirmar que el cambio no altera el valor que ya ve la pantalla legada.

## Estado de la migración (mantener actualizado)

| Pantalla | Estado |
|---|---|
| Sidebar | ✅ Cloud Design |
| Topbar | ✅ Cloud Design |
| Dashboard | ✅ Cloud Design |
| Métricas | ✅ Cloud Design |
| Caja | ⏳ Legado — mockup ya existe, falta migrar |
| Cargar movimiento | ⏳ Legado — mockup ya existe, falta migrar |
| Movimientos | ⏳ Legado — mockup ya existe, falta migrar |
| Celulares / Tiendas / Servicio Técnico | ⏳ Legado — sin mockup específico (el mockup las trata como placeholder "en construcción") |
| Proveedores / Deudores / Configuración | ⏳ Legado — sin mockup específico |

Cuando se migre una pantalla de esta lista, mover su fila a "✅ Cloud Design" en el mismo commit que la migra.

## Antes de reportar un cambio de UI como terminado

1. Verificar en el navegador (preview), no solo leyendo el código.
2. Confirmar que ningún valor de negocio (montos, saldos, stock, fechas) cambió — solo su presentación.
3. Confirmar que no quedaron clases/tokens del sistema legado mezclados en la pantalla migrada (`var(--green)`, `var(--red)`, `var(--text3)`, `font-family:monospace` sin comillas de familia específica, `Space Grotesk`).
4. Si se agregó un componente nuevo reutilizable, documentarlo en `DESIGN_SYSTEM.md` antes de dar el cambio por terminado.
