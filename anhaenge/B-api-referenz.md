# Anhang B – API-Referenz (Kurz)

Basis-URL: `http://localhost:3001` (Dev) bzw. konfigurierte Production-URL.

---

## Health

| Methode | Endpoint | Auth | Beschreibung |
|---------|----------|------|--------------|
| GET | `/health` | – | Server-Status |

---

## Authentifizierung (`/api/auth`)

| Methode | Endpoint | Auth | Beschreibung |
|---------|----------|------|--------------|
| GET | `/api/auth/csrf-token` | – | CSRF-Token abrufen |
| POST | `/api/auth/login` | – | Anmelden (Body: username, password) |
| POST | `/api/auth/logout` | Session | Abmelden |
| GET | `/api/auth/me` | Session | Aktueller Nutzer |
| POST | `/api/auth/change-password` | Session | Eigenes Passwort ändern |
| GET | `/api/auth/users` | Admin | Alle Nutzer |
| GET | `/api/auth/users/lookup` | Auth | Schlanke Nutzerliste (id, username, fullName) |
| POST | `/api/auth/users` | Admin | Nutzer anlegen |
| PUT | `/api/auth/users/:id` | Admin | Nutzer bearbeiten |
| DELETE | `/api/auth/users/:id` | Admin | Nutzer löschen |
| POST | `/api/auth/users/:id/reset-password` | Admin | Passwort zurücksetzen |

**Rate Limit:** Login und Reset: 10 Requests / 15 min / IP

---

## Dashboards (`/api/dashboards`)

| Methode | Endpoint | Auth | Beschreibung |
|---------|----------|------|--------------|
| GET | `/api/dashboards` | Auth | Alle Dashboards (rollenabhängig) |
| GET | `/api/dashboards/published` | – | Veröffentlichte Dashboards |
| POST | `/api/dashboards` | Author | Neues Dashboard anlegen |
| GET | `/api/dashboards/:id` | Auth/Public* | Dashboard laden |
| PUT | `/api/dashboards/:id` | Author** | Dashboard speichern |
| PUT | `/api/dashboards/:id/publish` | Author** | Veröffentlichungsstatus ändern |
| PUT | `/api/dashboards/:id/assign-user` | Admin | Eigentümer zuweisen |
| DELETE | `/api/dashboards/:id` | Author** | Dashboard löschen |
| POST | `/api/dashboards/:id/duplicate` | Author | Dashboard duplizieren |

\* Public nur wenn `published=true`  
\** Nur eigene Dashboards (`userId`), Admin darf alle

---

## SDMX-Proxy (`/api/uba-sdmx`)

| Methode | Endpoint | Auth | Query-Parameter | Beschreibung |
|---------|----------|------|-----------------|--------------|
| GET | `/api/uba-sdmx/health` | – | – | Service-Status |
| GET | `/api/uba-sdmx/dataflows` | – | `space`, `lang` | Dataflow-Liste |
| GET | `/api/uba-sdmx/dataflows/:id` | – | `space`, `startPeriod`, `endPeriod`, `lang` | Dataflow-Daten |
| GET | `/api/uba-sdmx/cache/stats` | Admin | – | Cache-Statistiken |
| DELETE | `/api/uba-sdmx/cache` | Admin | – | Cache leeren |

**Rate Limit:** 60 Requests / min / IP

**Space:** `release` (Default) oder `design`

---

## Bilder (`/api/images`)

| Methode | Endpoint | Auth | Beschreibung |
|---------|----------|------|--------------|
| GET | `/api/images` | Auth | Alle Bilder (Liste) |
| GET | `/api/images/type/:type` | Auth | Bilder nach Typ |
| GET | `/api/images/:id` | – | Bild (öffentlich für `<img>`) |
| GET | `/api/images/:id/thumbnail` | – | Thumbnail (öffentlich) |
| POST | `/api/images` | Author | Bild hochladen (Base64) |
| PUT | `/api/images/:id` | Author | Bild aktualisieren |
| DELETE | `/api/images/:id` | Author | Bild löschen |

**Limits:** Max. 5 MB, MIME: JPEG/PNG/WebP

---

## Tags (`/api/tags`)

| Methode | Endpoint | Auth | Beschreibung |
|---------|----------|------|--------------|
| GET | `/api/tags` | Admin | Alle Tags |
| GET | `/api/tags/available` | Auth | Verfügbare Tags für Autoren |
| POST | `/api/tags` | Admin | Tag anlegen |
| PUT | `/api/tags/:id` | Admin | Tag bearbeiten |
| DELETE | `/api/tags/:id` | Admin | Tag löschen |

---

## CTA-Links (`/api/cta-links`)

| Methode | Endpoint | Auth | Beschreibung |
|---------|----------|------|--------------|
| GET | `/api/cta-links` | – | Alle CTA-Items (öffentlich) |
| POST | `/api/cta-links` | Admin | CTA anlegen |
| PUT | `/api/cta-links/:id` | Admin | CTA bearbeiten |
| DELETE | `/api/cta-links/:id` | Admin | CTA löschen |

---

## CSRF-Header

Für alle mutierenden Requests (`POST`, `PUT`, `DELETE`):

```
X-CSRF-Token: <token aus GET /api/auth/csrf-token oder Login-Response>
Cookie: uba.csrf=<gleicher Wert>
```

---

## Fehlercodes

| Code | Bedeutung |
|------|-----------|
| 400 | Validierungsfehler |
| 401 | Nicht authentifiziert |
| 403 | Keine Berechtigung |
| 404 | Nicht gefunden |
| 429 | Rate Limit überschritten |
| 500 | Serverfehler |
