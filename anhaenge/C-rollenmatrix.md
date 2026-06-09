# Anhang C – Rollenmatrix

## Berechtigungen nach Rolle

| Aktion | Public | Reviewer | Author | Admin |
|--------|:------:|:--------:|:------:|:-----:|
| **Dashboards** |
| Veröffentlichte Dashboards anzeigen | ✅ | ✅ | ✅ | ✅ |
| Alle Dashboards anzeigen | ❌ | ✅ | ✅* | ✅ |
| Dashboard erstellen | ❌ | ❌ | ✅ | ✅ |
| Eigenes Dashboard bearbeiten | ❌ | ❌ | ✅ | ✅ |
| Fremdes Dashboard bearbeiten | ❌ | ❌ | ❌ | ✅ |
| Eigenes Dashboard veröffentlichen | ❌ | ❌ | ✅ | ✅ |
| Eigenes Dashboard löschen | ❌ | ❌ | ✅ | ✅ |
| Fremdes Dashboard löschen | ❌ | ❌ | ❌ | ✅ |
| Dashboard duplizieren | ❌ | ❌ | ✅ | ✅ |
| Dashboard-Eigentümer ändern | ❌ | ❌ | ❌ | ✅ |
| **Bilder** |
| Bilder anzeigen (öffentlich) | ✅ | ✅ | ✅ | ✅ |
| Bilder hochladen | ❌ | ❌ | ✅ | ✅ |
| **Tags** |
| Tags anzeigen (verfügbar) | ❌ | ❌ | ✅ | ✅ |
| Tags verwalten | ❌ | ❌ | ❌ | ✅ |
| **CTA-Links** |
| CTA anzeigen (öffentlich) | ✅ | ✅ | ✅ | ✅ |
| CTA verwalten | ❌ | ❌ | ❌ | ✅ |
| **Benutzer** |
| Anmelden / Abmelden | ✅ | ✅ | ✅ | ✅ |
| Eigenes Passwort ändern | ❌** | ✅ | ✅ | ✅ |
| Benutzer anzeigen (Liste) | ❌ | ❌ | ❌ | ✅ |
| Benutzer anlegen | ❌ | ❌ | ❌ | ✅ |
| Benutzer bearbeiten | ❌ | ❌ | ❌ | ✅ |
| Benutzer löschen | ❌ | ❌ | ❌ | ✅ |
| Passwort zurücksetzen | ❌ | ❌ | ❌ | ✅ |
| **SDMX** |
| Dataflows abrufen | ✅ | ✅ | ✅ | ✅ |
| Cache verwalten | ❌ | ❌ | ❌ | ✅ |
| **Dataflow-Nutzung** |
| Nutzungsbericht anzeigen | ❌ | ❌ | ✅ | ✅ |

\* Author sieht alle Dashboards in der Übersicht, kann aber nur eigene bearbeiten  
\** Public kann sich nicht anmelden (kein Account)

---

## Frontend-Routen nach Rolle

| Route | Public | Reviewer | Author | Admin |
|-------|:------:|:--------:|:------:|:-----:|
| `/:lang/` (Galerie) | ✅ | ✅ | ✅ | ✅ |
| `/:lang/view/:id` | ✅* | ✅ | ✅ | ✅ |
| `/:lang/login` | ✅ | ✅ | ✅ | ✅ |
| `/:lang/dashboard-overview` | ❌ | ✅ | ✅ | ✅ |
| `/:lang/edit/:id` | ❌ | ❌ | ✅** | ✅ |
| `/:lang/dashboard-admin/:id` | ❌ | ❌ | ✅** | ✅ |
| `/:lang/change-password` | ❌ | ✅ | ✅ | ✅ |
| `/:lang/user-management` | ❌ | ❌ | ❌ | ✅ |
| `/:lang/tag-management` | ❌ | ❌ | ❌ | ✅ |
| `/:lang/cta-management` | ❌ | ❌ | ❌ | ✅ |
| `/:lang/dataflow-usage` | ❌ | ❌ | ✅ | ✅ |
| Rechtliche Seiten | ✅ | ✅ | ✅ | ✅ |

\* Nur wenn Dashboard `published=true`  
\** Nur eigene Dashboards (Admin: alle)

---

## Dashboard-Eigentümerschaft

| Szenario | `userId` nach Aktion |
|----------|---------------------|
| Neues Dashboard | Eingeloggter Nutzer |
| Duplizieren | Aufrufender Nutzer (nicht Original-Eigentümer) |
| Admin-Zuweisung | Vom Admin gewählter Nutzer |
| Legacy-Dashboards ohne `userId` | Nur Admin darf bearbeiten/löschen |

---

## Sonderregeln

| Regel | Beschreibung |
|-------|--------------|
| Last-Admin-Schutz | Löschen/Deaktivieren des letzten Admins wird serverseitig blockiert |
| Session-Invalidierung | Passwortwechsel invalidiert alle Sessions des Nutzers |
| CSRF | Alle mutierenden Requests benötigen CSRF-Token |
| Rate Limiting | Login: 10/15min/IP |
