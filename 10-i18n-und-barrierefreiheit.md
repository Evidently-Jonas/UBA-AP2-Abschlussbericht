# 10 – Internationalisierung und Barrierefreiheit

## 10.1 Internationalisierung (i18n)

### Zwei Ebenen der Mehrsprachigkeit

| Ebene | Mechanismus | Persistenz |
|-------|-------------|------------|
| **UI-Texte** | react-i18next | `frontend/src/i18n/locales/de.json`, `en.json` |
| **Dashboard-Inhalte** | `MultilingualText` | MongoDB (pro Feld: `{ de: "...", en: "..." }`) |

### MultilingualText-Typ

```typescript
type MultilingualText = Record<string, string>;
// Beispiel: { de: "Luftqualität", en: "Air Quality" }
```

Betroffene Felder: Dashboard-Name, Page-Titel, Tile-Titel, KPI-Labels, Tag-Labels, CTA-Texte u. a.

### Fallback-Strategie

`getLocalizedText(text, lang)` in `frontend/src/i18n/Multilingual.ts`:

1. Gewählte Sprache
2. Fallback Deutsch
3. Erste verfügbare Sprache
4. Leerer String

### Sprach-Routing

URLs enthalten Sprachpräfix: `/de/dashboard-overview`, `/en/dashboard-overview`

- `LanguageStore` + `localStorage` für Präferenz
- Browser-Spracherkennung beim ersten Besuch
- Language Switcher im Header (DE/EN)

Details: `docs/language-routing.md`, `docs/i18n.md`

### Editor: Multilinguale Eingaben

| Komponente | Verwendung |
|------------|------------|
| `MultilingualInput` | Einzeilige Texte mit SegmentedControl |
| `MultilingualTextarea` | Mehrzeilige Texte |
| `MultilingualTagsInput` | Badge-Arrays |

### Erweiterbarkeit

Neue Sprache hinzufügen (z. B. Französisch):
1. `fr.json` erstellen
2. `config.ts` erweitern
3. LanguageSwitcher + MultilingualInput anpassen
4. Bestehende Dashboard-Inhalte manuell übersetzen

**Aktuell:** Nur DE/EN vollständig unterstützt.

## 10.2 Barrierefreiheit (a11y)

### Strategie

Die Anwendung verfolgt eine **testgetriebene Accessibility-Strategie** mit jest-axe (WCAG-orientiert), nicht eine formale Zertifizierung.

### Testabdeckung (~77 a11y-Tests)

Pfad: `frontend/src/test/a11y.test.tsx`

**Geprüfte Bereiche:**

| Kategorie | Komponenten/Seiten |
|-----------|-------------------|
| Muster | Skip-Link, Landmarks, aria-label, Form-Labels |
| Seiten | Login, Public Dashboards, Overview, Admin Detail, Accessibility Statement |
| Komponenten | Header, Footer, Sidebar, Cards, Modals, Tables, Toolbar, Gallery |

### Implementierte a11y-Maßnahmen

| Maßnahme | Beispiel |
|----------|----------|
| Skip-Link | Direkt zur Hauptnavigation |
| Landmarks | `main`, `nav`, `footer` |
| ARIA-Labels | Icon-Buttons, Chart-Regionen |
| Form-Labels | `htmlFor` + `id`-Verknüpfung |
| Chart-Zugänglichkeit | `role="img"` + `aria-label` |
| Tabellen | Sortierbare Spalten mit ARIA |
| Dialoge | Mantine Modal mit Fokus-Trap |
| Tastaturkürzel | Editor-Shortcuts dokumentiert |

### Barrierefreiheitserklärung

Eigene Seite: `/:lang/accessibility` – erfüllt BITV/WCAG-Transparenzanforderungen für öffentliche Behörden-Websites.

### Bekannte Einschränkungen

| Bereich | Status |
|---------|--------|
| Editor-Grid | Nicht in a11y-Tests |
| Editor-Sidebar | Nicht in a11y-Tests |
| Plotly-Charts | Teilweise (role=img); keine vollständige Daten-Tabelle als Alternative |
| Farbkontrast | Nicht systematisch gemessen (axe prüft teilweise) |
| Screenreader im Editor | Nicht evaluiert |

### axe-Regel-Ausnahmen

Manche Tests deaktivieren `heading-order` und `link-name` bei legitimen Layout-Gründen (Icon-Links, Hero-Hierarchie).

## 10.3 Mobile Responsiveness

- Responsive Grid (Desktop 12 Spalten, Mobile volle Breite)
- `defaultMobileSize` pro Tile-Definition
- Eigene Mobile-Styles in `PublicDashboards.css`
- User-Doku: `user-docs/de/mobile/`

**Editor:** Desktop-fokussiert; Mobile-Viewing optimiert.

## 10.4 Bedienerdokumentation als a11y-Ressource

Die VitePress-Doku (`user-docs/`) ist:
- Zweisprachig (DE/EN)
- Mit Screenshots für visuelle Anleitung
- PDF-exportierbar (Playwright + pdf-lib)
- Eigenständig gehostet (eigener Container)

## 10.5 Empfehlungen für Bericht-Evaluation

Für eine wissenschaftliche Evaluation der Barrierefreiheit könnten ergänzt werden:

1. **Manuelle Tests** mit Screenreadern (NVDA, VoiceOver)
2. **Farbkontrast-Analyse** (WCAG 2.1 AA: 4.5:1)
3. **Tastatur-Only-Test** durch gesamten Editor-Workflow
4. **Nutzerstudie** mit Menschen mit Behinderungen
5. **Vergleich** mit BITV 2.0 / EN 301 549 Checkliste
