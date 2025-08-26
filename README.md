Cumbre Sinergética — Sitio de Gil Romo (Astro)

Landing y páginas de la Cumbre Sinergética (Mérida, 15 de noviembre) construidas con Astro + TailwindCSS, animaciones con AOS, formulario de leads conectado a Google Apps Script, carrusel de aliados comerciales y Pixel de Facebook (PageView global y Lead en el submit del formulario).

🚀 Tech stack

Astro
 (static site)

Tailwind CSS

AOS
 (animaciones on-scroll)

TypeScript opcional en componentes .astro (scripts del DOM en JS plano)

Integraciones:

Google Apps Script (registro de leads en Google Sheets)

Meta/Facebook Pixel (PageView global, Lead en formulario)

Google Maps Embed (sede del evento)

📦 Requisitos

Node.js 18+ (recomendado LTS)

Gestor de paquetes: pnpm (recomendado), npm o yarn

🛠️ Inicio rápido
# 1) Clonar
git clone <URL-de-tu-repo> cumbre-sinergética
cd cumbre-sinergética

# 2) Instalar dependencias
pnpm install
# o npm install / yarn

# 3) Modo desarrollo
pnpm dev
# Abre http://localhost:4321

# 4) Build de producción
pnpm build

# 5) Vista previa del build
pnpm preview


El build genera la carpeta dist/, lista para subir a Vercel, Netlify o cualquier hosting estático.

📂 Estructura sugerida
/
├─ public/
│  ├─ favicon.svg
│  └─ img/patrocinadores/   # logos del carrusel
├─ src/
│  ├─ components/
│  │  ├─ Navbar.astro
│  │  ├─ Footer.astro
│  │  ├─ VideoEmbed.astro
│  │  ├─ VIPAllInOne.astro        # tarjeta de planes (VIP/Platino/General)
│  │  └─ SponsorStrip.astro       # carrusel infinito de aliados comerciales
│  ├─ layouts/
│  │  └─ SiteLayout.astro         # layout con Pixel (PageView) y AOS
│  ├─ pages/
│  │  ├─ index.astro              # landing principal
│  │  └─ gracias.astro            # página de confirmación
│  └─ styles/
│     └─ global.css               # Tailwind y estilos globales
└─ package.json

🔐 Variables & Config (recomendado)

Aunque hay valores “hardcodeados” que funcionan, se recomienda moverlos a variables:

Crea .env (opcional):

PUBLIC_APPSCRIPT_URL="https://script.google.com/macros/s/…/exec"
PUBLIC_FB_PIXEL_ID="2178875679294428"


Y en los componentes/layouts, usa import.meta.env.PUBLIC_… para leerlos.

🧾 Formulario de leads (Google Sheets)

Endpoint: Google Apps Script (método POST), guarda registros en tu Hoja de Cálculo.

Campos enviados: nombre, correo, telefono, edad, estatus (hidden), estatus_color (hidden),
empresa (hidden, default “Gil Romo Team”), lead_name (hidden, default “Cumbre Mérida 15 Nov Gil Romo”).

Tras enviar correctamente:

Dispara fbq('track', 'Lead') si está disponible.

Redirige a /gracias.

Apps Script (resumen de columnas):

Fecha | Nombre | Correo | Teléfono | Edad | Empresa | Estatus | Nombre del lead


Si cambias el ID de la hoja, actualiza el openById(...) en el Apps Script.

📸 Pixel de Facebook

PageView: incluido en el layout principal (por ejemplo src/layouts/MainLayout.astro o SiteLayout.astro).

Lead: el componente de contacto dispara fbq('track', 'Lead') al enviar el formulario con éxito.

Para cambiar el Pixel ID, actualiza el valor en el layout o usa PUBLIC_FB_PIXEL_ID.

🎬 Video & Modal de WhatsApp

Video: en secciones donde aparece, está comentado según tu instrucción. Para reactivarlo, descomenta el bloque <VideoEmbed /> y el script asociado.

Modal obligatorio de WhatsApp: fue comentado; ahora solo se muestra el botón y texto para unirte al grupo directamente (sin bloquear la navegación).

🤝 Aliados comerciales (carrusel infinito)

Componente SponsorStrip.astro (o equivalente): carrusel tipo marquee infinito.

Logos en public/img/patrocinadores/.

Ajusta el tamaño y la velocidad en el CSS del componente.

Para agregar/quitar logos:

const logos = [
  { src: '/img/patrocinadores/Patrocinador1.png', alt: 'Patrocinador 1' },
  // …
];

🧩 Secciones clave

Navbar: enlaces con hash #por-que, #ponentes, #programa, #contacto (manejo de scroll y resaltado).

Por qué: tarjetas con bullets.

Planes: componente VIPAllInOne.astro para General / Platino / VIP (precios, badges, CTA).

Gracias: confirmación, CTA a pasarela, botón al grupo de WhatsApp, mensaje para código de descuento.

Footer: enlaces a redes (Instagram, Facebook, TikTok, YouTube, Spotify, WhatsApp).

Instagram: @grconexion y/o @gil.romo.vargas (puedes mostrar ambos si lo deseas).

Edita las URLs en Footer.astro.

🧪 QA checklist

 Cambiar siteUrl e imagen OG en el layout (SEO/OG/Twitter).

 Probar el formulario con el endpoint real de Apps Script.

 Confirmar que el Pixel PageView carga en producción.

 Confirmar Lead al enviar formulario exitoso.

 Revisar logos en móviles (no se enciman) y en desktop (alineados).

 Verificar accesibilidad básica (alt de imágenes, foco visible, contraste).

 Activar el bloque de Video cuando tengas el archivo final.

🚢 Deploy

Cualquier hosting estático funciona:

Vercel
# Build
pnpm build
# Deploy (si usas la CLI)
vercel deploy --prod

Netlify

Build command: pnpm build

Publish directory: dist/

📝 Licencia

Privado / Propietario — © 2025 Gil Romo Team.
No redistribuir sin autorización.

👥 Créditos

Dirección: Gil Romo Team

Desarrollo & soporte: Cumbre Sinergética Web

Contacto: WhatsApp
 · Email

💡 Tips de mantenimiento

Mantén AOS inicializado sólo una vez (ya está en el layout).

Evita as HTMLElement en scripts dentro de .astro para no generar errores TS8016; usa JS plano y checks null-safe.

Si navegas entre páginas internas (e.g. / ↔ /gracias), usa rutas absolutas (/) para volver a la home; en el navbar, si estás en otra página, redirige a /#por-que, etc.
