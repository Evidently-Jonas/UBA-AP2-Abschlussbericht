# Berichtsmatrix AP2

Diese Matrix verbindet die Anforderungen aus AP2 der Leistungsbeschreibung mit der tatsächlichen Umsetzung, den Nachweisen im Bericht und der Argumentation zur Zielerreichung und Wirtschaftlichkeit.

## Zweck der Matrix

Die Matrix dient als roter Faden für den Abschlussbericht. Sie soll sicherstellen, dass der Bericht nicht nur die Anwendung beschreibt, sondern systematisch zeigt:

1. welche Anforderungen aus AP2 bestanden,
2. wie diese Anforderungen umgesetzt wurden,
3. wodurch die Umsetzung belegbar ist,
4. welchen Beitrag die Umsetzung zu Nutzen, Nachhaltigkeit und Wirtschaftlichkeit leistet,
5. welche Punkte noch offen, zu konkretisieren oder als Grenze einzuordnen sind.

## Matrix

| AP2-Anforderung | Umsetzung / Nachweis in der Anwendung | Quelle / Beleg | Berichtskapitel | Mögliche Abbildung/Tabelle | Bewertungsaspekt | Status | Wirtschaftlichkeits- / Nachhaltigkeitsargument |
|---|---|---|---|---|---|---|---|
| Co-Design-Workshop im UBA durchführen | Workshop als Grundlage für Anforderungen, Zielgruppen, Bedienkonzepte und Dashboard-Struktur | Leistungsbeschreibung AP2; Workshop-Dokumentation noch ergänzen | 3 Methodisches Vorgehen | Tabelle: Methodische Schritte; ggf. Workshop-Ergebnisübersicht | Nutzerzentrierung, Bedarfsgerechtigkeit | offen zu konkretisieren | Frühzeitige Abstimmung reduziert Fehlentwicklung und spätere Nachsteuerungskosten. |
| Inhaltliches Konzept für Dashboard-Format | Dashboards werden als kuratierte Ergänzung zum Data Cube positioniert; Fokus auf Zielgruppen, Kommunikationsziele, Themenauswahl und Grenzen des Formats | Inhaltssammlung Kapitel 01, 02, 13; Leistungsbeschreibung AP2 | 4 Fachliches Dashboard-Konzept | Tabelle: Zielgruppen, Kommunikationsziele und Dashboard-Funktionen | fachliche Angemessenheit | konzeptionell belegt | Klare Einsatzgrenzen verhindern, dass Dashboards für ungeeignete Analysezwecke überentwickelt werden. |
| Designkonzept für Dashboards | Seitenstruktur mit öffentlicher Galerie, Dashboard-Seiten, Hero-Bereich, Tile-Layout, UBA-CD-orientierter UI und mehrsprachigen Inhalten | Inhaltssammlung Kapitel 04, 08, 10; Screenshots noch ergänzen | 4 Fachliches Konzept; 7 Editor und Tile-System; 9 Barrierefreiheit und i18n | Abbildung: Seitenaufbau Dashboard; Screenshots öffentlicher Ansicht | Verständlichkeit, Wiedererkennbarkeit, öffentliche Nutzbarkeit | teilweise belegt | Wiederverwendbare Layout- und Tile-Struktur reduziert Aufwand für neue Dashboard-Themen. |
| Technisches Konzept | Drei-Schichten-Architektur mit React-Frontend, Express/Node.js-Backend, MongoDB, SDMX-Proxy und Deployment-Konzept | Inhaltssammlung Kapitel 03, 05, 06, 12; Architekturdiagramm | 5 Technische Realisierung | Systemkontext; Architekturdiagramm | Wartbarkeit, Skalierbarkeit, Integrationsfähigkeit | belegt | Klare Schichtentrennung erleichtert Betrieb, Wartung und spätere Erweiterung. |
| Prüfung bestehender Tools / geeigneter technischer Basis | Abwägung zwischen Standardtools und passgenauer, modularer Eigenentwicklung; Eigenentwicklung begründet durch Anforderungen an UBA-Webintegration, SDMX-Anbindung, Editor, Barrierefreiheit und redaktionellen Workflow | Inhaltssammlung Kapitel 13; ggf. Tool-Evaluierung ergänzen | 11 Zielerreichung und Wirtschaftlichkeit | Tabelle: Abwägung Standardtool vs. Custom-Lösung | Praxistauglichkeit, Verwertbarkeit, Wirtschaftlichkeit | zu schärfen | Die Lösung vermeidet Lizenz- und Anpassungsabhängigkeiten, wenn Standardtools die geforderte Integration und Barrierefreiheit nicht ausreichend erfüllen. |
| Integration in UBA-Webseite / öffentliche Bereitstellung | Öffentliche Dashboard-Galerie, veröffentlichte Dashboard-Routen, Sprachrouting, rechtliche Seiten, Deployment über Azure und Docker-Optionen | Inhaltssammlung Kapitel 03, 10, 12; Deployment-Stand ergänzen | 10 Qualität, Betrieb und Wartbarkeit | Deployment-Diagramm; Tabelle Betriebsoptionen | Betriebssicherheit, Veröffentlichungsfähigkeit | teilweise belegt | Betrieb über standardisierte Deployment-Wege reduziert Betriebsrisiken und ermöglicht Übertragbarkeit. |
| Anbindung an Data Explorer / Data Cube | Backend-SDMX-Proxy, Dataflow-Auswahl im Editor, release/design Space, Caching, Deep Links zum Data Cube | Inhaltssammlung Kapitel 06, 07; API-Referenz Anhang B | 6 Datenintegration und SDMX-Anbindung | Sequenzdiagramm SDMX-Datenfluss | Datenintegrität, Aktualisierbarkeit | belegt | Direkte SDMX-Anbindung vermeidet manuelle Datenkopien und reduziert Pflegeaufwand bei Datenaktualisierungen. |
| Möglichkeit zur schnellen Veröffentlichung ohne externe Unterstützung | Visueller Drag-and-Drop-Editor, Speichern/Laden, Duplizieren, Publizieren, Rollenmodell, Dashboard-Eigentümerschaft | Inhaltssammlung Kapitel 02, 04, 08, 09; Rollenmatrix Anhang C | 7 Editor und Tile-System; 8 Governance | Workflow-Diagramm Author → Review/Publish; Rollenmatrix | Effizienz, redaktionelle Eigenständigkeit | belegt, fachlich noch mit Beispielen zu untermauern | UBA-Fachanwenderinnen und -anwender können Inhalte selbst pflegen; externe Entwicklungsaufwände sinken bei neuen oder aktualisierten Dashboards. |
| Grafisch ansprechende Dashboard-Funktion | Implementierte Tile-Typen: Bar Chart, Donut, KPI, Year Picker, Legend, Intro, Text, Link, Button; konfigurierbare Eigenschaften und Datenbindung | Inhaltssammlung Kapitel 08; Anhang D Tile-Typen | 7 Editor und Tile-System | Tabelle: Tile-Typen und Einsatzbereiche | Nutzbarkeit, Erweiterbarkeit, gestalterische Flexibilität | belegt | Wiederverwendbare Komponenten erhöhen den Nutzen der einmal entwickelten Plattform über einzelne Dashboards hinaus. |
| Drei Dashboards zu ausgewählten Themen | Konkrete Dashboard-Beispiele mit Thema, Zielgruppe, Datenbasis, Layout und Funktion müssen ergänzt werden | Projektstand / Screenshots / Dashboard-URLs noch ergänzen | 8 Beispiel-Dashboards; 11 Zielerreichung | Steckbriefe; Screenshots | konkrete Auftragserfüllung | offen | Die drei Dashboards dienen zugleich als Nachweis der Praxistauglichkeit und als wiederverwendbare Muster für weitere Themen. |
| Barrierefreiheit berücksichtigen | Mehrsprachigkeit, semantische UI, ARIA-Labels, Skip-Link, Form-Labels, jest-axe-Tests; finale formale Prüfung bleibt gesondert erforderlich | Inhaltssammlung Kapitel 10, 11; UBA-Vorlagenrichtlinie | 9 Barrierefreiheit und i18n | Tabelle: Maßnahmen und Grenzen | Zugang, Compliance, Qualität | teilweise belegt | Früh integrierte Barrierefreiheitsmaßnahmen reduzieren spätere Nachbearbeitung und verbessern öffentliche Nutzbarkeit. |
| Redaktionelle Governance ermöglichen | Rollen Public, Reviewer, Author, Admin; Session-Auth; CSRF; Dashboard-Eigentümerschaft; Admin-Verwaltung | Inhaltssammlung Kapitel 09; Rollenmatrix Anhang C | 8 Governance und Sicherheit | Rollenmatrix; Sicherheitsmodell | Kontrolle, Verantwortlichkeit, Schutz vor Fehlbedienung | belegt | Rollenbasierte Prozesse senken organisatorische Risiken und unterstützen kontrollierte Veröffentlichung. |
| Qualitätssicherung und Betrieb absichern | Frontend-/Backend-Tests, a11y-Tests, SDMX-Integrationstests, CI/CD, Docker-/Azure-Deployment, Backup-Konzept | Inhaltssammlung Kapitel 11, 12 | 10 Qualität, Betrieb und Wartbarkeit | Tabelle: Teststrategie; Deployment-Diagramm | Betriebssicherheit, Wartbarkeit | teilweise belegt, Zahlen ergänzen | Qualitätssicherung verringert Folgekosten durch Fehlerbehebung und erleichtert nachhaltigen Betrieb. |
| Zwischenbericht / Bericht zu AP2 | Bericht dokumentiert Zielsetzung, Vorgehen, Umsetzung, Zielerreichung, Wirtschaftlichkeit, Grenzen und Ausblick | Bericht selbst; Leistungsbeschreibung Berichterstattung | gesamter Bericht | Tabelle: Zielerreichung AP2 | Nachvollziehbarkeit, Prüfbarkeit | in Erstellung | Strukturierte Dokumentation erleichtert fachliche Abnahme und spätere Prüfung der Mittelverwendung. |

