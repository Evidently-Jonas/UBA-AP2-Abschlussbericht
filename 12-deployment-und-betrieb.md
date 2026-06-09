# 12 – Deployment und Betrieb

## 12.1 Deployment-Optionen

| Modus | Zielgruppe | Mechanismus |
|-------|------------|-------------|
| **Azure (primär)** | UBA Production | Static Web Apps + Web App Service |
| **Docker Dev** | Entwickler | `compose.dev.yml` (Hot-Reload) |
| **Docker lokal prod-ähnlich** | QA, Demo | `docker-compose.yml` |
| **Docker Production** | Self-Hosted | `compose.prod.yml` (Registry-Images) |

## 12.2 Azure-Architektur

```
                    ┌─────────────────────────┐
                    │   GitHub Repository      │
                    │   (dev branch)           │
                    └───────────┬─────────────┘
                                │
            ┌───────────────────┼───────────────────┐
            ▼                   ▼                   ▼
   ┌────────────────┐  ┌────────────────┐  ┌────────────────┐
   │ Frontend CI    │  │ Backend CI     │  │ User-Docs CI   │
   │ → Azure SWA    │  │ → Azure Web App│  │ → Artefakte    │
   └────────────────┘  └────────────────┘  └────────────────┘
            │                   │
            ▼                   ▼
   ┌────────────────┐  ┌────────────────┐
   │ Static Web App │  │ Web App Service│
   │ (CDN, SPA)     │  │ UBA-Data-Cube- │
   │                │  │ Dashboards-    │
   │                │  │ Backend        │
   └────────────────┘  └────────┬───────┘
                                │
                                ▼
                       ┌────────────────┐
                       │ MongoDB        │
                       │ (Azure/extern) │
                       └────────────────┘
```

### Frontend-Workflow

Datei: `.github/workflows/azure-static-web-apps-red-river-0439aec03.yml`

1. Build user-docs
2. Embed in `frontend/public/docs`
3. Build Frontend mit `VITE_API_URL`
4. Deploy zu Azure Static Web Apps

Konfiguration: `frontend/staticwebapp.config.json` (SPA-Fallback, CSP)

### Backend-Workflow

Datei: `.github/workflows/dev_uba-data-cube-dashboards-backend.yml`

1. Trigger: Push to `dev` (nur `backend/**`)
2. npm install → build → test
3. Zip → Azure Web App deploy

## 12.3 Docker Compose Stacks

### Dev (`compose.dev.yml`)

| Service | Port | Beschreibung |
|---------|------|--------------|
| frontend | 5173 | Vite Dev Server |
| backend | 3001 | Nodemon |
| user-docs | 5174 | VitePress Hot-Reload |
| mongo | 27017 | Direkt erreichbar |

### Default (`docker-compose.yml`)

| Service | Port | Beschreibung |
|---------|------|--------------|
| frontend | 8080 | nginx, statisches Build |
| backend | 3001 | Node Production |
| user-docs | 8081 | nginx, VitePress |
| mongo | intern | Kein externer Port |

### Production (`compose.prod.yml`)

- Vorgefertigte Images aus Registry
- Kein Build im Deployment
- `SESSION_SECRET`, `ALLOWED_ORIGINS` Pflicht

## 12.4 Konfiguration

### Laufzeit vs. Build-Time

| Variable | Ebene | Service |
|----------|-------|---------|
| `VITE_API_URL` | Build-Time | Frontend |
| `APP_API_URL` | Laufzeit | Frontend (via config.js) |
| `VITE_DOCS_BASE_URL` | Build-Time | Frontend |
| `APP_DOCS_BASE_URL` | Laufzeit | Frontend |
| `SESSION_SECRET` | Laufzeit | Backend |
| `MONGODB_URI` | Laufzeit | Backend |
| `ALLOWED_ORIGINS` | Laufzeit | Backend |

**Vorteil Laufzeit-Config:** Ein Frontend-Image für mehrere Umgebungen.

### Vorlage

`env.docker.example` im Projekt-Root.

## 12.5 Backup und Recovery

### MongoDB-Backup

Pfad: `ops/mongo-backup/`

| Parameter | Wert |
|-----------|------|
| Zeitplan | Täglich 02:00 (Cron) |
| Format | `mongodump.gz` |
| Speicherort | Volume `backups` |
| Retention | 7 Tage (`RETENTION_DAYS`) |

### Restore

```bash
docker compose -f compose.prod.yml exec mongo bash -lc \
  "mongorestore --archive=/backups/YYYY-MM-DD/mongodump.gz --gzip --drop"
```

**Warnung:** `--drop` löscht bestehende Datenbanken.

## 12.6 Monitoring und Observability

| Aspekt | Aktueller Stand |
|--------|-----------------|
| Health-Checks | `/health`, `/api/uba-sdmx/health` |
| Logging | console.log (Backend), kein strukturiertes Logging |
| APM/Tracing | Nicht implementiert |
| Metriken | Nicht implementiert |
| Alerting | Nicht im Repo |
| Uptime-Monitoring | Extern (nicht dokumentiert) |

### Empfohlene Monitoring-Punkte

- HTTP 429 auf `/api/auth/*` (Brute-Force-Indikator)
- SDMX-Cache-Hit-Rate
- MongoDB-Disk-Space
- Backup-Erfolg
- Response-Zeiten Backend

## 12.7 Skalierung

| Komponente | Skalierung | Hinweis |
|------------|------------|---------|
| Frontend (SWA) | Automatisch (CDN) | Stateless |
| Backend | Horizontal möglich | Sticky Sessions nötig |
| MongoDB | Replica Set empfohlen | Nicht in Default-Compose |
| SDMX-Cache | Pro Instanz | Nicht shared → Redis empfohlen |

## 12.8 Initial-Setup (Production)

1. `.env` aus `env.docker.example` erstellen
2. Images bauen und in Registry pushen
3. `docker compose -f compose.prod.yml up -d`
4. Admin anlegen:
   ```bash
   docker compose exec -e ADMIN_PASSWORD='...' backend node scripts/create-admin.js
   ```
5. Production-Checklist abarbeiten (`docs/deployment.md`)

## 12.9 Release-Zyklus

- **Branch:** `dev` (CI-Trigger)
- **Frontend:** Automatisch bei Push
- **Backend:** Automatisch bei Push (backend/**)
- **User-Docs:** Artefakte bei Push; eingebettet in Frontend-Build

Kein dokumentiertes Staging/Production-Branching im Repo.
