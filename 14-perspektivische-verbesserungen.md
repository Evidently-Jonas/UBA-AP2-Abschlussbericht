# 14 – Perspektivische Verbesserungen

## 14.1 Einordnung

Dieses Kapitel sammelt **technische, fachliche und organisatorische Verbesserungsvorschläge**, abgeleitet aus der Repository-Analyse. Sie eignen sich für den Ausblick eines wissenschaftlichen Berichts.

Priorisierung: **Hoch / Mittel / Niedrig** nach Impact und Aufwand.

---

## 14.2 Funktionale Erweiterungen

### Hoch

| ID | Verbesserung | Beschreibung | Aufwand |
|----|--------------|--------------|---------|
| FE-01 | **Dashboard-Versionierung** | Revision History, Diff, Rollback auf frühere Version | Hoch |
| FE-02 | **Auto-Save** | Periodisches Speichern im Editor, Konfliktauflösung | Mittel |
| FE-03 | **URL-shareable Filter** | Globale Filter als Query-Parameter in View-URL | Mittel |
| FE-04 | **Dashboard-Templates** | Vordefinierte Layouts für häufige Themen | Mittel |

### Mittel

| ID | Verbesserung | Beschreibung | Aufwand |
|----|--------------|--------------|---------|
| FE-05 | **Weitere Tile-Typen** | Tabelle, Karte (Geo), Zeitreihen-Liniendiagramm | Mittel je Tile |
| FE-06 | **Export (PDF/PNG)** | Dashboard als PDF oder Screenshot exportieren | Mittel |
| FE-07 | **Scheduled Publishing** | Veröffentlichung zu definiertem Zeitpunkt | Mittel |
| FE-08 | **Review-Workflow** | Reviewer kann Kommentare hinterlassen, Author benachrichtigen | Hoch |
| FE-09 | **Persistierte Default-Filter** | Globale Filter im Dashboard-JSON speicherbar | Niedrig |

### Niedrig

| ID | Verbesserung | Beschreibung | Aufwand |
|----|--------------|--------------|---------|
| FE-10 | **Dashboard-Favoriten** | Nutzer können Dashboards bookmarken | Niedrig |
| FE-11 | **Embed-Modus** | iframe-Einbettung für externe Websites | Mittel |
| FE-12 | **Weitere Sprachen** | FR, ES etc. (Infrastruktur vorhanden) | Mittel (Übersetzung) |

---

## 14.3 Technische Verbesserungen

### Hoch

| ID | Verbesserung | Beschreibung | Aufwand |
|----|--------------|--------------|---------|
| TE-01 | **E2E-Tests (Playwright)** | Login → Edit → Save → Publish → View | Mittel |
| TE-02 | **Frontend-Tests in CI** | Vitest in Azure-SWA-Workflow | Niedrig |
| TE-03 | **Distributed SDMX-Cache** | Redis statt In-Memory | Mittel |
| TE-04 | **Strukturiertes Logging** | JSON-Logs, Correlation-IDs | Mittel |

### Mittel

| ID | Verbesserung | Beschreibung | Aufwand |
|----|--------------|--------------|---------|
| TE-05 | **Code Coverage** | Istanbul/c8 mit Thresholds | Niedrig |
| TE-06 | **MongoDB Replica Set** | Hochverfügbarkeit, automatisches Failover | Mittel |
| TE-07 | **API-Versionierung** | `/api/v2/` für Breaking Changes | Mittel |
| TE-08 | **WebSocket/SSE** | Echtzeit-Updates bei Datenänderungen | Hoch |
| TE-09 | **Service Worker / PWA** | Offline-Viewing veröffentlichter Dashboards | Mittel |

### Niedrig

| ID | Verbesserung | Beschreibung | Aufwand |
|----|--------------|--------------|---------|
| TE-10 | **OpenAPI/Swagger** | Automatische API-Dokumentation | Niedrig |
| TE-11 | **Storybook** | Komponenten-Katalog für UI-Entwicklung | Mittel |
| TE-12 | **Bundle-Analyse** | webpack-bundle-analyzer / rollup-plugin-visualizer | Niedrig |

---

## 14.4 Sicherheit und Compliance

