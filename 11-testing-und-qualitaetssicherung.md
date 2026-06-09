# 11 – Testing und Qualitätssicherung

## 11.1 Teststrategie (Überblick)

| Schicht | Framework | Umfang | Fokus |
|---------|-----------|--------|-------|
| Frontend Unit | Vitest | ~26 Tests | Utilities, i18n, Routing |
| Frontend a11y | Vitest + jest-axe | ~77 Tests | Barrierefreiheit, Komponenten |
| Backend Unit | Jest + supertest | Routes, Services, Middleware | API-Verträge, Auth |
| Backend Integration | Jest (separate Config) | SDMX Crosscheck, Live-Tests | Externe API |
| User-Docs | Playwright | Screenshots, PDF | Visuelle Regression |
| E2E | – | Nicht vorhanden | – |

## 11.2 Frontend-Tests

### Setup

- Runner: Vitest mit jsdom
- Setup: `frontend/src/test/setup.ts` (jest-dom, i18n, ResizeObserver-Mock, matchMedia-Mock)
- Utilities: `renderWithProviders`, `renderWithEditorProviders`, `createAppContext`

### Unit-Tests (`utils.test.ts`)

| Modul | Getestete Funktionen |
|-------|---------------------|
| `languageRoutes` | Spracherkennung, Pfad-Manipulation, Navigation |
| `getLocalizedText` | Fallback-Kette |
| `getLocalizedArray` | Array-Fallbacks |
| `getApiBaseUrl` | Runtime-Config, URL-Normalisierung |

### Accessibility-Tests (`a11y.test.tsx`)

- jest-axe für WCAG-orientierte Prüfung
- Mock von `DataApiService` (keine echten API-Calls)
- Auth-Mock via `mockAuthorUser`

**Nicht getestet (dokumentiert):**
- Vollständiger Editor mit Grid/Sidebar
- DashboardGridEdit, SideBar
- E2E-Workflows

### Befehle

```bash
cd frontend
npm run test              # Alle Tests
npm run test -- --run a11y.test   # Nur a11y
npm run test -- --watch   # Watch-Modus
```

## 11.3 Backend-Tests

### Unit-Tests

Pfad: `backend/src/__tests__/`

| Bereich | Dateien |
|---------|---------|
| App | `app.test.ts` |
| Auth | `routes/auth.test.ts` |
| Dashboards | `routes/dashboard.test.ts` |
| Tags | `routes/tags.test.ts` |
| Images | `routes/images.test.ts` |
| SDMX | `services/UbaSdmxService.test.ts` |
| Middleware | `middleware/auth.test.ts`, `csrf.test.ts` |

### Integrationstests

```bash
npm run test:integration   # Live SDMX-Tests
npm run test:crosscheck    # SDMX Crosscheck
```

Fixtures: `backend/src/__tests__/fixtures/sdmx/` (XML-Beispieldaten)

### CI-Integration

Backend-Workflow (`.github/workflows/dev_uba-data-cube-dashboards-backend.yml`):
- Trigger: Push to `dev` (backend/**)
- Steps: install → build → **test** → deploy

## 11.4 User-Docs-Tests

- Playwright für Screenshots (committed, nicht in CI regeneriert)
- PDF-Export via pdf-lib
- Screenshots in `user-docs/public/screenshots/`

## 11.5 Qualitätsmetriken

| Metrik | Wert | Bewertung |
|--------|------|-----------|
| Frontend-Tests gesamt | ~103 | Gut für a11y-Fokus |
| Backend Unit-Tests | Mehrere Suites | Solide für API |
| Code Coverage | Nicht konfiguriert/reportet | Lücke |
| E2E-Tests | 0 | Signifikante Lücke |
| Performance-Tests | 0 | Lücke |
| Security-Tests (SAST/DAST) | Nicht im Repo | Extern empfohlen |

## 11.6 Manuelle QA (implizit)

- Docker Compose Stacks für lokale Prod-ähnliche Tests
- Production-Checklist in `docs/deployment.md`
- Bedienerdokumentation als manuelles Test-Skript

## 11.7 Testlücken und Risiken

| Lücke | Risiko | Priorität |
|-------|--------|-----------|
| Kein E2E (Login → Edit → Save → View) | Regressions in Integration | Hoch |
| Editor-Grid ungetestet | Layout-Bugs | Mittel |
| Keine Coverage-Reports | Unbekannte blinde Flecken | Mittel |
| SDMX-Integration nur teilweise live | Upstream-Änderungen unbemerkt | Mittel |
| Keine Lasttests | Skalierungsgrenzen unbekannt | Niedrig-Mittel |

## 11.8 Empfohlene Test-Erweiterungen

1. **Playwright E2E** – kritische User Journeys
2. **Coverage-Thresholds** – z. B. 80% für Services/Utils
3. **SDMX-Contract-Tests** – Fixtures gegen Parser-Regression
4. **Visual Regression** – Storybook + Chromatic oder Percy
5. **Load Testing** – k6 oder Artillery auf SDMX-Proxy

## 11.9 Qualitätssicherung im Entwicklungsprozess

| Praxis | Status |
|--------|--------|
| TypeScript strict | ✅ Frontend + Backend |
| ESLint | ✅ (konfiguriert) |
| Pre-Commit Hooks | Nicht im Repo dokumentiert |
| PR-Reviews | Organisatorisch (nicht technisch erzwungen) |
| CI-Tests vor Deploy | ✅ Backend; Frontend-Tests nicht in SWA-Workflow |

**Hinweis:** Der Frontend-Azure-Workflow baut und deployed, führt aber keine Tests aus. Empfehlung: Tests in CI-Pipeline aufnehmen.
