# 03 – Systemarchitektur

## 3.1 Architekturüberblick

Die Anwendung folgt einer **klassischen Drei-Schichten-Architektur** mit klarer Trennung von Präsentation, Anwendungslogik und Persistenz:

```
┌─────────────────────────────────────────────────────────────────┐
│                        CLIENT (Browser)                          │
│  ┌───────────────────────────────────────────────────────────┐  │
│  │  React SPA (Vite)                                         │  │
│  │  ├── App Pages (Overview, Public, Admin)                  │  │
│  │  └── Editor (lazy-loaded)                                 │  │
│  │      MobX Stores → Controllers → Services → HTTP          │  │
│  └───────────────────────────────────────────────────────────┘  │
└────────────────────────────┬────────────────────────────────────┘
                             │ HTTPS, Cookies, CSRF-Header
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│                    BACKEND (Express/Node.js)                     │
│  Routes → Middleware (Auth, CSRF, Rate Limit) → Services        │
│  ├── DashboardService    ├── UbaSdmxService (Proxy+Cache)       │
│  ├── ImageService        └── Auth/User Management               │
└────────────┬───────────────────────────────┬────────────────────┘
             │                               │
             ▼                               ▼
┌────────────────────────┐    ┌──────────────────────────────────┐
│  MongoDB 7             │    │  UBA SDMX API (daten.uba.de)     │
│  ├── dashboards        │    │  release/ + design/ Space        │
│  ├── users, tags, cta  │    └──────────────────────────────────┘
│  ├── sessions          │
│  └── GridFS (images)   │
└────────────────────────┘
```

## 3.2 Monorepo-Struktur

| Verzeichnis | Verantwortung |
|-------------|---------------|
| `frontend/` | React-SPA, Editor, Stores, UI |
| `backend/` | REST-API, SDMX-Proxy, Persistenz |
| `user-docs/` | VitePress-Bedienerhandbuch (DE/EN) |
| `docs/` | Entwicklerdokumentation |
| `poc-sdmx-explorer/` | Isolierter SDMX-POC |
| `ops/mongo-backup/` | Backup-Container |
| `.github/workflows/` | CI/CD-Pipelines |

## 3.3 Frontend-Architektur

### Schichtenmodell (Store + Controller)

Das Frontend implementiert ein **strikt getrenntes Schichtenmodell** (vgl. `docs/architecture.md`):

1. **UI (Pages/Components)** – liest Stores, rendert, triggert Controller
2. **Controller** – Use-Case-Orchestrierung (`loadDashboards`, `login`, …)
3. **Services** – HTTP-Kommunikation, keine UI-Abhängigkeiten
4. **Stores (MobX)** – Observable State, Mutationen

**Prinzip:** UI ist *read-only* bezüglich API-Logik; alle Seiteneffekte laufen über Controller.

### Zwei Store-Bäume

| Store-Baum | Scope | Beispiel-Stores |
|------------|-------|-----------------|
| `RootStoreApp` | App-weit | `AuthStore`, `OverviewStore`, `LanguageStore`, `TagStore` |
| `RootStoreEditor` | Nur Editor-Routen | `DashboardStore`, `FilterStore`, `UndoRedoStore`, `DataStore` |

Der Editor wird über `EditorProvider` (`ui/contexts/EditorContext.tsx`) nur auf `/edit/:id`, `/view/:id` und `/dataflow-usage` bereitgestellt.

### Lazy Loading

`DashboardView` wird per `React.lazy` geladen. Der Editor-Chunk (Stores, ActionService, Property-Panels, editor-components) wird erst bei Navigation zum Editor nachgeladen – andere Pages laden diese Abhängigkeiten nicht.

## 3.4 Backend-Architektur

### Express-Monolith

Einstiegspunkt: `backend/src/app.ts`

| Route-Prefix | Verantwortung |
|--------------|---------------|
| `/api/auth` | Login, Logout, User-CRUD, CSRF-Token |
| `/api/dashboards` | Dashboard-CRUD, Publish, Duplicate |
| `/api/images` | GridFS-Upload, Thumbnails |
| `/api/uba-sdmx` | SDMX-Proxy mit Caching |
| `/api/tags` | Tag-Verwaltung |
| `/api/cta-links` | CTA-Kacheln |
| `/health` | Health-Check |

### Middleware-Kette

```
Request → helmet() → cors() → session() → csrf() → rateLimiter → routeHandler
```

## 3.5 Datenfluss: Dashboard anzeigen

```
1. Nutzer navigiert zu /de/view/:dashboardId
2. Router-Loader → Controller.loadDashboard()
3. ApiService → GET /api/dashboards/:id
4. DashboardStore.deserialisiert JSON → Element-Stores
5. DataFetcher (MobX reaction) erkennt benötigte Dataflows
6. DataApiService → GET /api/uba-sdmx/dataflows/:id?space=release
7. UbaSdmxService → fetch daten.uba.de → parse SDMX → cache
8. DataflowProjectionStore wendet Filter an
9. Renderer (BarChart, KpiTile, …) zeigt gefilterte Daten
```

## 3.6 Datenfluss: Dashboard speichern

```
1. Nutzer klickt „Speichern" in Editor-Toolbar
2. Controller.saveDashboard()
3. DashboardStore.serialize() → JSON
4. ApiService → PUT /api/dashboards/:id (mit CSRF-Header)
5. Backend validatePages() → MongoDB upsert
```

## 3.7 Dokumentations-Architektur

Die Bedienerdokumentation ist **bewusst entkoppelt**:

- Eigener VitePress-Container (`user-docs`)
- Frontend verlinkt per konfigurierbarer URL (`APP_DOCS_BASE_URL`)
- In Production typischerweise eigene Subdomain (`docs.example.com`)

Vorteil: Unabhängige Release-Zyklen, kleinere Frontend-Bundles, klare Zuständigkeit.

## 3.8 Deployment-Architektur (Azure)

```
GitHub (dev branch)
    │
    ├── Frontend Workflow → Azure Static Web Apps
    │   (baut user-docs ein → public/docs)
    │
    ├── Backend Workflow → Azure Web App Service
    │   (npm build → zip deploy)
    │
    └── User-Docs Workflow → Artefakte (optional separat)
```

Alternativ: Self-Hosted via `compose.prod.yml` mit Registry-Images.

## 3.9 Architekturmuster (Zusammenfassung)

| Muster | Anwendung |
|--------|-----------|
| **SPA** | Single Page Application mit Client-Routing |
| **BFF-lite** | Backend als SDMX-Proxy und Session-Handler |
| **Repository** | Mongoose-Models kapseln DB-Zugriff |
| **Command** | Editor-Actions für Undo/Redo |
| **Registry/Plugin** | ElementRegistry für Tile-Typen |
| **Projection** | Filter-/Aggregations-Layer über Rohdaten |
| **Double-Submit CSRF** | Session + Cookie + Header |

## 3.10 Technologieentscheidungen (Kurzbegründung)

| Technologie | Begründung |
|-------------|------------|
| React + MobX | Bewährte SPA-Architektur, reaktive Stores für komplexen Editor-State |
| Express | Leichtgewichtig, ausreichend für REST-Monolith |
| MongoDB | Flexible Schema für Dashboard-Komponenten (heterogene Tiles) |
| Mantine | Zugängliche UI-Komponenten, konsistentes Design |
| Plotly | Interaktive Charts mit Selektion-API |
| Vite | Schnelle Dev-Experience, modernes Build-Tool |

Ausführliche Abwägungen: Kapitel 13.
