# 05 – Backend und API

## 5.1 Technologie-Stack

| Komponente | Technologie | Version (ca.) |
|------------|-------------|---------------|
| Runtime | Node.js | ≥20 (CI: 22.x) |
| Framework | Express | 4.x |
| ODM | Mongoose | 8.x |
| Session Store | connect-mongo | MongoDB Collection `sessions` |
| Bildverarbeitung | sharp | JPEG Re-Encode |
| XML-Parsing | fast-xml-parser | SDMX-Strukturen |
| Tests | Jest + supertest | Unit + Integration |

## 5.2 Datenmodelle (MongoDB)

### Dashboard

Pfad: `backend/src/models/Dashboard.ts`, Typen in `backend/src/types/Dashboard.ts`

```typescript
// Vereinfacht
interface Dashboard {
  id: string;           // crypto.randomUUID()
  name: MultilingualText;
  description: MultilingualText;
  pages: Page[];
  published: boolean;
  userId: string;       // Referenz auf User
  tags: string[];       // Tag-Keys
}
```

**Flexibles Komponenten-Schema:** Jede Page enthält `components[]` mit heterogenen `properties`, `layout`, `dataProps` je nach Tile-Typ. MongoDB eignet sich hier besser als relationale Normalisierung.

### User

Pfad: `backend/src/models/User.ts`

- Rollen: `public`, `reviewer`, `author`, `admin`
- Passwort: bcrypt-Hash, `passwordChangedAt` für Session-Invalidierung
- Felder: `username`, `email`, `firstName`, `lastName`, `isActive`

### Tag

Pfad: `backend/src/models/Tag.ts`

- `key` (eindeutig), `label` (MultilingualText), `category`
- Zentral verwaltet; Dashboards referenzieren nur Keys

### CTAItem

Pfad: `backend/src/models/CTAItem.ts`

- Call-to-Action-Kacheln für öffentliche Galerie
- `slug`, `title`, `description`, `href`, `order`, `image`

### Image (GridFS)

Pfad: `backend/src/models/Image.ts`, `backend/src/services/ImageService.ts`

- Original in GridFS
- Thumbnail als Base64 im Dokument (für schnelle Listenansicht)
- MIME-Whitelist: JPEG, PNG, WebP (kein SVG)

## 5.3 REST-API-Übersicht

Vollständige Tabelle: Anhang B.

### Authentifizierung (`/api/auth`)

| Methode | Endpoint | Auth | Beschreibung |
|---------|----------|------|--------------|
| GET | `/csrf-token` | – | CSRF-Token abrufen |
| POST | `/login` | – | Session erstellen |
| POST | `/logout` | Session | Session beenden |
| GET | `/me` | Session | Aktueller Nutzer |
| GET | `/users` | Admin | Alle Nutzer |
| GET | `/users/lookup` | Auth | Schlanke Nutzerliste |
| POST | `/users` | Admin | Nutzer anlegen |
| PUT | `/users/:id` | Admin | Nutzer bearbeiten |
| DELETE | `/users/:id` | Admin | Nutzer löschen |
| POST | `/change-password` | Auth | Eigenes Passwort |
| POST | `/users/:id/reset-password` | Admin | Passwort-Reset |

### Dashboards (`/api/dashboards`)

| Methode | Endpoint | Auth | Beschreibung |
|---------|----------|------|--------------|
| GET | `/` | Auth | Alle Dashboards (rollenabhängig) |
| GET | `/published` | – | Veröffentlichte Dashboards |
| POST | `/` | Author | Neues Dashboard |
| GET | `/:id` | Auth/Public* | Dashboard laden |
| PUT | `/:id` | Author** | Dashboard speichern |
| PUT | `/:id/publish` | Author** | Veröffentlichen |
| PUT | `/:id/assign-user` | Admin | Eigentümer ändern |
| DELETE | `/:id` | Author** | Löschen |
| POST | `/:id/duplicate` | Author | Duplizieren |

\* Public nur wenn `published=true`  
\** Nur eigene Dashboards (`userId`), Admin darf alle

### SDMX-Proxy (`/api/uba-sdmx`)

| Methode | Endpoint | Auth | Beschreibung |
|---------|----------|------|--------------|
| GET | `/health` | – | Service-Status |
| GET | `/dataflows` | – | Dataflow-Liste |
| GET | `/dataflows/:id` | – | Dataflow-Daten |
| GET | `/cache/stats` | Admin | Cache-Statistiken |
| DELETE | `/cache` | Admin | Cache leeren |

### Weitere Endpunkte

- `/api/images` – Upload (Author), öffentlicher GET für `<img>`-Einbettung
- `/api/tags` – Admin-CRUD, `GET /available` für Autoren
- `/api/cta-links` – öffentlicher GET, Admin-Write

## 5.4 Validierung und Schutzmaßnahmen

### Dashboard-Inhalte (`validatePages`)

Bei `PUT /api/dashboards/:id`:
- Max. Anzahl Seiten und Komponenten
- Max. JSON-Größe pro Komponente
- Max. Verschachtelungstiefe (`checkDepth`)
- Whitelist erlaubter Komponententypen (`ALLOWED_COMPONENT_TYPES`)

**Zweck:** Verhindert Stored-XSS und DoS durch überdimensionierte Payloads.

### ID-Generierung

`crypto.randomUUID()` statt `Date.now()` – keine vorhersagbaren IDs.

## 5.5 Session-Management

- Cookie: `uba.session.id`, HTTP-Only, SameSite=Lax
- Production: `Secure`, optional `COOKIE_DOMAIN`
- TTL: 24 Stunden
- Store: MongoDB `sessions` Collection
- Invalidierung: `passwordChangedAt` > `session.loginAt` → Session zerstört

## 5.6 Fehlerbehandlung

- Standardisierte HTTP-Statuscodes (400, 401, 403, 404, 429, 500)
- Rate Limiting → 429 mit Retry-Hinweis
- SDMX-Upstream-Timeout → 15s, dann Fehler an Client

## 5.7 Skalierung und Grenzen

| Aspekt | Aktueller Stand | Grenze |
|--------|-----------------|--------|
| Architektur | Monolith | Horizontal skalierbar mit Sticky Sessions |
| SDMX-Cache | In-Memory pro Instanz | Nicht shared zwischen Replikas |
| MongoDB | Single Instance (Compose) | Replica Set in Production empfohlen |
| GridFS | In MongoDB | Speicherwachstum bei vielen Bildern |

Perspektivische Verbesserungen: Kapitel 14.
