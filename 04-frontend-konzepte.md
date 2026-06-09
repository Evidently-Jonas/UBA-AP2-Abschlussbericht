# 04 – Frontend-Konzepte

## 4.1 State Management mit MobX

Das Frontend nutzt **MobX 6** mit `makeObservable` für reaktiven State. Stores sind in zwei Hierarchien organisiert:

### RootStoreApp (App-weit)

Pfad: `frontend/src/stores/common/RootStoreApp.ts`

Enthält u. a.:
- `AuthStore` – eingeloggter Nutzer, Session-Status
- `LanguageStore` – aktuelle UI-Sprache
- `OverviewStore` – Dashboard-Liste für Autoren
- `PublicDashboardsStore` – öffentliche Galerie
- `TagStore`, `CTAStore`, `UserLookupStore`

### RootStoreEditor (Editor-spezifisch)

Pfad: `frontend/src/stores/editor/RootStore.ts`

Enthält u. a.:
- `DashboardStore` – aktuelles Dashboard, Seiten, Komponenten
- `FilterStore` – globale Dashboard-Filter
- `UndoRedoStore` – Undo/Redo-Stack
- `DataStore` / `DataflowStore` – geladene SDMX-Rohdaten
- `CodeListStore` – Codelist-Metadaten

**Designentscheidung:** Trennung verhindert, dass App-Pages Editor-State laden und umgekehrt. Reduziert Bundle-Größe und kognitive Komplexität.

## 4.2 Controller-Pattern

Controller kapseln Use-Cases und sind in `AppControllers` bzw. Editor-Controllern organisiert:

```typescript
// Vereinfachtes Muster
class OverviewController {
  async loadDashboards() {
    const data = await this.apiService.getDashboards();
    this.rootStoreApp.overviewStore.setDashboards(data);
  }
}
```

**Vorteile:**
- UI-Komponenten bleiben dünn (nur Render + Event-Handler)
- Testbarkeit: Controller mockbar ohne DOM
- Router-Loader können Controller direkt aufrufen

## 4.3 Routing und Data Loading

React Router 6 mit **Loaders** für initiales Datenladen:

- Sprachpräfix: `/:lang/*` (siehe `docs/language-routing.md`)
- Geschützte Routen: Route-Guards prüfen `AuthStore`
- Lazy Routes: `DashboardView` per `React.lazy`

Sprach-Routing-Utilities (`languageRoutes.ts`) werden unit-getestet (26 Tests in `utils.test.ts`).

## 4.4 Editor: Command-Pattern und Undo/Redo

Bearbeitungsaktionen sind **Command-Objekte** (`frontend/src/services/editor-actions/`):

| Action | Beschreibung |
|--------|--------------|
| `ActionAddElementToPage` | Tile zur Seite hinzufügen |
| `ActionRemoveElement` | Tile entfernen |
| `ActionUpdateLayout` | Grid-Position ändern |
| `ActionSetGlobalFilter` | Globalen Filter setzen |
| `ActionClearGlobalFilter` | Filter löschen |
| `ActionUpdateYearSelection` | Jahresbereich ändern |
| … | Weitere Property-Updates |

`EditorActionService` dispatcht Actions in `UndoRedoStore` mit:
- **Merge-Logik** für schnelles Tippen (z. B. Textfeld)
- **Omit-Logik** für redundante Zwischenschritte

## 4.5 DataFetcher: Reaktives Datenladen

`DataFetcher` (`frontend/src/services/DataFetcher.ts`) ist ein **MobX-Reaction-Service**:

1. Beobachtet alle `DataflowProjectionStore`-Instanzen im Dashboard
2. Sammelt benötigte `(dataflowId, space)`-Paare
3. Lädt fehlende Dataflows über `DataApiService`
4. Schreibt Ergebnisse in `DataflowStore`

**Vorteil:** Tiles müssen nicht selbst laden; zentrale Deduplizierung und Ladezustandsverwaltung.

## 4.6 UI-Framework: Mantine + styled-components

- **Mantine 7** – Formulare, Modals, Navigation, Layout
- **styled-components** – komponentenspezifische Styles (Legacy/Kompatibilität)
- **CSS Modules** – einige Seiten (z. B. `PageHero.module.css`)
- **@fontsource** – Roboto, Montserrat (Self-Hosted Fonts)

## 4.7 Grid-Layout

`react-grid-layout` für den 12-Spalten-Desktop-Grid:

- Drag-and-Drop im Edit-Modus
- Responsive Breakpoints (Desktop/Mobile)
- `defaultSize` / `defaultMobileSize` aus Element-Definition
- `UnplacedElementsPanel` für Tiles außerhalb des Grids

## 4.8 Kontext-Provider-Hierarchie

```
AppContext (rootStoreApp, controllers)
  └── MantineProvider
      └── ModalsProvider
          └── RouterProvider
              └── [Editor-Routen]
                  └── EditorProvider (rootStoreEditor, editorServices)
```

Test-Utilities (`renderWithProviders`, `renderWithEditorProviders`) spiegeln diese Hierarchie.

## 4.9 Runtime-Konfiguration

Production-Frontend nutzt **Laufzeit-Config** statt nur Build-Time-Env:

- `frontend/config.template.js` → `/config.js` via `entrypoint.sh`
- Variablen: `APP_API_URL`, `APP_DOCS_BASE_URL`
- Ermöglicht: ein Image, mehrere Umgebungen

## 4.10 Frontend-Sicherheitsaspekte

- CSRF-Token wird bei Login geholt und bei mutierenden Requests mitgesendet
- `ReauthOverlay` bei Session-Ablauf
- Keine Secrets im Frontend-Bundle
- CSP in `staticwebapp.config.json` (Azure SWA)

## 4.11 Bekannte Einschränkungen

| Bereich | Einschränkung |
|---------|---------------|
| Editor-Tests | Grid/Sidebar nicht in a11y-Suite |
| E2E | Keine Playwright/Cypress-Tests im Frontend |
| SSR | Kein Server-Side Rendering (reine SPA) |
| Offline | Keine Service-Worker-/PWA-Unterstützung |
