# Berichtsmatrix AP2

Diese Matrix verbindet die Anforderungen aus AP2 der Leistungsbeschreibung mit der tatsächlichen Umsetzung und den geplanten Nachweisen im Bericht.

| AP2-Anforderung | Umsetzung / Nachweis in der Anwendung | Berichtskapitel | Mögliche Abbildung/Tabelle | Bewertungsaspekt |
|---|---|---|---|---|
| Co-Design-Workshop im UBA durchführen | Workshop als Grundlage für Anforderungen, Zielgruppen, Bedienkonzepte und Dashboard-Struktur | 3 Methodisches Vorgehen | Tabelle: Methodische Schritte | Nutzerzentrierung, Bedarfsgerechtigkeit |
| Inhaltliches Konzept für Dashboard-Format | Rolle von Dashboards als kuratierte Ergänzung zum Data Cube; Zielgruppen, Kommunikationsziele, Themenauswahl | 4 Fachliches Dashboard-Konzept | Tabelle: Kommunikationsziele und Dashboard-Funktionen | fachliche Angemessenheit |
| Designkonzept für Dashboards | Seitenstruktur, Hero-Bereich, Kachelsystem, öffentliche Galerie, UBA-CD-orientierte UI | 4 / 8 | Abbildung: Seitenaufbau Dashboard | Verständlichkeit, Wiedererkennbarkeit |
| Technisches Konzept | Drei-Schichten-Architektur, React-Frontend, Express-Backend, MongoDB, SDMX-Proxy | 5 Technische Realisierung | Systemkontext, Architekturdiagramm | Wartbarkeit, Skalierbarkeit, Integrationsfähigkeit |
| Prüfung bestehender Tools / geeigneter technischer Basis | Bewertung von Standardtools vs. Custom-Lösung; modularer Eigenbau als passgenaue Lösung | 11 Zielerreichung und Wirtschaftlichkeit | Tabelle: Abwägung Standardtool vs. Eigenentwicklung | Wirtschaftlichkeit, Praxistauglichkeit |
| Integration in UBA-Webseite / öffentliche Bereitstellung | Öffentliche Routen, Galerie, Sprachrouting, Deployment über Azure/Docker, rechtliche Seiten | 10 Qualität, Betrieb, Wartbarkeit | Deployment-Diagramm | Betriebssicherheit, Veröffentlichungsfähigkeit |
| Anbindung an Data Explorer / Data Cube | Backend-SDMX-Proxy, Dataflow-Auswahl, release/design Space, Caching, Deep Links | 6 Datenintegration SDMX | Sequenzdiagramm SDMX-Datenfluss | Datenintegrität, Aktualisierbarkeit |
| Möglichkeit zur schnellen Veröffentlichung ohne externe Unterstützung | Visueller Editor, Drag-and-Drop, Tile-System, Speichern/Laden/Publizieren, Rollenmodell | 7 Editor und Tile-System / 8 Governance | Workflow-Diagramm Author → Publish | Effizienz, redaktionelle Eigenständigkeit |
| Grafisch ansprechende Dashboard-Funktion | Bar Chart, Donut, KPI, Intro, Text, Link, Button, Year Picker, Legende | 7 / 8 | Tabelle: Tile-Typen | Nutzbarkeit, Erweiterbarkeit |
| Drei Dashboards zu ausgewählten Themen | Konkrete Dashboard-Beispiele ergänzen: Thema, Zielgruppe, Datenbasis, Funktion | 8 Beispiel-Dashboards | Screenshots / Steckbriefe | konkrete Auftragserfüllung |
| Barrierefreiheit berücksichtigen | i18n, semantische UI, axe-Tests, UBA-Formatvorgaben für Bericht; weitere Prüfung erforderlich | 9 Barrierefreiheit und i18n | Tabelle: Maßnahmen und Grenzen | Zugang, Compliance, Qualität |
| Zwischenbericht zu AP2 | Bericht als Abschluss-/Zwischenbericht mit Zielerreichung, Umsetzung, Grenzen und Ausblick | gesamter Bericht | Tabelle: Zielerreichung | Nachvollziehbarkeit |

## Noch zu ergänzen

- Exakte Namen und Inhalte der drei Dashboards.
- Datum und zentrale Ergebnisse des Co-Design-Workshops.
- Screenshots bzw. Abbildungsgrundlagen.
- Falls vorhanden: Lighthouse-/a11y-Werte, Testzahlen, Deployment-URLs.
- Konkrete Verweise auf Repositories, Dokumentation und ggf. Nutzerhandbuch.