| ID | Verbesserung | Beschreibung | Priorität |
|----|--------------|--------------|-----------|
| SE-01 | **SSO/OIDC** | Integration mit Behörden-IdP (LDAP, AD) | Hoch (für Enterprise) |
| SE-02 | **2FA/MFA** | Zwei-Faktor-Authentifizierung für Admins | Mittel |
| SE-03 | **Audit-Log** | Wer hat wann welches Dashboard geändert | Hoch |
| SE-04 | **SAST/DAST** | Dependabot, CodeQL, OWASP ZAP in CI | Mittel |
| SE-05 | **Account-Lockout** | Nach N fehlgeschlagenen Logins | Mittel |
| SE-06 | **Formale WCAG-Zertifizierung** | BITV 2.0 / EN 301 549 Audit | Mittel |

---

## 14.5 Daten und SDMX

| ID | Verbesserung | Beschreibung | Priorität |
|----|--------------|--------------|-----------|
| DA-01 | **Inkrementelle Updates** | Nur neue Observations laden, nicht Full-Fetch | Mittel |
| DA-02 | **Dataflow-Versionierung** | Explizite Version statt `latest`-Fallback | Mittel |
| DA-03 | **Dataflow-Health-Monitoring** | Alert wenn Upstream-Dataflow sich ändert | Hoch |
| DA-04 | **OR-Filter** | ODER-Verknüpfung zwischen Membern | Niedrig |
| DA-05 | **Berechnete Felder** | z. B. Pro-Kopf-Emissionen aus Bevölkerungsdaten | Mittel |
| DA-06 | **SDMX-ML Export** | Dashboard-Filter als SDMX-Query exportieren | Niedrig |

---

## 14.6 Betrieb und Observability

| ID | Verbesserung | Beschreibung | Priorität |
|----|--------------|--------------|-----------|
| OP-01 | **APM (Application Performance Monitoring)** | Azure App Insights, Datadog o. ä. | Hoch |
| OP-02 | **Zentrales Logging** | ELK, Loki, Azure Log Analytics | Hoch |
| OP-03 | **Uptime-Monitoring** | Pingdom, UptimeRobot auf /health | Mittel |
| OP-04 | **Staging-Umgebung** | Separater Branch/Stack vor Production | Mittel |
| OP-05 | **Blue-Green Deployment** | Zero-Downtime-Deploys | Niedrig |
| OP-06 | **Backup-Restore-Tests** | Regelmäßige Restore-Übungen | Mittel |

---

## 14.7 Forschungsfragen

Für einen wissenschaftlichen Bericht können folgende **offene Forschungsfragen** diskutiert werden:

1. **Usability:** Wie intuitiv ist der Editor für Nicht-Techniker? (Nutzerstudie)
2. **Cognitive Load:** Reicht das 2-Ebenen-Filtermodell (global/lokal) aus?
3. **Performance:** Wie skaliert die Anwendung bei 100+ gleichzeitigen Viewern?
4. **Data Literacy:** Verbessern kuratierte Dashboards das Datenverständnis der Öffentlichkeit?
5. **Governance:** Wie wirkt sich rollenbasierte Freigabe auf Publikationsgeschwindigkeit aus?
6. **Accessibility:** Erfüllt die Anwendung BITV 2.0 in realen Screenreader-Tests?
7. **SDMX-Standard:** Wie vergleicht sich die Proxy-Architektur mit anderen SDMX-Frontends (PxWeb, .Stat)?

---

## 14.8 Roadmap-Vorschlag (Phasen)

### Phase 1 – Stabilisierung (0–3 Monate)
- E2E-Tests, Frontend-CI-Tests
- Strukturiertes Logging
- Audit-Log (Basis)
- Dataflow-Health-Monitoring

### Phase 2 – Erweiterung (3–6 Monate)
- Dashboard-Versionierung
- Auto-Save
- Redis-Cache
- SSO-Evaluation

### Phase 3 – Reife (6–12 Monate)
- Review-Workflow
- Weitere Tile-Typen
- Formale a11y-Zertifizierung
- Staging/Blue-Green

---

## 14.9 Risiken bei Nicht-Umsetzung

| Risiko | Auswirkung | Betroffene Verbesserung |
|--------|------------|-------------------------|
| Regressions im Editor | Datenverlust, Bugs in Production | TE-01, TE-02 |
| SDMX-Upstream-Änderung | Dashboards zeigen falsche Daten | DA-03 |
| Skalierung bei Traffic-Spitzen | Langsame Ladezeiten | TE-03, OP-01 |
| Compliance-Lücke | BITV-Beschwerden | SE-06 |
| Kein Audit-Trail | Unklare Verantwortlichkeit | SE-03 |
