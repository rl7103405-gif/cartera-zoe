# Mi Cartera — copia de Zoe

Esta copia **ya viene configurada**. Comparte el proyecto de Firebase de Roberto, pero con
un espacio de datos propio: Zoe no ve los datos de Roberto y Roberto no los ve desde la app.

> Nota de transparencia: el proyecto de Firebase es de Roberto, así que él **sí** podría ver
> estos datos desde la consola de Firebase (no desde la app). Si eso no te acomoda, la
> alternativa es crear un proyecto propio con tu cuenta de Google y seguir el README de
> `mi-cartera-plantilla`.

## Lo que ya está listo (no hay que tocarlo)

- `firebaseConfig` → proyecto `mi-cartera-b43e0`.
- `ALLOWED_EMAIL` → `zoe.frei@outlook.de`.
- Espacio de datos propio: `NS='zoe'`, `PFX='zoe_'` (documentos en `zoe/*`, colecciones
  `zoe_gastos`, `zoe_ingresos`, `zoe_transferencias`, `zoe_stocksOps`).
- Cuentas reetiquetadas para Nu y BBVA.

## Pasos para dejarla funcionando (los hace Roberto, dueño del proyecto)

### 1. Dar de alta a Zoe en Authentication
En <https://console.firebase.google.com> → proyecto `mi-cartera-b43e0` →
**Authentication → Users → Agregar usuario**: correo `zoe.frei@outlook.de` y una contraseña.
Mejor que **ella** la teclee, o que la cambie en cuanto entre.

### 2. Publicar las reglas de seguridad (IMPORTANTE)
En **Firestore Database → Reglas**, borra lo que haya y pega **completo** el contenido de
`firestore.rules` de esta carpeta. Ese archivo cubre a **los dos**: si publicas solo las
reglas de una persona, la otra pierde acceso a sus datos.

Da **Publicar** y verifica después que tu propia app siga cargando bien.

### 3. Subir esta copia a internet
Repositorio nuevo (ej. `cartera-zoe`) con estos 5 archivos: `index.html`, `manifest.json`,
`sw.js`, `icon-192.png`, `icon-512.png`. Súbelos **en un solo commit**. Luego
**Settings → Pages → Deploy from a branch → main → / (root)**.

Queda en `https://TU-USUARIO.github.io/cartera-zoe`.

### 4. Primer uso
Zoe abre esa dirección, inicia sesión, y luego:
1. **Perfil** → sus datos (edad, si vive con familia o paga renta, ingreso) y su foto.
2. **Inicio → actualizar saldos** → cuánto tiene hoy en cada cuenta.
3. Si tiene cajita en Nu, capturar su saldo, la fecha de hoy y la tasa (13% la Cajita Turbo).
   Si su cuenta de BBVA no genera rendimiento, dejar la tasa en 0.

## Si algo no cuadra

- **"acceso no autorizado" al entrar**: el correo tecleado no es exactamente
  `zoe.frei@outlook.de`, o no se creó el usuario en Authentication (paso 1).
- **Punto rojo de sincronización**: faltan las reglas del paso 2, o están publicadas solo
  para una persona.
- **Las cuentas no se llaman como su banco**: por ahora los nombres están fijos en el código.
  La versión con cuentas configurables está en camino.
