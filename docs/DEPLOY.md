# Despliegue OTDR Analyzer (igual que NEXALAB)

Sitio estático: un HTML servido con nginx en Docker → Portainer en tu CPU.

```
Cursor → git push → GitHub Actions → webhook → Portainer rebuild
```

## 1. Repo en GitHub (una vez)

Desde esta carpeta:

```powershell
cd "C:\Users\Mauro\Desktop\OTDR Analyzer"
git init
git add .
git commit -m "Initial OTDR Analyzer deploy"
git branch -M main
gh repo create Advanced52/OTDR-Analyzer --private --source=. --remote=origin --push
```

Si no usas `gh`, crea el repo vacío en GitHub y:

```powershell
git remote add origin https://github.com/Advanced52/OTDR-Analyzer.git
git push -u origin main
```

## 2. Stack en Portainer (una vez)

1. **Stacks** → **Add stack**
2. Nombre: `otdr-analyzer`
3. Método: **Repository**
4. Repository URL: `https://github.com/Advanced52/OTDR-Analyzer.git`
5. Reference: `refs/heads/main`
6. Compose path: `docker-compose.prod.yml`
7. Si el repo es privado: autenticación GitHub (usuario + PAT)
8. Environment variables (opcional):
   - `OTDR_HTTP_PORT=8095`  (cambia el puerto si choca con otro servicio)
9. **Deploy the stack**

Abre `http://IP-DE-TU-CPU:8095` (o el puerto que elegiste).

## 3. Auto-update con webhook (como NEXALAB)

### Webhook en Portainer

1. Stacks → **otdr-analyzer**
2. Activa webhook **Pull and redeploy** / **GitOps update**
3. Copia la URL pública (Cloudflare), no localhost
4. **Re-pull image**: desactivado (las imágenes se construyen en el servidor)

### Secret en GitHub

1. https://github.com/Advanced52/OTDR-Analyzer/settings/secrets/actions
2. Secret: `PORTAINER_WEBHOOK_URL` = URL del webhook
3. Si usas Cloudflare Access (igual que NEXALAB): `CF_ACCESS_CLIENT_ID` y `CF_ACCESS_CLIENT_SECRET`

## Día a día

```powershell
cd "C:\Users\Mauro\Desktop\OTDR Analyzer"
# Si editas otdr_analyzer.html, cópialo a index.html antes:
Copy-Item otdr_analyzer.html index.html -Force
git add .
git commit -m "descripcion del cambio"
git push
```

Portainer se actualiza solo en 1–5 minutos.
