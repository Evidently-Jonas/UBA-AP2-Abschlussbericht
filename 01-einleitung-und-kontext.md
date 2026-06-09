# 01 – Einleitung und Kontext

## 1.1 Problemstellung

Behörden und Forschungseinrichtungen veröffentlichen zunehmend statistische Umweltdaten in standardisierten Formaten. Das **Umweltbundesamt (UBA)** stellt umfangreiche Datensätze über die SDMX-basierte API `daten.uba.de` bereit und visualisiert sie im **UBA Data Cube** (`datacube.uba.de/vis`).

Für fachliche Kommunikation, politische Berichterstattung und öffentliche Aufklärung reicht eine generische Data-Cube-Oberfläche jedoch nicht immer aus. Es besteht Bedarf an **kuratierten, narrativ geführten Dashboards**, die:

- spezifische Umweltthemen (z. B. Treibhausgasemissionen, Luftqualität) fokussieren,
- interaktive Filter und KPIs kombinieren,
- mehrsprachig (DE/EN) publiziert werden können,
- von autorisierten Fachkräften **ohne Entwicklerunterstützung** erstellt und gepflegt werden.

Die Anwendung **UBA Dashboarding** (intern: *Data-Cube-Dashboards*) adressiert genau diese Lücke: Sie verbindet die bestehende SDMX-Dateninfrastruktur mit einem visuellen Dashboard-Editor und einem Veröffentlichungsworkflow.

## 1.2 Domäne und Einordnung

| Aspekt | Beschreibung |
|--------|--------------|
| **Organisation** | Umweltbundesamt (UBA), Bundesrepublik Deutschland |
| **Datenstandard** | SDMX (Statistical Data and Metadata eXchange) |
| **Primäre Datenquelle** | `https://daten.uba.de` (Release- und Design-Space) |
| **Verwandtes System** | UBA Data Cube Visualisierung (`datacube.uba.de/vis`) |
| **Zielgruppe (Autoren)** | Fachreferate, Kommunikation, Reviewer |
| **Zielgruppe (Leser)** | Öffentlichkeit, Politik, Wissenschaft, Medien |

Die Anwendung ist **kein Ersatz** für den Data Cube, sondern ein **ergänzendes Publikationswerkzeug**. Deep-Links aus dem Editor führen Nutzer bei Bedarf in die vollständige Cube-Visualisierung (`frontend/src/utils/datacubeUrl.ts`).

## 1.3 Stakeholder und Rollenmodell

Das System unterscheidet vier Rollen mit gestaffelten Rechten:

| Rolle | Beschreibung |
|-------|--------------|
| **Public** | Anonyme Nutzer; Zugriff nur auf veröffentlichte Dashboards |
| **Reviewer** | Interne Prüfung aller Dashboards ohne Bearbeitungsrecht |
| **Author** | Erstellung, Bearbeitung und Veröffentlichung eigener Dashboards |
| **Admin** | Vollzugriff inkl. Benutzer-, Tag- und CTA-Verwaltung |

Details: `docs/authentication.md`, Anhang C.

## 1.4 Projektziele (übergeordnet)

1. **Demokratisierung der Datenvisualisierung** – Fachanwender erstellen Dashboards ohne Programmierkenntnisse.
2. **Datenintegrität** – Bindung an autoritative SDMX-Quellen statt manueller CSV-Uploads.
3. **Governance** – Rollenbasierte Freigabe, Eigentümerschaft (`userId`), Review-Workflow.
4. **Barrierefreiheit** – WCAG-orientierte UI mit systematischen axe-Tests.
5. **Mehrsprachigkeit** – Parallele DE/EN-Inhalte auf UI- und Datenbankebene.
6. **Betriebsfähigkeit** – Production-Deployment auf Azure mit Docker-Alternativen.

## 1.5 Abgrenzung

**Im Scope der Hauptanwendung:**
- Dashboard-Editor mit 10 Tile-Typen
- SDMX-Proxy mit Caching im Backend
- Benutzer- und Tagverwaltung
- Öffentliche Dashboard-Galerie mit Filterung
- Eigenständige Bedienerdokumentation (VitePress)

**Außerhalb des Scopes / separat:**
- `poc-sdmx-explorer/` – eigenständiger Proof-of-Concept zur SDMX-Erkundung (browser-direkt, ohne Backend)
- UBA Data Cube selbst (externes System)
- Datenproduktion und -pflege in `daten.uba.de`

## 1.6 Wissenschaftliche Einordnung

Der Bericht kann die Anwendung in folgenden Forschungs- bzw. Praxisfeldern verorten:

- **E-Government / Open Government Data** – Veröffentlichung behördlicher Statistiken
- **Visual Analytics** – Interaktive Filter, verknüpfte Visualisierungen
- **Low-Code / No-Code BI** – Visueller Editor für nicht-technische Autoren
- **Softwarearchitektur** – Schichtentrennung, Lazy Loading, Plugin-Registry
- **Security Engineering** – Defense-in-Depth in Webanwendungen mit Session-Auth

## 1.7 Methodik der Repository-Analyse

Diese Inhaltssammlung basiert auf:

- Strukturanalyse des Monorepos (Frontend, Backend, Docs, Ops)
- Auswertung der Entwicklerdokumentation (`docs/`)
- Quellcode-Review zentraler Module (Architektur, SDMX, Filter, Security)
- Auswertung von Test- und CI/CD-Konfigurationen
- Vergleich mit dem SDMX-POC als explorative Vorarbeit