## Priorisierte offene Punkte

| Priorität | Offener Punkt | Warum wichtig? | Benötigte Information |
|---|---|---|---|
| 1 | Drei konkrete Dashboards | Zentraler Nachweis der direkten AP2-Erfüllung | Namen, Themen, Datenbasis, Zielgruppe, Screenshots, aktueller Status |
| 1 | Co-Design-Workshop | Methodischer Nachweis der Nutzerzentrierung | Datum, Teilnehmendenkreis, zentrale Ergebnisse, abgeleitete Anforderungen |
| 1 | Deployment-Stand | Nachweis der lauffähigen Integration | DEV/PROD-URL, Hosting-Modell, aktueller Betriebsstatus |
| 2 | Test- und a11y-Zahlen | Nachweis technischer Qualität | Anzahl Tests, relevante Ergebnisse, bekannte Lücken |
| 2 | Tool-/Technologieabwägung | Wichtig für Wirtschaftlichkeit | Warum keine reine Standardlösung wie Superset, Power BI, Tableau, Shiny etc.? |
| 2 | Screenshots / Abbildungen | Bericht braucht prüfbare visuelle Nachweise | öffentliche Galerie, Dashboard, Editor, Dataflow-Auswahl, Tile-Konfiguration |
| 3 | Projektmetadaten | Formale Berichtsfassung | Laufzeit, Autorenschaft, Institutionen, FKZ, ggf. UBA-FB |
| 3 | Quellen- und Repositories | Nachvollziehbarkeit | Repository-Links, Dokumentationsstände, Nutzerhandbuch |

## Leitfragen für die weitere Ausarbeitung

1. Welche Aussagen im Bericht belegen unmittelbar die Erfüllung der Leistungsbeschreibung?
2. Welche Aussagen belegen den nachhaltigen Nutzen über die drei Dashboards hinaus?
3. Wo muss klar zwischen realisierter Funktion, prototypischem Stand und perspektivischem Ausbau unterschieden werden?
4. Welche technischen Details sind für die Prüfbarkeit nötig, ohne den Bericht zu einer Entwicklerdokumentation zu machen?
5. Welche Abbildungen und Tabellen erzeugen den höchsten Nachweiswert?