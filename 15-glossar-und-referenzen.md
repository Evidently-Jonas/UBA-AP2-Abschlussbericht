# 15 – Glossar und Referenzen

## 15.1 Glossar

| Begriff | Definition |
|---------|------------|
| **Author** | Rolle mit Rechten zum Erstellen, Bearbeiten und Veröffentlichen eigener Dashboards |
| **Barrierefreiheit (a11y)** | Zugänglichkeit digitaler Anwendungen für Menschen mit Behinderungen (WCAG/BITV) |
| **BFF** | Backend for Frontend – API-Schicht, die auf Frontend-Bedürfnisse zugeschnitten ist |
| **Brushing & Linking** | Visual-Analytics-Konzept: Selektion in einer View beeinflusst andere Views |
| **Command Pattern** | Entwurfsmuster: Aktionen als Objekte für Undo/Redo |
| **CSRF** | Cross-Site Request Forgery – Angriff durch unautorisierte Requests |
| **CTA** | Call-to-Action – Handlungsaufforderungs-Kachel in der öffentlichen Galerie |
| **Data Cube** | Mehrdimensionale Datenstruktur; UBA-Visualisierung unter datacube.uba.de |
| **Dataflow** | SDMX-Konzept: logische Datenlieferung mit Metadaten und Struktur |
| **DSD** | Data Structure Definition – SDMX-Schema einer Dataflow |
| **GridFS** | MongoDB-Dateisystem für große Binärdateien (Bilder) |
| **KPI** | Key Performance Indicator – Kennzahl in Dashboard-Kacheln |
| **MobX** | Reactive State-Management-Bibliothek für JavaScript |
| **MultilingualText** | Typ `Record<string, string>` für mehrsprachige Inhalte |
| **OLAP** | Online Analytical Processing – multidimensionale Datenanalyse |
| **Plotly** | JavaScript-Charting-Bibliothek mit Interaktions-API |
| **POC** | Proof of Concept – Machbarkeitsnachweis (poc-sdmx-explorer) |
| **Projektions-Layer** | Frontend-Schicht für Filter, Aggregation, Selektion über Rohdaten |
| **Public** | Rolle für anonyme Nutzer (nur veröffentlichte Dashboards) |
| **RBAC** | Role-Based Access Control – rollenbasierte Zugriffskontrolle |
| **Reviewer** | Rolle mit Lesezugriff auf alle Dashboards ohne Bearbeitungsrecht |
| **SDMX** | Statistical Data and Metadata eXchange – ISO-Standard für Statistikdaten |
| **Session** | Server-seitiger Zustand für authentifizierte Nutzer |
| **Space (SDMX)** | release (Produktion) oder design (Test) bei daten.uba.de |
| **Tile** | Dashboard-Komponente (Chart, KPI, Text etc.) im Editor |
| **UBA** | Umweltbundesamt – Bundesbehörde für Umweltschutz |
| **Undo/Redo** | Rückgängig/Wiederholen von Editor-Aktionen |
| **VitePress** | Statischer Site-Generator für Dokumentation (Vue-basiert) |
| **WCAG** | Web Content Accessibility Guidelines |

## 15.2 Standards und Normen

| Standard | Relevanz |
|----------|----------|
| **SDMX 2.1/3.0** | Datenformat und API der UBA-Statistiken |
| **WCAG 2.1 AA** | Barrierefreiheits-Richtlinie (axe-Tests orientieren sich daran) |
| **BITV 2.0** | Barrierefreie-Informationstechnik-Verordnung (DE) |
| **EN 301 549** | Europäische Norm für digitale Barrierefreiheit |
| **OWASP Top 10** | Sicherheitsmaßnahmen orientieren sich daran (CSRF, Injection, XSS) |
| **DSGVO** | Datenschutz (Privacy-Seite, Session-Daten) |

## 15.3 Externe Systeme

| System | URL | Rolle |
|--------|-----|-------|
| UBA SDMX API | https://daten.uba.de | Primäre Datenquelle |
| UBA Data Cube | https://datacube.uba.de/vis | Ergänzende Visualisierung |
| Azure Static Web Apps | (Deployment-Ziel Frontend) | Hosting |
| Azure Web App Service | (Deployment-Ziel Backend) | Hosting |

## 15.4 Technische Referenzen

### Projektintern

| Dokument | Pfad |
|----------|------|
| Root-README | `README.md` |
| Architektur | `docs/architecture.md` |
| Authentifizierung | `docs/authentication.md` |
| Backend-Sicherheit | `docs/backend/security.md` |
| UBA SDMX API | `docs/backend/uba-sdmx.md` |
| Filter (technisch) | `docs/filters-technical.md` |
| Neue Tiles | `docs/new-tile-guide.md` |
| Deployment | `docs/deployment.md` |
| Frontend Testing | `frontend/docs/TESTING.md` |
| SDMX-POC | `poc-sdmx-explorer/README.md` |
| Bedienerdoku | `user-docs/README.md` |

### Technologien

| Technologie | Dokumentation |
|-------------|---------------|
| React | https://react.dev |
| MobX | https://mobx.js.org |
| Mantine | https://mantine.dev |
| Express | https://expressjs.com |
| Mongoose | https://mongoosejs.com |
| Plotly.js | https://plotly.com/javascript |
| SDMX | https://sdmx.org |
| Vitest | https://vitest.dev |
| VitePress | https://vitepress.dev |

## 15.5 Zitierhinweis (Vorschlag)

```
UBA Dashboarding – Data-Cube-Dashboards.
Repository: [Internes Git-Repository UBA].
Stand: Juni 2026.
Entwicklerdokumentation: docs/README.md.
```

## 15.6 Verwandte Arbeiten (Einordnung)

| System / Arbeit | Vergleich |
|-----------------|-----------|
| **PxWeb** (Statistics Sweden) | SDMX-basiert, tabellarisch, kein visueller Editor |
| **Eurostat .Stat** | SDMX Data Explorer, keine Dashboard-Publikation |
| **Grafana** | Dashboard-Editor, aber nicht SDMX-nativ |
| **Power BI / Tableau** | Enterprise BI, proprietäre Datenquellen |
| **Datawrapper** | Einfache Charts, kein SDMX, kein Self-Hosted |

**Alleinstellungsmerkmal UBA Dashboarding:** SDMX-native + visueller Editor + Self-Hosted + Behörden-Governance + Mehrsprachigkeit.
