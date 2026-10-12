# Prompt para Claude Code — HumaClear

Pega esto en Claude Code, dentro de la carpeta del repositorio (la que ya subiste a GitHub). El código vive en tu repo; este documento le da todo el contexto para continuar o regenerar.

---

## Rol y objetivo
Eres un desarrollador front-end senior. Trabajas sobre **HumaClear** (https://humaclear-mx.vercel.app/), la agencia de digitalización y automatización de RH para PyMEs de Adrián. El sitio tiene dos funciones: (1) **sitio comercial** que muestra y vende los servicios; (2) **portal de clientes** con inicio de sesión donde cada cliente ve solo lo que contrató. Todo el texto visible va en **español de México**.

## Marca y oferta
- Colores: navy `#293a73` (acento principal), teal `#25b9a7` y lima solo en logo y gráficas, tinta `#1d1d1f`, gris `#6e6e73`, línea `#d6d6d6`, fondos blanco y `#f5f5f7`.
- Servicios: Entrevistas de salida, Encuestas de clima laboral, Buzón de opinión, Seguimiento 30-60-90.
- Paquetes: $18,000 / $32,000 / $45,000 MXN (desde). Contacto: humaclear@gmail.com y WhatsApp 55 7531 9051 (`525575319051`).
- No mostrar el nombre del fundador en el sitio público.

## Arquitectura (sin build)
- HTML + módulos ES nativos, desplegado en Vercel (`vercel.json`: cleanUrls, cabeceras de seguridad, noindex en portal y admin).
- Archivos: `index.html`, `portal.html`, `admin.html`, `privacidad.html`, `css/styles.css`, `css/app.css`, `js/` (`config.js`, `catalog.js`, `chart.js`, `site.js`, `portal.js`, `admin.js`, `auth-ui.js`, `backend.js`, `backend-firebase.js`, `backend-demo.js`), `firestore.rules`.
- `backend.js` elige Firebase o modo demo (localStorage) según `firebaseConfig.apiKey` en `js/config.js`. **No sobrescribir el `config.js` real de Adrián.**
- Firebase Auth (correo/contraseña) + Firestore, cargados por CDN (v10.14.1, Firestore **lite**). El admin crea clientes con una segunda instancia de la app para no cerrar su propia sesión. El primer admin se crea a mano: `users/{UID}` con `role: "admin"`.

## Modelo de datos
- `users/{uid}`: name, company, email, role (client|admin), active, createdAt.
- `contracts/{uid_serviceId}`: uid, serviceId, status (activo|implementacion|pausado), url, note, kpis[], series {title, kind hbar|column|line, unit, labels, values, max?}, updatedAt.
- `documents`: uid, title, type, url, createdAt. `requests`: uid, userName, company, kind (soporte|nuevo_servicio), serviceId, subject, message, status (abierta|en_proceso|resuelta), reply, createdAt, updatedAt.
- `leads`: creación pública validada (solo ciertas llaves, consent == true, status "nuevo"); lectura solo admin.
- Reglas: el cliente activo lee solo lo suyo; el admin escribe todo.

## Portal y admin
- Portal (`#/resumen`, `#/servicios`, `#/documentos`, `#/soporte`): servicios contratados con KPIs y gráfica, documentos (enlaces http/https), solicitudes de soporte o de nuevos servicios.
- Admin (`#/clientes`, `#/clientes/{uid}`, `#/solicitudes`, `#/prospectos`): crear clientes, editar contratos (líneas "etiqueta: valor" para la gráfica), agregar documentos, responder solicitudes, ver prospectos.
- Gráficas: librería SVG propia (`chart.js`) con barras horizontales, columnas y línea, tooltips y tabla accesible.

## Estilo (referencia Apple)
Galería blanca, bandas `#f5f5f7`, tarjetas de 28 px sin sombra, botones en píldora, títulos grandes con tracking negativo, tipografía -apple-system / SF Pro con Inter de respaldo, mucho espacio en blanco, un solo acento (navy). Etiqueta de lanzamiento en naranja `#b64400` solo como texto.

## Requisitos de calidad (criterios de aceptación)
1. **Listas desplegables y formularios funcionan** aunque Firebase falle: el sitio público no depende de `config.js` ni del backend para pintarse.
2. **Portal rápido**: el login aparece de inmediato; tras iniciar sesión, perfil y datos se piden en paralelo; Firestore lite; fuentes sin bloquear; `modulepreload`.
3. Si algo falla al arrancar, mostrar un mensaje claro con "Reintentar" (nunca un spinner infinito); tiempo límite de 12 s.
4. Sin desbordamiento horizontal a 390 px. Accesible (foco visible, `aria-*`, contraste AA).
5. Video: sección `#video` ("Míralo en acción") con `assets/humaclear.mp4` y `.webm` (~2 MB, 15 s, poster `assets/humaclear-poster.jpg`); se oculta sola si los archivos no existen.
6. Probar con Playwright: cotización, login cliente y admin, crear cliente, editar contrato, responder solicitud, y un `config.js` roto.

## Pendientes de diseño (decide con Adrián)
- Qué parte del estilo no lo convence (¿muy blanco/frío?, ¿poco color de marca?, ¿tipografía?, ¿falta imagen o video?).
- Completar el domicilio del responsable en `privacidad.html`.
