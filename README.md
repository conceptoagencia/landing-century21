# Century 21 Liberty Home — Landing

Proyecto estático (HTML/CSS/JS) con Tailwind compilado en local. Incluye formulario conectado a Make y un video local `landing.mp4` que está ignorado en Git.

## Estructura
- `index.html`: Landing principal (vídeo, sliders, modal con formulario)
- `gracias.html`: Página de agradecimiento tras envío correcto del formulario
- `email_template.html`, `email_template2.html`: Plantillas de email
- `tailwind.css`: CSS compilado y minificado que consume la landing en producción
- `img/`: Activos de imágenes (webp/png)
- `styles/tailwind.input.css`: Entrada de Tailwind
- `tailwind.config.js`: Tokens y theme extend de Tailwind
- `package.json`: Scripts de compilación CSS
- `.gitignore`: Excluye `landing.mp4` y formatos de vídeo para mantener el repo liviano

## Desarrollo local
- Abrir `index.html` en el navegador.
- Instalar dependencias con `npm install`.
- Generar CSS con `npm run build:css`.
- Si vas a iterar estilos, usar `npm run watch:css`.
- El formulario envía a Make (webhook en el propio `index.html`) y redirige a `gracias.html` en caso de éxito.

## Tracking de campañas (UTM/Meta)
- El formulario en `index.html` envía al webhook todos los datos planos del formulario, incluyendo 6 campos ocultos para CRM.
- Los 6 campos ocultos son: `lead_id`, `campaign_id`, `ad_id`, `created_time`, `nombre_campana`, `nombre_anuncio`.
- Se autocompletan desde URL (o `localStorage` si ya venían en una visita anterior):
  - `lead_id` ← `lead_id`
   - `campaign_id` ← `campaign_id` o `utm_id`
   - `ad_id` ← `ad_id`
   - `created_time` ← `created_time` (fallback: timestamp actual ISO)
  - `nombre_campana` ← `nombre_campana` o `utm_campaign` o `campaign_name`
  - `nombre_anuncio` ← `nombre_anuncio` o `utm_content` o `ad_name`

## Vídeo (`landing.mp4`)
- Está ignorado por Git para no inflar el repositorio.
- Para producción, súbelo a tu hosting/CDN y cambia `src` del `<video id="hero-video">` por su URL pública.

## Compartir con otro desarrollador
Tienes dos caminos recomendados:

### A) Rama + Pull Request en el remoto actual
1. Crear rama de trabajo y pushear:
   ```bash
   git checkout -b feat/sin-videos
   git push -u origin feat/sin-videos
   ```
2. Crear Pull Request hacia `main` en GitHub.

### B) Nuevo repositorio bajo tu cuenta
1. Crear repo vacío en tu cuenta (sin README/Licencia).
2. Cambiar el remoto y subir `main`:
   ```bash
   git remote set-url origin https://github.com/<tu-usuario>/<nuevo-repo>.git
   git push -u origin main
   ```
3. Añadir colaborador (`Settings > Collaborators > Add people`) con el usuario `TobiasArmiento` o el email `tobiasarmiento555@gmail.com`.

## Despliegue automatizado (GitHub Actions)
Al hacer merge a `main` se dispara un workflow que sube el sitio por SFTP al servidor de producción.

Workflow: `.github/workflows/deploy.yml`.

Configura los siguientes Secrets en GitHub (`Settings > Secrets and variables > Actions > New repository secret`):
- `SFTP_HOST` → access-5020106982.webspace-host.com
- `SFTP_PORT` → 22
- `SFTP_USER` → su714186
- `SFTP_PASS` → (la contraseña; no la subas al repo)
- `SFTP_REMOTE_PATH` → public/lp

Notas:
- El despliegue excluye `.git`, `.github`, `node_modules`, `trash`, `.vscode` y `*.mp4`.
- Si necesitas desplegar el vídeo, súbelo manualmente por SFTP o usa un CDN y cambia la `src` del `<video>`.

## Notas
- El historial se limpió localmente del último commit y se añadió `.gitignore` para excluir vídeos.
- Si necesitas versionar videos, usa Git LFS en lugar de ignorarlos.

## LFS (opcional)
```bash
git lfs install
git lfs track "*.mp4" "*.mov"
git add .gitattributes
git commit -m "track videos via LFS"
# Migración del historial (avanzado)
# git lfs migrate import --include="*.mp4,*.mov" --everything
```