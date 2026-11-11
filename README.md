# OBRA360

Calendario personal y compartido para postventa de obras. `index.html` + `config.js` (sitio estático), `supabase/` (base de datos y función de invitaciones).

## Probarlo ya, sin configurar nada
Abre `index.html` (o `npx serve -l 8080 .` y entra a http://localhost:8080). Con `config.js` vacío funciona en **modo demostración**: ana@obra360.cl / demo123 (o marco@obra360.cl). Los datos se guardan solo en ese navegador. Ideal para mostrárselo a tu mamá.

## Versión real (con Google e invitaciones por Gmail)
1. **Supabase** (supabase.com, gratis): crea un proyecto. Anota *Project URL* y la clave *anon* (Project Settings → API).
2. **Base de datos:** SQL Editor → pega todo `supabase/migrations/0001_init.sql` → Run.
3. **Google Cloud** (console.cloud.google.com), en un proyecto nuevo:
   - Biblioteca: activa **Gmail API**.
   - Pantalla de consentimiento OAuth: tipo *Externo*, déjala en modo **Testing**, agrega el alcance `https://www.googleapis.com/auth/gmail.send` y agrega como **usuarios de prueba** tu correo y el de tu mamá (hasta 100). No necesitas verificar la app; solo verás un aviso de "app no verificada": pulsa *Avanzado → Continuar*.
   - Credenciales → ID de cliente OAuth → Aplicación web. En *URI de redirección autorizados* pon `https://TU_REF.supabase.co/auth/v1/callback`. Copia Client ID y Client Secret.
4. **Supabase → Authentication → Sign In / Providers → Google:** actívalo y pega Client ID y Secret. En *URL Configuration* pon `http://localhost:8080` (o la URL donde publiques) como *Site URL* y en *Redirect URLs*.
5. **Función de invitaciones** (necesitas Node):
```
npm i -g supabase
supabase login
supabase link --project-ref TU_REF
supabase secrets set APP_URL=http://localhost:8080/
supabase functions deploy invite
```
`APP_URL` debe ser la URL exacta donde abres la app, con `/` al final.
6. **Conectar:** edita `config.js` con tu Project URL y la clave anon (nunca la `service_role`).
7. **Publicar (opcional):** sube la carpeta sin `supabase/` a Netlify, Cloudflare Pages o Vercel, y actualiza la URL en el paso 4 y en `APP_URL`.

## Cómo funciona
- Se entra solo con Google. La primera vez pide permiso para enviar correo.
- Al invitar a alguien a un calendario compartido, el correo sale **desde tu Gmail** (aparece en *Enviados*).
- El permiso de Gmail dura cerca de 1 hora; si vence, la app pide volver a entrar con Google.
- La persona invitada debe entrar con Google usando **ese mismo correo** y pulsar el enlace.

## Para probar que todo anda
- Entra con dos cuentas de Google; cada una ve solo su calendario personal.
- Crea un calendario compartido e invita a la otra cuenta como *Lector*; acepta desde el enlace del correo y comprueba que no puede editar.
- Cámbiale el rol a *Editor* y mira que los cambios aparezcan en ambas sesiones.
- Mueve y copia eventos entre el calendario personal y el compartido.
- Retira al miembro: pierde el acceso al instante.

Esta versión no se ha probado contra un proyecto real de Supabase, así que haz estas pruebas una vez.
