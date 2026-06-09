# 09 – Authentifizierung und Sicherheit

## 9.1 Sicherheitsarchitektur (Überblick)

Die Anwendung implementiert **Defense-in-Depth** mit mehreren unabhängigen Schutzebenen:

```
┌─────────────────────────────────────────────────────────┐
│  Transport: HTTPS (Production), HSTS via helmet         │
├─────────────────────────────────────────────────────────┤
│  Rate Limiting: Auth 10/15min, API 300/15min, SDMX 60/min│
├─────────────────────────────────────────────────────────┤
│  Session: HTTP-Only Cookie, 24h TTL, Mongo-Store       │
├─────────────────────────────────────────────────────────┤
│  CSRF: Double-Submit (Session + Cookie + Header)      │
├─────────────────────────────────────────────────────────┤
│  Input Validation: isPlainString, validatePages       │
├─────────────────────────────────────────────────────────┤
│  Authorization: Rollen-Middleware (requireAuth/Admin) │
├─────────────────────────────────────────────────────────┤
│  Content Security: Bild-Re-Encode, kein SVG, CSP      │
└─────────────────────────────────────────────────────────┘
```

Vollständige Übersicht: `docs/backend/security.md`

## 9.2 Authentifizierung

### Session-basiert (nicht JWT)

| Aspekt | Implementierung |
|--------|-----------------|
| Mechanismus | Server-seitige Session in MongoDB |
| Cookie | `uba.session.id`, HTTP-Only, SameSite=Lax |
| Production | `Secure=true`, optional `COOKIE_DOMAIN` |
| TTL | 24 Stunden |
| Secret | `SESSION_SECRET` (≥32 Zeichen, Pflicht in Prod) |

**Begründung Session vs. JWT:**
- Einfachere Invalidierung (Passwortwechsel, Logout)
- Kein Token-Refresh-Overhead
- Cookie-basiert = XSS-resistenter als localStorage-JWT

### Passwort-Policy

- Mindestens 10 Zeichen
- Mindestens 1 Buchstabe + 1 Ziffer
- Bcrypt mit 12 Salt-Rounds
- Erzwungen in allen Flows (Anlegen, Ändern, Reset, Admin-Script)

### Session-Invalidierung bei Passwortwechsel

```
User.passwordChangedAt wird beim Hash-Update gesetzt
→ Bei jedem Request: session.loginAt < passwordChangedAt?
→ Ja: Session zerstört, Nutzer ausgeloggt
```

Betrifft alle Geräte des Nutzers gleichzeitig.

## 9.3 Autorisierung (RBAC)

### Rollen-Hierarchie

```
Admin > Author > Reviewer > Public
```

### Middleware (Backend)

| Middleware | Prüfung |
|------------|---------|
| `requireAuth` | Eingeloggt |
| `requireAuthor` | Rolle author oder admin |
| `requireAdmin` | Rolle admin |

### Frontend Route Guards

Geschützte Routen prüfen `AuthStore.user.role` vor Render.

### Dashboard-Eigentümerschaft

- `userId` ist einzige Quelle für Eigentum
- Author darf nur eigene Dashboards bearbeiten/löschen
- Admin darf alle Dashboards und `userId` ändern
- Duplizieren setzt `userId` auf Aufrufer

## 9.4 CSRF-Schutz

**Double-Submit-Cookie-Pattern** (hartcodiert aktiv, kein Feature-Flag):

1. Login → Token in Session + Cookie `uba.csrf` (nicht HTTP-Only)
2. Mutierende Requests → Header `X-CSRF-Token` erforderlich
3. Vergleich: Header == Cookie == Session via `crypto.timingSafeEqual`
4. Login/Logout → Token-Rotation

**Warum nicht HTTP-Only für CSRF-Cookie?**
Double-Submit erfordert, dass JavaScript den Cookie-Wert lesen und als Header mitsenden kann.

## 9.5 Schutz vor Injection

### NoSQL-Injection

Alle Auth-Body-Felder werden mit `isPlainString` validiert:

```javascript
// Angriff (blockiert):
{ "username": { "$ne": null } }

// Erlaubt:
{ "username": "admin", "password": "..." }
```

### Dashboard-Inhalte

`validatePages` bei PUT:
- Max. Seiten/Komponenten
- Max. JSON-Größe
- Max. Verschachtelungstiefe
- Whitelist `ALLOWED_COMPONENT_TYPES`

## 9.6 Bild-Upload-Sicherheit

| Maßnahme | Detail |
|----------|--------|
| MIME-Whitelist | JPEG, PNG, WebP |
| Kein SVG | Verhindert Stored XSS |
| Größenlimit | 5 MB |
| Re-Encode | sharp → garantiertes JPEG |
| GridFS | Kein Base64 im Dashboard-Dokument |
| Auth | Upload nur Author; GET öffentlich für `<img>` |

## 9.7 Rate Limiting

| Limiter | Pfad | Limit |
|---------|------|-------|
| authLimiter | `/api/auth/login`, reset-password | 10 / 15 min / IP |
| sdmxLimiter | `/api/uba-sdmx/*` | 60 / min / IP |
| apiLimiter | `/api/*` (Rest) | 300 / 15 min / IP |

## 9.8 Weitere Maßnahmen

| Maßnahme | Ort |
|----------|-----|
| helmet() | HTTP-Security-Headers |
| Last-Admin-Schutz | Verhindert Aussperrung aller Admins |
| timedFetch (15s) | SDMX-Upstream-Timeout |
| Non-Root Docker | `USER node` in Dockerfile |
| CORS-Allow-List | `ALLOWED_ORIGINS` (Pflicht in Prod) |
| Kein Azure-Wildcard in Prod | Server-Start-Abbruch |

## 9.9 Threat Model (vereinfacht)

| Bedrohung | Mitigation | Residual Risk |
|-----------|------------|---------------|
| CSRF | Double-Submit | Niedrig |
| XSS (Stored) | Schema-Whitelist, kein SVG, Re-Encode | Niedrig-Mittel* |
| XSS (Reflected) | React escaping, keine innerHTML | Niedrig |
| Brute-Force Login | Rate Limiting | Mittel** |
| Session Hijacking | HTTP-Only, Secure, SameSite | Niedrig (bei HTTPS) |
| NoSQL Injection | isPlainString | Niedrig |
| DoS (große Payloads) | validatePages, Rate Limits | Mittel |
| Upstream-DoS | 15s Timeout | Niedrig |

\* Plotly rendert SVG – konfigurierte Daten aus SDMX, nicht User-HTML  
\** Kein Account-Lockout, nur IP-basiertes Rate Limit

## 9.10 Bewusst nicht implementiert

- SSO/OIDC (LDAP, Active Directory)
- 2FA/MFA
- API-Keys für Maschinen-Zugriff
- Audit-Log (wer hat wann was geändert)
- Content Security Policy im Frontend-Bundle (nur Azure SWA)

## 9.11 Production-Checklist

Siehe `docs/deployment.md`:
- `SESSION_SECRET` setzen
- `ALLOWED_ORIGINS` einschränken
- HTTPS erzwingen
- `COOKIE_SECURE=true`
- Admin-Passwort sicher setzen
- HTTP-429 monitoren
