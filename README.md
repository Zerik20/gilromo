# Cumbre Sinergética — Sitio de Gil Romo (Astro)

Landing y páginas de la **Cumbre Sinergética (Mérida, 15 de noviembre)** construidas con **Astro + TailwindCSS**, animaciones con **AOS**, formulario de **leads** conectado a **Google Apps Script**, carrusel de **aliados comerciales** y **Pixel de Facebook** (PageView global y Lead al enviar el formulario).

---

## 🚀 Tech stack

- [Astro](https://astro.build/) (sitio estático)
- [Tailwind CSS](https://tailwindcss.com/)
- [AOS](https://michalsnik.github.io/aos/) (animaciones on-scroll)
- Scripts del DOM en **JS plano** (evita errores TS8016 dentro de `.astro`)
- Integraciones:
  - **Google Apps Script** (registro de leads en Google Sheets)
  - **Meta/Facebook Pixel** (PageView global, Lead al registrar)
  - **Google Maps Embed** (sede del evento)

---

## 📦 Requisitos

- **Node.js 18+** (LTS recomendado)
- Gestor de paquetes: `pnpm` (recomendado), o `npm` / `yarn`

---

## 🛠️ Inicio rápido

```bash
# 1) Clonar
git clone <URL-de-tu-repo> cumbre-sinergética
cd cumbre-sinergética

# 2) Instalar dependencias
pnpm install
# o npm install / yarn

# 3) Desarrollo
pnpm dev
# Abre http://localhost:4321

# 4) Build de producción
pnpm build

# 5) Vista previa del build
pnpm preview
```
> El build genera `dist/`, lista para subir a **Vercel**, **Netlify** o cualquier hosting estático.

---

## 📂 Estructura del proyecto

```text
/
├─ public/
│  ├─ favicon.svg
│  └─ img/patrocinadores/      # logos del carrusel
├─ src/
│  ├─ components/
│  │  ├─ Navbar.astro
│  │  ├─ Footer.astro
│  │  ├─ VideoEmbed.astro
│  │  ├─ VIPAllInOne.astro     # tarjetas de planes (General/Platino/VIP)
│  │  └─ SponsorStrip.astro    # carrusel infinito de aliados comerciales
│  ├─ layouts/
│  │  └─ SiteLayout.astro      # layout con Pixel PageView y AOS
│  ├─ pages/
│  │  ├─ index.astro           # landing principal
│  │  └─ gracias.astro         # página de confirmación
│  └─ styles/
│     └─ global.css            # Tailwind y estilos globales
└─ package.json
```

---

## 🔐 Variables & configuración (opcional)

Crea un `.env` para evitar hardcodear valores:

```env
PUBLIC_APPSCRIPT_URL="https://script.google.com/macros/s/…/exec"
PUBLIC_FB_PIXEL_ID="2178875679294428"
```

En los componentes/layouts puedes leerlos con `import.meta.env.PUBLIC_…`.

---

## 🧾 Formulario de leads (Google Sheets)

**Flujo**
1. El usuario envía **nombre**, **correo**, **teléfono**, **edad**.
2. Se envían también campos ocultos:
   - `estatus`: `"Sin contactar"`
   - `estatus_color`: `#F59E0B` (ámbar)
   - `empresa`: `"Gil Romo Team"` (asignada automáticamente en el front)
   - `lead_name`: `"Cumbre Mérida 15 Nov Gil Romo"` (por defecto)
3. Si la respuesta del endpoint es `OK`:
   - Se dispara `fbq('track', 'Lead')` (si el pixel está disponible).
   - Redirección a `/gracias`.

**Apps Script — orden de columnas esperado:**

```text
Fecha | Nombre | Correo | Teléfono | Edad | Empresa | Estatus | Nombre del lead
```

> Si cambias el ID de la hoja, actualiza `SpreadsheetApp.openById(...)` en tu Apps Script.

---

## 📸 Pixel de Facebook

- **PageView global**: incluido en el layout principal (`SiteLayout.astro`).
- **Lead**: se dispara **solo** cuando el formulario se envía correctamente.

Para cambiar el **Pixel ID**, actualiza el valor en el layout o usa `PUBLIC_FB_PIXEL_ID`.

Ejemplo (PageView en `<head>` del layout):

```html
<!-- Meta Pixel Code (PageView) -->
<script>
!function(f,b,e,v,n,t,s){
 if(f.fbq)return;n=f.fbq=function(){n.callMethod?
 n.callMethod.apply(n,arguments):n.queue.push(arguments)};
 if(!f._fbq)f._fbq=n;n.push=n;n.loaded=!0;n.version='2.0';
 n.queue=[];t=b.createElement(e);t.async=!0;
 t.src=v;s=b.getElementsByTagName(e)[0];
 s.parentNode.insertBefore(t,s)}(window, document,'script',
 'https://connect.facebook.net/en_US/fbevents.js');
fbq('init', '2178875679294428'); // <-- tu Pixel ID
fbq('track', 'PageView');
</script>
<noscript><img height="1" width="1" style="display:none"
src="https://www.facebook.com/tr?id=2178875679294428&ev=PageView&noscript=1"/></noscript>
<!-- End Meta Pixel Code -->
```

Y en el envío exitoso del formulario (Lead):

```js
if (typeof window !== 'undefined' && typeof window.fbq === 'function') {
  try { window.fbq('track', 'Lead'); } catch {}
}
```

---

## 🎬 Video & WhatsApp

- **Video**: los bloques `<VideoEmbed />` y scripts asociados pueden estar **comentados**. Para reactivarlo, descomenta el bloque de video y su JS.
- **WhatsApp (grupo)**: el **modal obligatorio** quedó **comentado**. En su lugar se dejó un **texto + botón** “Unirme al grupo” que abre WhatsApp en **nueva pestaña**.

---

## 🤝 Aliados comerciales (carrusel infinito)

- `SponsorStrip.astro`: carrusel tipo **marquee** infinito (duplica logos para bucle continuo).
- Imágenes en `public/img/patrocinadores/`.
- Ajustes:
  - Tamaño del logo: clases utilitarias (`h-…`, `max-w-…`).
  - Velocidad/animación: keyframes/duración en CSS del componente.

Para agregar/quitar logos:

```ts
const logos = [
  { src: '/img/patrocinadores/Patrocinador1.png', alt: 'Patrocinador 1' },
  // …
];
```

---

## 🧩 Secciones clave

- **Navbar**: enlaces de ancla `#por-que`, `#ponentes`, `#programa`, `#contacto`.
  - Si navegas desde otra página (p. ej. `/gracias`), se normaliza a rutas absolutas `/#por-que`, etc.
- **Sección “¿Por qué?”**: tarjetas con bullets.
- **Planes**: `VIPAllInOne.astro` reusado para **General / Platino / VIP** (precios, badges, CTA).
- **Gracias**: confirmación, CTA a pasarela, botón al **grupo de WhatsApp**, mensaje para **código de descuento**.
- **Footer**: redes sociales (Instagram @grconexion, @gil.romo.vargas, Facebook, TikTok, YouTube, Spotify, WhatsApp).

---

## 🧪 Checklist de QA

- [ ] Actualizar `siteUrl` e imagen OG en el layout (SEO/OG/Twitter).
- [ ] Probar el formulario con el **endpoint real** de Apps Script.
- [ ] Verificar que el **Pixel PageView** carga en producción.
- [ ] Confirmar evento **Lead** tras envío exitoso del formulario.
- [ ] Revisar logos en móviles (que no se encimen) y en desktop (alineados).
- [ ] Comprobar accesibilidad básica (alt de imágenes, foco, contraste).
- [ ] Activar el **Video** cuando tengas el archivo final.

---

## 🚢 Deploy

Cualquier hosting estático funciona.

### Vercel

```bash
# Build
pnpm build

# Deploy con la CLI (opcional)
vercel deploy --prod
```

### Netlify

- **Build command**: `pnpm build`
- **Publish directory**: `dist/`

---

## 📝 Licencia

**Privado / Propietario** — © 2025 **Gil Romo Team**.  
No redistribuir sin autorización.

---

## 👥 Créditos

- Dirección: **Gil Romo Team**
- Desarrollo & soporte: **Cumbre Sinergética Web**
- Contacto: [WhatsApp](https://wa.me/524476701434) · [Email](mailto:gil.romo.vargas@gmail.com)

---

## 💡 Tips de mantenimiento

- Mantén **AOS** inicializado una sola vez (ya está en el layout).
- Evita `as HTMLElement` dentro de scripts en `.astro` para no generar el error **TS8016**; usa **JS plano** con checks null-safe.
- Para navegación entre páginas internas (e.g. `/` ↔ `/gracias`), usa rutas absolutas (`/`) y anclas (`/#por-que`) para volver a la **home** sin problemas.
