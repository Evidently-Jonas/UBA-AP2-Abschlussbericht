# 02 – Anforderungen und Ziele

## 2.1 Funktionale Anforderungen

### FA-01: Dashboard-Erstellung und -Bearbeitung

| ID | Anforderung | Umsetzung |
|----|-------------|-----------|
| FA-01.1 | Visueller Drag-and-Drop-Editor auf 12-Spalten-Grid | `DashboardGridEdit`, react-grid-layout |
| FA-01.2 | Mehrseitige Dashboards mit Hero-Bereich pro Seite | `DashboardStore`, Page-Modell |
| FA-01.3 | Undo/Redo für Bearbeitungsschritte | `UndoRedoStore`, `EditorActionService` |
| FA-01.4 | Speichern und Laden von Dashboard-Definitionen | `PUT /api/dashboards/:id`, MongoDB |
| FA-01.5 | Duplizieren von Dashboards | `POST /api/dashboards/:id/duplicate` |
| FA-01.6 | Veröffentlichung (Publish/Unpublish) | `PUT /api/dashboards/:id/publish` |

### FA-02: Datenanbindung (SDMX)

| ID | Anforderung | Umsetzung |
|----|-------------|-----------|
| FA-02.1 | Auswahl von UBA-Dataflows im Editor | `DataflowSelectPropertyInput` |
| FA-02.2 | Abruf von Zeitreihen und KPI-Werten | `UbaSdmxService`, `DataFetcher` |
| FA-02.3 | Unterstützung Release- und Design-Space | Query-Parameter `?space=` |
| FA-02.4 | Zeitraumfilter (startPeriod/endPeriod) | `YearPicker`, `ProjectionFilters` |
| FA-02.5 | Hierarchische Codelists (Parent-Codes) | `enrichDataWithCodeParents` in `sdmx-parsers.ts` |

### FA-03: Interaktive Visualisierung

| ID | Anforderung | Umsetzung |
|----|-------------|-----------|
| FA-03.1 | Balkendiagramme mit Selektion | `BarChart`, Plotly |
| FA-03.2 | Donut-Diagramme | `DonutChart` |
| FA-03.3 | KPI-Kacheln (Zahl, Text, Sparkline) | `KpiTile` |
| FA-03.4 | Dashboardweite Filter via Chart-Selektion | `FilterStore`, `ProjectionSelection` |
| FA-03.5 | Lokale Filter pro Tile | `ProjectionFilters.dimensionFilters` |
| FA-03.6 | Legende mit Chart-Verknüpfung | `Legend`, `IChartLegendSource` |

### FA-04: Inhaltliche Elemente

| ID | Anforderung | Umsetzung |
|----|-------------|-----------|
| FA-04.1 | Intro-, Text- und Link-Kacheln | `Intro`, `TextTile`, `LinkTile` |
| FA-04.2 | Buttons mit konfigurierbaren Aktionen | `ButtonTile`, `TileClickActionsInput` |
| FA-04.3 | Bild-Upload für Hero und Tiles | GridFS, `ImageService` |
| FA-04.4 | Mehrsprachige Texteingaben | `MultilingualInput`, `MultilingualText` |

### FA-05: Verwaltung und Governance

| ID | Anforderung | Umsetzung |
|----|-------------|-----------|
| FA-05.1 | Rollenbasierte Authentifizierung | Session-Auth, 4 Rollen |
| FA-05.2 | Benutzerverwaltung (Admin) | `/user-management` |
| FA-05.3 | Zentrale Tag-/Badge-Verwaltung | `Tag`-Modell, `/tag-management` |
| FA-05.4 | CTA-Kacheln in öffentlicher Galerie | `CTAItem`, `/cta-management` |
| FA-05.5 | Dataflow-Nutzungsbericht | `/dataflow-usage` |
| FA-05.6 | Dashboard-Eigentümerschaft | `userId`-Feld, Admin-Zuweisung |

### FA-06: Öffentliche Präsentation

| ID | Anforderung | Umsetzung |
|----|-------------|-----------|
| FA-06.1 | Öffentliche Dashboard-Galerie mit Suche/Filter | `PublicDashboards` |
| FA-06.2 | Nur veröffentlichte Dashboards für Public | `GET /api/dashboards/published` |
| FA-06.3 | Sprachpräfix in URLs (`/de/`, `/en/`) | `LanguageRouting` |
| FA-06.4 | Rechtliche Seiten (Impressum, Datenschutz, Barrierefreiheit) | Statische Pages |

---

## 2.2 Nicht-funktionale Anforderungen

### NFA-01: Sicherheit

