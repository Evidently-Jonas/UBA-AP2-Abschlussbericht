# 08 – Editor und Tile-System

## 8.1 Editor-Überblick

Der Dashboard-Editor ist das **Kernmodul** der Anwendung. Er ermöglicht autorisierten Nutzern (Author, Admin), mehrseitige Dashboards visuell zu komponieren, Daten zu binden und zu veröffentlichen.

**Zugangswege:**
- `/de/edit/:dashboardId` – Bearbeitungsmodus
- `/de/view/:dashboardId` – Ansichtsmodus (auch für Public bei published)

## 8.2 Tile-System (Element-Registry)

### Architekturprinzip: Plugin-Registry

Jeder Tile-Typ ist ein **eigenständiges Modul** mit vier Bausteinen:

| Baustein | Datei | Aufgabe |
|----------|-------|---------|
| Store | `<Tile>Store.ts` | State, Serialisierung, Daten-Bindung |
| Renderer | `<Tile>.tsx` | React-Anzeige |
| Properties-Panel | `<Tile>PropertiesPanel.tsx` | Editor-Konfiguration |
| Definition | `<Tile>Definition.ts` | Registry-Eintrag, Default-Größen |

Registrierung in `ElementRegistry.ts` und `ElementTypes.ts`.

### Implementierte Tile-Typen (10)

| Typ | Datengebunden | Interaktiv | Beschreibung |
|-----|---------------|------------|--------------|
| `barChart` | ✅ | ✅ (Selektion) | Balkendiagramm mit Plotly |
| `donutChart` | ✅ | – | Ringdiagramm |
| `kpiTile` | ✅ | – | Kennzahl (Zahl/Text/Sparkline) |
| `yearPicker` | – | ✅ | Jahresbereich-Auswahl |
| `legend` | – | – | Legende für verknüpfte Charts |
| `intro` | – | – | Einleitungsbereich |
| `textTile` | – | – | Freitext |
| `linkTile` | – | – | Externer Link |
| `button` | – | ✅ | Aktionen (Navigation, Filter) |

Pfad: `frontend/src/editor-components/`

### Default-Größen

Seit Refactoring liegt die **einzige Quelle der Wahrheit** für Grid-Größen in der Element-Definition:

```typescript
export const BarChartDefinition = createElementDefinition({
  type: ElementTypes.barChart,
  defaultSize: { w: 6, h: 6 },
  defaultMobileSize: { w: 12, h: 8 },
  // ...
});
```

`createElementDefinition` injiziert `layout.w/h` beim Store-Erstellen.

## 8.3 Editor-UI-Module

| Modul | Pfad | Funktion |
|-------|------|----------|
| SideBar | `ui/editor/panels/SideBar` | Seiten-Navigation, Einstellungen |
| DataPanel | `ui/editor/panels/DataPanel` | Daten-Bindung, Filter |
| ElementGalleryPanel | `ui/editor/panels/ElementGalleryPanel` | Tile-Auswahl |
| Property-Inputs | `ui/editor/panels/property-inputs/` | Wiederverwendbare Eingaben |
| DashboardGridEdit | `ui/components/DashboardGridEdit` | Drag-and-Drop-Grid |
| DashboardToolbar | `ui/components/DashboardToolbar` | Undo, Redo, Save, Add |
| UnplacedElementsPanel | `ui/components/UnplacedElementsPanel` | Tiles außerhalb Grid |
| PageHero | `ui/components/PageHero` | Hero-Bereich pro Seite |

### Wiederverwendbare Property-Inputs

- `DataflowSelectPropertyInput` – Dataflow + Space
- `DimensionAggregatePropertyInput` – Dimension/Aggregation
- `FilterComponent` / `YearSelectorComponent` – Filter-UI
- `MultilingualInput` – DE/EN-Texte
- `ImagePropertyInput` – Bild-Upload
- `TileClickActionsInput` – Klick-Aktionen
- `ColorSchemePropertyInput`, `IconPropertyInput`

## 8.4 Seitenmodell

Jedes Dashboard hat **mehrere Pages**:

```typescript
interface Page {
  title: MultilingualText;
  text: MultilingualText;
  buttonText: MultilingualText;
  badgeText: MultilingualText;
  image: string | null;      // Image-ID
  heroHeight: number;
  components: Component[];
}
```

- Hero-Bereich mit Bild, Titel, Text, Badge
- Grid mit Tiles darunter
- Seiten-Navigation in Sidebar

## 8.5 Speichern und Veröffentlichen

| Aktion | Verhalten |
|--------|-----------|
| **Speichern** | Serialisiert Dashboard-JSON → `PUT /api/dashboards/:id` |
| **Veröffentlichen** | Setzt `published=true` → öffentlich sichtbar |
| **Duplizieren** | Kopie mit neuem `userId` (Aufrufer) |

Auto-Save ist **nicht** implementiert – explizites Speichern erforderlich.

## 8.6 Tastaturkürzel

Dokumentiert in `docs/editor-keyboard-controls.md`, implementiert in `KeyboardControlsService`:

- Undo/Redo
- Speichern
- Komponente hinzufügen
- Navigation

## 8.7 Erweiterbarkeit: Neuen Tile-Typ hinzufügen

Entwickler-Guide: `docs/new-tile-guide.md`

**Checkliste:**
1. Store mit `IElementStore`, `DataflowProjectionStore` (falls datengebunden)
2. Renderer mit `isDataReady`-Guard
3. Properties-Panel mit `useElementStore<T>()`
4. Definition mit `defaultSize`
5. Eintrag in `ElementTypes` + `ElementRegistryMap`
6. i18n-Keys in `de.json` / `en.json`
7. Backend-Whitelist (`ALLOWED_COMPONENT_TYPES`)
8. User-Doku in `user-docs/{de,en}/components/`

**Geschätzter Aufwand:** 1–3 Tage je nach Komplexität (mit/ohne Datenbindung).

## 8.8 Dataflow-Nutzungsbericht

Route: `/dataflow-usage`

Zeigt, welche Dashboards welche SDMX-Dataflows nutzen – hilfreich für:
- Datenpflege (welche Dataflows sind in Produktion gebunden?)
- Impact-Analyse bei Dataflow-Änderungen
- Governance und Cleanup

## 8.9 Editor-Performance

| Maßnahme | Wirkung |
|----------|---------|
| Lazy Loading | Editor-Chunk nur bei Bedarf |
| MobX computed | Aggregation nur bei Datenänderung |
| DataFetcher-Dedup | Gleicher Dataflow nur einmal geladen |
| Plotly lazy | Charts rendern on-demand |

## 8.10 Bekannte Limitierungen

- Kein Multi-User-Editing (kein Locking)
- Keine Versionierung (nur letzter Stand)
- Kein Template-System (Dashboard-Vorlagen)
- Mobile-Editing eingeschränkt (View optimiert, Edit Desktop-fokussiert)
