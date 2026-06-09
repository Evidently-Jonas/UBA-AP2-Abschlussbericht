# Anhang A – Technologie-Stack (detailliert)

## Frontend (`frontend/package.json`)

| Paket | Version (ca.) | Zweck |
|-------|---------------|-------|
| react | 19.x | UI-Framework |
| react-dom | 19.x | DOM-Rendering |
| typescript | 5.8.x | Typsicherheit |
| vite | 6.x | Build-Tool, Dev-Server |
| mobx | 6.x | State Management |
| mobx-react-lite | 4.x | React-Bindings für MobX |
| @mantine/core | 7.x | UI-Komponenten |
| @mantine/hooks | 7.x | React-Hooks |
| @mantine/modals | 7.x | Modal-Dialoge |
| react-router-dom | 6.x | Client-Routing |
| plotly.js | 2.x | Interaktive Charts |
| react-plotly.js | 2.x | React-Wrapper für Plotly |
| react-grid-layout | 1.x | Drag-and-Drop-Grid |
| axios | 1.x | HTTP-Client |
| i18next | 23.x | Internationalisierung |
| react-i18next | 14.x | React-i18n-Bindings |
| styled-components | 6.x | CSS-in-JS |
| @tabler/icons-react | 3.x | Icons |
| @fontsource/roboto | – | Schriftart |
| @fontsource/montserrat | – | Schriftart |

### DevDependencies (Frontend)

| Paket | Zweck |
|-------|-------|
| vitest | Test-Runner |
| @testing-library/react | Komponenten-Tests |
| @testing-library/jest-dom | DOM-Matcher |
| jest-axe | Accessibility-Tests |
| jsdom | DOM-Umgebung für Tests |

---

## Backend (`backend/package.json`)

| Paket | Version (ca.) | Zweck |
|-------|---------------|-------|
| express | 4.x | Web-Framework |
| mongoose | 8.x | MongoDB ODM |
| connect-mongo | 5.x | Session-Store |
| bcrypt | 5.x | Passwort-Hashing |
| helmet | 7.x | HTTP-Security-Headers |
| express-rate-limit | 7.x | Rate Limiting |
| cors | 2.x | Cross-Origin Resource Sharing |
| sharp | 0.33.x | Bildverarbeitung |
| fast-xml-parser | 4.x | SDMX-XML-Parsing |
| axios | 1.x | HTTP (falls benötigt) |

### DevDependencies (Backend)

| Paket | Zweck |
|-------|-------|
| jest | Test-Runner |
| supertest | HTTP-Assertion für API-Tests |
| ts-jest | TypeScript-Support für Jest |
| nodemon | Dev-Auto-Reload |
| typescript | Typsicherheit |

---

## User-Docs (`user-docs/package.json`)

| Paket | Version (ca.) | Zweck |
|-------|---------------|-------|
| vitepress | 1.6.x | Statischer Site-Generator |
| playwright | 1.x | Screenshots |
| pdf-lib | 1.x | PDF-Export |

---

## Infrastruktur

| Komponente | Version | Zweck |
|------------|---------|-------|
| Node.js | ≥20 (CI: 22.x) | Runtime |
| MongoDB | 7 | Datenbank |
| nginx | alpine | Frontend/Docs Reverse-Proxy |
| Docker Compose | v2 | Container-Orchestrierung |

---

## POC (`poc-sdmx-explorer/package.json`)

| Paket | Zweck |
|-------|-------|
| react | 19.x |
| vite | 6.x |
| typescript | 5.x |

Keine UI-Library, keine Backend-Dependencies.

---

## CI/CD

| Plattform | Workflow |
|-----------|----------|
| GitHub Actions | Frontend → Azure SWA |
| GitHub Actions | Backend → Azure Web App |
| GitHub Actions | User-Docs → Artefakte |

---

## Externe APIs

| API | Protokoll | Auth |
|-----|-----------|------|
| daten.uba.de (SDMX) | HTTPS REST | Keine (öffentlich) |
| datacube.uba.de | HTTPS | Keine (öffentlich) |