| ID | Anforderung | Umsetzung |
|----|-------------|-----------|
| NFA-01.1 | Schutz vor CSRF | Double-Submit-Cookie-Pattern |
| NFA-01.2 | Schutz vor Brute-Force | Rate Limiting auf Login |
| NFA-01.3 | Schutz vor NoSQL-Injection | `isPlainString`-Validierung |
| NFA-01.4 | Sichere Passwortspeicherung | Bcrypt, 12 Rounds |
| NFA-01.5 | Session-Invalidierung bei Passwortwechsel | `passwordChangedAt` |
| NFA-01.6 | Kein SVG-Upload (XSS-Vermeidung) | MIME-Whitelist, sharp Re-Encode |
| NFA-01.7 | Schema-Validierung von Dashboard-Inhalten | `validatePages`, Whitelist |

### NFA-02: Performance

| ID | Anforderung | Umsetzung |
|----|-------------|-----------|
| NFA-02.1 | SDMX-Key-Caching (24h TTL) | `UbaSdmxService.keyCache` |
| NFA-02.2 | Struktur-Caching (DSD/Codelists) | `structureCache` |
| NFA-02.3 | Lazy Loading des Editors | `React.lazy(DashboardView)` |
| NFA-02.4 | Timeout für Upstream-Requests (15s) | `timedFetch` |
| NFA-02.5 | Getrennte Editor/App-Bundles | Code-Splitting |

### NFA-03: Wartbarkeit und Erweiterbarkeit

| ID | Anforderung | Umsetzung |
|----|-------------|-----------|
| NFA-03.1 | Plugin-artige Tile-Registry | `ElementRegistry` |
| NFA-03.2 | Dokumentierte Entwickler-Guides | `docs/new-tile-guide.md` |
| NFA-03.3 | Getrennte Dev-/User-Dokumentation | `docs/` vs. `user-docs/` |
| NFA-03.4 | TypeScript durchgängig | Frontend + Backend |

### NFA-04: Barrierefreiheit

| ID | Anforderung | Umsetzung |
|----|-------------|-----------|
| NFA-04.1 | WCAG-orientierte Tests | jest-axe, ~77 a11y-Tests |
| NFA-04.2 | Skip-Links, Landmarks, ARIA-Labels | `UbaHeader`, Komponenten |
| NFA-04.3 | Tastaturkürzel im Editor | `KeyboardControlsService` |
| NFA-04.4 | Barrierefreiheitserklärung | `/accessibility` |

### NFA-05: Internationalisierung

| ID | Anforderung | Umsetzung |
|----|-------------|-----------|
| NFA-05.1 | UI in DE/EN | react-i18next |
| NFA-05.2 | Dashboard-Inhalte in DE/EN | `MultilingualText` in MongoDB |
| NFA-05.3 | Erweiterbarkeit für weitere Sprachen | `getLocalizedText`-Fallback |

### NFA-06: Betrieb

| ID | Anforderung | Umsetzung |
|----|-------------|-----------|
| NFA-06.1 | Containerisierung | Docker Compose (dev/prod) |
| NFA-06.2 | CI/CD | GitHub Actions → Azure |
| NFA-06.3 | Datenbank-Backup | `ops/mongo-backup`, 7-Tage-Retention |
| NFA-06.4 | Health-Checks | `/health`, `/api/uba-sdmx/health` |
| NFA-06.5 | Konfigurierbare API-/Docs-URLs | Runtime-Config via `config.js` |

---

## 2.3 Qualitätsziele

| Ziel | Metrik / Indikator | Ist-Stand |
|------|-------------------|-----------|
| Testabdeckung Frontend | Anzahl Tests | ~103 (Vitest) |
| Testabdeckung Backend | Jest Unit + Integration | Routes, Services, Middleware |
| Accessibility | axe-Verstöße in getesteten Komponenten | Systematisch geprüft, gezielte Regel-Ausnahmen |
| Sicherheit Production | Checklist erfüllt | Dokumentiert in `docs/deployment.md` |
| Dokumentation | Dev + User Docs | Vollständig, zweisprachig (User) |

---

## 2.4 Erfüllungsgrad und offene Punkte

**Vollständig umgesetzt:**
- Kern-Editor mit allen 10 Tile-Typen
- SDMX-Integration mit Caching
- Rollenbasierte Auth mit Security-Hardening
- Mehrsprachigkeit UI + Inhalte
- Azure-Deployment-Pipeline

**Teilweise / mit Einschränkungen:**
- E2E-Tests fehlen (nur Unit + a11y)
- Editor-Grid/Sidebar nicht in a11y-Tests
- Kein automatisiertes Performance-Monitoring
- Keine formale WCAG-Zertifizierung, nur axe-basierte Prüfung

**Nicht umgesetzt (bewusst oder perspektivisch):**
- SSO/OIDC-Integration
- Versionierung/Revision History von Dashboards
- Echtzeit-Kollaboration im Editor
- Offline-Fähigkeit

Details zu Verbesserungen: Kapitel 14.
