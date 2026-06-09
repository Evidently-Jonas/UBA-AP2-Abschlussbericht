# 13 – Designentscheidungen und Abwägungen

## 13.1 Methodik

Dieses Kapitel dokumentiert **bewusste Architektur- und Technologieentscheidungen** mit Begründung und Trade-offs. Es eignet sich für die Diskussion in einem wissenschaftlichen Bericht.

## 13.2 Entscheidungen im Überblick

| ID | Entscheidung | Alternative | Gewählt weil |
|----|--------------|-------------|--------------|
| AD-01 | MobX statt Redux | Redux, Zustand, Context | Weniger Boilerplate, natürliche Reaktivität für Editor |
| AD-02 | MongoDB statt SQL | PostgreSQL, SQLite | Flexibles Schema für heterogene Tiles |
| AD-03 | Session-Auth statt JWT | JWT, OAuth | Einfache Invalidierung, Cookie-Sicherheit |
| AD-04 | Backend SDMX-Proxy | Direktzugriff Browser | Caching, Timeout, einheitliche API |
| AD-05 | Express-Monolith | Microservices | Geringere Komplexität für Teamgröße |
| AD-06 | Lazy Editor-Loading | Alles in einem Bundle | Kleinere Initial-Load für Public Pages |
| AD-07 | Tile-Registry | Hardcoded Switch | Erweiterbarkeit ohne Core-Änderung |
| AD-08 | Getrennte User-Docs | Embedded Help | Unabhängige Release-Zyklen |
| AD-09 | Plotly statt D3 direkt | D3, Chart.js | Selektion-API, weniger Custom-Code |
| AD-10 | Mantine statt MUI/Chakra | Material UI | Zugänglichkeit, moderne API |

---

## 13.3 Detaillierte Abwägungen

### AD-01: State Management (MobX)

**Entscheidung:** MobX mit Store+Controller-Pattern

**Pro:**
- Automatische Reaktivität (`computed`, `reaction`) ideal für DataFetcher und Filter
- Weniger Code als Redux (keine Actions/Reducers pro Feld)
- Gute TypeScript-Integration mit `makeObservable`

**Contra:**
- Weniger vorhersagbar als Redux (Mutationen überall möglich)
- Kleinere Community als Redux
- Debugging schwieriger ohne DevTools wie Redux

**Fazit:** Für einen Editor mit vielen interdependenten Stores ist MobX pragmatisch.

---

### AD-02: Datenbank (MongoDB)

**Entscheidung:** MongoDB mit flexiblem Dashboard-Schema

**Pro:**
- Dashboard-Komponenten sind heterogen (10 Tile-Typen, unterschiedliche properties)
- Keine komplexen JOINs nötig
- GridFS für Bilder nativ integriert
- Session-Store (connect-mongo) out-of-the-box

**Contra:**
- Keine referentielle Integrität auf DB-Ebene
- Schema-Validierung nur in Application Layer
- Aggregation-Pipeline komplexer als SQL für Reports

**Fazit:** Document-Store passt zum „Dashboard als JSON-Dokument"-Modell.

---

### AD-03: Authentifizierung (Sessions)

**Entscheidung:** Server-seitige Sessions mit HTTP-Only Cookies

**Pro:**
- Sofortige Invalidierung (Passwortwechsel, Admin-Reset)
- Kein Token-Refresh-Flow
- XSS-resistenter als localStorage

**Contra:**
- Nicht stateless → Sticky Sessions bei Skalierung
- Kein SSO/OIDC ohne Erweiterung
- Mobile/Native-Clients schwieriger

**Fazit:** Für eine Web-only-Behördenanwendung mit überschaubarer Nutzerzahl angemessen.

---

### AD-04: SDMX-Zugriff (Backend-Proxy)

**Entscheidung:** Backend als Proxy mit Cache

**Pro:**
- 24h-Cache reduziert Last auf daten.uba.de
- 15s-Timeout schützt Worker
- Einheitliches JSON-Format für Frontend
- Funktioniert auch bei CORS-Änderungen

**Contra:**
- Zusätzliche Latenz (ein Hop)
- Single Point of Failure
- Cache nicht distributed

**POC-Erkenntnis:** Browser-direkter Zugriff funktioniert (CORS offen), wurde dennoch nicht für Produktion gewählt.

---

### AD-05: Architektur (Monolith)

**Entscheidung:** Express-Monolith statt Microservices

**Pro:**
- Einfacheres Deployment (ein Container)
- Keine Service-Discovery, kein API-Gateway
- Ausreichend für aktuellen Funktionsumfang

**Contra:**
- SDMX-Proxy und Auth skalieren nicht unabhängig
- Größerer Blast-Radius bei Bugs

**Fazit:** YAGNI – Microservices wären Over-Engineering für diesen Scope.

---

### AD-06: Editor-Trennung (Lazy Loading)

**Entscheidung:** Editor als separater Chunk mit eigenem Store-Baum

**Pro:**
- Public Pages laden schneller (kein Editor-Code)
- Klare Code-Grenzen
- MobX-Stores nicht überladen

**Contra:**
- Zwei Store-Bäume zu verstehen
- EditorProvider nur auf bestimmten Routen

**Fazit:** Klare Trennung zwischen „Konsument" und „Produzent" der Dashboards.

---

### AD-07: Filter-Architektur (Projektions-Layer)

**Entscheidung:** Filter in separatem Layer, nicht in Tiles

**Pro:**
- Einheitliche Logik für alle Tile-Typen
- Brushing & Linking ohne Tile-Kopplung
- Testbar isoliert

**Contra:**
- Indirektion erschwert Debugging für neue Entwickler
- Globale Filter nicht persistiert

**Fazit:** Bewährtes Visual-Analytics-Muster, domänenspezifisch vereinfacht.

---

### AD-08: Dokumentation (Getrennte Container)

**Entscheidung:** user-docs als eigener Service

**Pro:**
- Unabhängige Updates (Doku ohne App-Deploy)
- Kleinere Frontend-Bundles
- PDF-Export ohne App-Build

**Contra:**
- Zusätzlicher Container zu betreiben
- URL-Konfiguration nötig (`APP_DOCS_BASE_URL`)

**Fazit:** Trennung von Operator- und Entwicklerdokumentation ist Best Practice.

---

## 13.4 Verworfene Alternativen

| Alternative | Grund der Verwerfung |
|-------------|---------------------|
| CSV/Excel-Upload | Datenintegrität – nur autoritative SDMX-Quelle |
| iframe-Einbettung Data Cube | Keine narrative Kontrolle, kein Branding |
| Server-Side Rendering | Komplexität, SPA reicht für Zielgruppe |
| GraphQL | REST ausreichend, weniger Tooling-Aufwand |
| SVG-Bildupload | XSS-Risiko |
| CSRF Feature-Flag | Security by default – immer aktiv |

---

## 13.5 Technische Schulden (bewusst akzeptiert)

| Schuld | Auswirkung | Akzeptiert weil |
|--------|------------|-----------------|
| In-Memory SDMX-Cache | Nicht shared bei Skalierung | Single-Instance-Deployment |
| Kein E2E-Tests | Regressionsrisiko | a11y + Unit als Minimum |
| Kein Audit-Log | Keine Änderungshistorie | Scope-Begrenzung |
| console.log statt strukturiertem Logging | Schwierige Analyse | Ausreichend für aktuelle Größe |
| Kein Auto-Save | Datenverlust bei Browser-Crash | Explizites Speichern = bewusste Aktion |
