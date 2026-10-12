# HumaClear — sitio, portal de clientes y panel de administración

Sitio estático (HTML + JavaScript, sin paso de compilación) con Firebase para inicio de sesión y datos.

| Archivo | Qué es |
|---|---|
| `index.html` | Sitio comercial: demos, proceso, paquetes, cotización |
| `portal.html` | Portal de clientes (inicio de sesión, servicios, documentos, soporte) |
| `admin.html` | Panel de administración (clientes, servicios, solicitudes, prospectos) |
| `privacidad.html` | Aviso de privacidad (completa el domicilio antes de publicar) |
| `js/config.js` | **Aquí pegas la configuración de Firebase** |
| `firestore.rules` | Reglas de seguridad para pegar en Firebase |

## Probarlo ya, sin Firebase (modo demo)

Mientras `js/config.js` tenga `apiKey` vacío, todo funciona con datos locales del navegador.
Abre `portal.html` o `admin.html` y usa los botones “Entrar como cliente / administrador”.
Para verlo localmente: `python3 -m http.server 8000` dentro de esta carpeta y abre `http://localhost:8000`.

## Poner Firebase (una sola vez, ~15 minutos)

1. En <https://console.firebase.google.com> crea un proyecto (puedes desactivar Google Analytics).
2. **Authentication → Sign-in method →** activa **Correo electrónico/contraseña**.
3. **Firestore Database → Crear base de datos →** modo producción, región `us-east1` o la más cercana.
4. **Firestore → Reglas →** pega el contenido de `firestore.rules` y publica.
5. **Configuración del proyecto → Tus apps → Web (`</>`) →** registra la app y copia el objeto `firebaseConfig`
   dentro de `js/config.js`. (Esa llave es pública por diseño; la seguridad la dan las reglas.)
6. **Primer administrador:**
   - En **Authentication → Users → Add user** crea tu usuario (correo y contraseña).
   - Copia su **UID**.
   - En **Firestore → Iniciar colección** `users`, ID de documento = ese UID, con los campos:
     `name` (texto), `company` = `HumaClear`, `email` (texto), `role` = `admin`, `active` = `true` (booleano), `createdAt` (texto, p. ej. `2026-10-12`).
7. Cuando publiques en Vercel, agrega tu dominio en **Authentication → Settings → Authorized domains**.

## Publicar en Vercel

Sube esta carpeta a un repositorio de GitHub e impórtalo en Vercel (Framework: *Other*, sin comando de build,
directorio de salida: la raíz). O arrastra la carpeta con `vercel` desde la terminal.

## Uso diario

- **Dar de alta un cliente:** `admin.html` → Clientes → Nuevo cliente (nombre, empresa, correo y contraseña temporal).
  Se crea su cuenta de acceso y puede cambiar la contraseña con “Olvidé mi contraseña”.
- **Asignar servicios:** abre al cliente → marca los servicios contratados → elige estado, enlace de su sistema,
  indicadores y datos de la gráfica → Guardar. El cliente solo ve lo que tenga marcado.
  Un servicio en **Pausado** aparece bloqueado; si **pausas el acceso** del cliente, no puede entrar al portal.
- **Documentos:** sube el archivo a Drive/Dropbox, comparte el enlace y pégalo en el cliente (tipo, título, enlace https).
- **Solicitudes:** lo que el cliente envía desde Soporte llega a *Solicitudes*; responde y cambia el estado.
- **Prospectos:** las cotizaciones del sitio llegan a *Prospectos*.

## Pendientes y notas

- **Aviso de privacidad:** completa el domicilio en `privacidad.html` y pídele a un abogado que lo revise.
- **Avisos por correo:** el sitio guarda los prospectos en Firestore pero no te manda correo. Si lo quieres,
  la extensión de Firebase *Trigger Email* puede enviarte un correo por cada prospecto nuevo.
- **Archivos propios:** los documentos son enlaces. Subir archivos directo al portal requiere Firebase Storage
  (plan Blaze) y es una ampliación posible.
- **Anti-spam:** el formulario tiene un campo trampa. Si recibes basura, activa *Firebase App Check*.
- **Fuente:** Manrope se carga desde Google Fonts; el sitio funciona con una fuente del sistema si no está disponible.
