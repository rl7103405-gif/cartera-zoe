# Mi Cartera — copia de Zoe (proyecto compartido)

PWA de finanzas personales para **una sola persona**, pensada para clonarse: cada usuario
tiene su propia copia del código y su propio proyecto de Firebase. Sin datos de nadie
precargados.

## Stack

- **HTML/CSS/JS puro en un solo `index.html`.** Sin frameworks, sin build, sin npm.
- **Firebase Firestore** (sincronización) y **Firebase Authentication** (email/password).
- Se publica tal cual en GitHub Pages.
- Archivos: `index.html`, `manifest.json`, `sw.js`, `icon-192.png`, `icon-512.png`,
  `firestore.rules`.

## Ya configurada — comparte proyecto con otra persona

Esta copia usa el proyecto de Firebase de Roberto (`mi-cartera-b43e0`) pero con un ESPACIO
de datos propio, definido arriba del todo en `index.html`:

- `NS = 'zoe'` → documentos en la coleccion `zoe/*` (saldos, perfil, tarjeta, ...).
- `PFX = 'zoe_'` → colecciones `zoe_gastos`, `zoe_ingresos`, `zoe_transferencias`, `zoe_stocksOps`.

**Toda** ruta de Firestore debe pasar por `NS`/`C_GASTOS`/`C_INGRESOS`/`C_TRANSF`/`C_OPS`.
Si agregas una ruta nueva con un literal (`doc(db,'gastos',...)`), esa escritura se iria al
espacio de la otra persona y las reglas la rechazarian: el dato se perderia en silencio.

Las claves de `localStorage` tambien se prefijan con `NS` porque ambas apps viven en el
mismo dominio de GitHub Pages y si no se pisarian entre si.

`firestore.rules` contiene las reglas del PROYECTO COMPLETO (los dos espacios). Publicar
solo una parte deja sin acceso a la otra persona.

Ver `README.md` para el paso a paso.

## Qué trae

- **Inicio**: patrimonio total, tarjetas por cuenta, "cómo vas este mes", actualizar saldos,
  transferencias, tarjetas de crédito, registro de compras de acciones, temas de color.
- **Movimientos**: alta de gastos/ingresos e historial filtrable por tipo y periodo.
- **Metas**: metas de fin de año, proyección "camino a diciembre" y apartados de ahorro.
- **Perfil**: foto, datos personales, estándar financiero personalizado y calificación de hábitos.
- **Noticias**: titulares de tus acciones y de bancos vía RSS público.
- **Deudas**: quién te debe y a quién le debes, con historial por persona.
- **Resumen**: cierre del mes/año con coach, utilidades ("cuánto ganó tu dinero"), pasteles
  interactivos, categorías y gráficas de evolución.

## Conceptos centrales del código

- `calcCompoundAt(base, fechaStr, tasaPct, ms)`: interés compuesto diario (tasa nominal /365,
  días calendario completos). **Toda** cuenta que genere rendimiento pasa por aquí; nunca
  hardcodear un monto fijo por día.
- `parseFechaLocal()` / `hoyLocal()` / `msFechaMov()`: manejo de fechas en horario local para
  evitar el clásico bug de que `YYYY-MM-DD` se lea como UTC y muestre el día anterior.
- **Patrón de escritura**: mutación optimista + `writeBatch`/`runTransaction` + rollback en
  `catch` con `backupCuentas()`/`restoreCuentas()`. Si agregas un campo nuevo al estado que
  se mute en un flujo, agrégalo también al respaldo.
- `calcularCierre()` es un **modelo puro** (no toca el DOM); `textoCoach()`/`textoAnalisis()`
  generan texto; `cierreMesHTML()` pinta. No mezclar esas capas.
- `ESTANDARES` + `estandarDe(perfil)`: única fuente de umbrales financieros (ahorro objetivo,
  gasto máximo, meses de emergencia). Coach, cierre, racha y score los consumen; no
  hardcodear porcentajes sueltos.
- **Cuentas**: hoy están fijas en el código (`CUENTAS`, campos del `state`). Volverlas
  configurables por usuario es el siguiente paso planeado de esta plantilla.

## Sin señal (16/10/2026, traído de mi-cartera; revisado por code-reviewer, Codex y qa-tester)

- Igual que en `mi-cartera` (su CLAUDE.md, sección "Sin señal"): la carga desde caché nunca se marca confirmada (banda arriba, punto rojo, reintento solo); **gasto e ingreso** sin señal van a una bandeja propia del teléfono (`cartera-bandeja-cartera-zoe`, sale de la carpeta del sitio) y suben por `txDinero` con `requerirAusentes`; lo demás pide señal y avisa al instante.
- **Regla que no se rompe:** `altaGasto`/`altaIngreso` llevan `requerirAusentes:[ref]` desde el primer intento (sin eso, una respuesta perdida cobraba dos veces).
- La lista de `desdeCache` en `loadFromFirebase` nombra EXACTAMENTE las lecturas de esta copia (aquí no hay `fDoc` ni `mtDoc`): una variable que no existe tumba la carga.
- Pruebas con emuladores (nunca producción): `Desktop\CARTERA\pruebas-sin-senal\` (`adaptar-pruebas.mjs` las adapta a cada copia; para Zoe la carpeta del sitio es `cartera-zoe`).

## Reglas de trabajo

1. **Un solo commit por tanda de cambios** — GitHub Pages atasca su cola de deploys si se
   suben varios commits seguidos.
2. Si cambias `sw.js`, **sube también la versión de `CACHE`** para invalidar el caché viejo.
3. `ALLOWED_EMAIL` es solo un gate de UI: la seguridad real está en `firestore.rules`.
4. Validar siempre lo que viene de Firestore (`Number.isFinite`, arrays, regex) — un dato
   corrupto no debe romper la app ni propagar `NaN` a los saldos.
5. Escapar con `escapeHtml()` cualquier texto del usuario que se interpole en HTML.

## Deudas con tarjeta (16/10/2026)

Igual que en `mi-cartera` (su CLAUDE.md, sección "Deudas con tarjeta"): las tarjetas de crédito de esta copia sirven para "me deben"/"le pagué" (suben su deuda) y se apagan cuando el dinero entra; `txDinero` trata un movimiento con id ya aplicado como `ya-existe` (éxito, sin cobrar dos veces) y `yaAplicado()` repinta Deudas. Pruebas: `deudas-tarjeta.mjs` y `deudas-doble.mjs` en `Desktop\CARTERA\pruebas-sin-senal\`.
