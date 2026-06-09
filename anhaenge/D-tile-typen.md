# Anhang D – Tile-Typen (Dashboard-Komponenten)

Alle Tiles sind unter `frontend/src/editor-components/` implementiert und in `ElementRegistry.ts` registriert.

---

## Übersicht

| Typ | Name (DE) | Datengebunden | Interaktiv | Default-Größe (Desktop) |
|-----|-----------|:-------------:|:----------:|:----------------------:|
| `barChart` | Balkendiagramm | ✅ | ✅ | 6×6 |
| `donutChart` | Donut-Diagramm | ✅ | – | – |
| `kpiTile` | KPI-Kachel | ✅ | – | – |
| `yearPicker` | Jahresauswahl | – | ✅ | – |
| `legend` | Legende | – | – | – |
| `intro` | Intro-Bereich | – | – | – |
| `textTile` | Text-Kachel | – | – | – |
| `linkTile` | Link-Kachel | – | – | – |
| `button` | Button | – | ✅ | – |

---

## Detaillierte Beschreibungen

### barChart – Balkendiagramm

**Pfad:** `editor-components/bar-chart/`

| Aspekt | Detail |
|--------|--------|
| Bibliothek | Plotly.js |
| Daten | `DataflowProjectionStore` → `aggregation.seriesResult` / `multiSeriesResult` |
| Interaktion | Klick-Selektion → globaler Filter |
| Besonderheit | Dimmt nicht-ausgewählte Balken (Focus+Context) |
| Properties | Dataflow, Dimension, Aggregation, Farbschema, Titel |

**Dateien:** `BarChartStore.ts`, `BarChart.tsx`, `BarChartPropertiesPanel.tsx`, `BarChartDefinition.ts`, `PlotlySelectionHelper.ts`

---

### donutChart – Donut-Diagramm

**Pfad:** `editor-components/donut-chart/`

| Aspekt | Detail |
|--------|--------|
| Bibliothek | Plotly.js |
| Daten | `aggregation.seriesResult` |
| Interaktion | Keine Selektion |
| Properties | Dataflow, Dimension, Farbschema |

---

### kpiTile – KPI-Kachel

**Pfad:** `editor-components/kpi-tile/`

| Aspekt | Detail |
|--------|--------|
| Anzeigemodi | Zahl (`NumberMode`), Text (`TextMode`), Sparkline (`SparklineMode`) |
| Daten | `aggregation.kpiValue`, `filters.kpiLabel` |
| Properties | Dataflow, Aggregation, Icon, Farbe, Display-Mode |

**Dateien:** `kpiComputations.ts` für Berechnungslogik

---

### yearPicker – Jahresauswahl

**Pfad:** `editor-components/year-picker/`

| Aspekt | Detail |
|--------|--------|
| Funktion | Setzt `yearSelection` in `ProjectionFilters` |
| Wirkung | Schneidet Observations auf gewählten Zeitraum |
| Interaktion | Nutzer wählt Start-/Endjahr |
| Datenbindung | Keine direkte SDMX-Bindung |

---

### legend – Legende

**Pfad:** `editor-components/legend/`

| Aspekt | Detail |
|--------|--------|
| Funktion | Zeigt Farbzuordnung verknüpfter Charts |
| Interface | `IChartLegendSource` für Chart-Verknüpfung |
| Datenbindung | Keine |

---

### intro – Intro-Bereich

**Pfad:** `editor-components/intro/`

| Aspekt | Detail |
|--------|--------|
| Funktion | Einleitungstext, oft auf erster Seite |
| Inhalte | Mehrsprachiger Titel und Text |
| Datenbindung | Keine |

---

### textTile – Text-Kachel

**Pfad:** `editor-components/text-tile/`

| Aspekt | Detail |
|--------|--------|
| Funktion | Freitext, Erklärungen, Fußnoten |
| Inhalte | Mehrsprachiger Text |
| Datenbindung | Keine |

---

### linkTile – Link-Kachel

**Pfad:** `editor-components/link-tile/`

| Aspekt | Detail |
|--------|--------|
| Funktion | Externer Link mit Icon und Beschreibung |
| Inhalte | URL, Titel, Beschreibung (mehrsprachig) |
| Datenbindung | Keine |

---

### button – Button

**Pfad:** `editor-components/button-tile/`

| Aspekt | Detail |
|--------|--------|
| Funktion | Klickbare Aktion |
| Aktionen | Navigation (Seite), Filter setzen/löschen |
| Konfiguration | `TileClickActionsInput` |
| Datenbindung | Keine |

---

## Architektur pro Tile

Jeder Tile folgt dem **4-Bausteine-Muster**:

```
<TileName>/
├── <TileName>Store.ts        # State, Serialisierung, dataProps
├── <TileName>.tsx            # React-Renderer
├── <TileName>PropertiesPanel.tsx  # Editor-Konfiguration
└── <TileName>Definition.ts   # Registry-Eintrag, defaultSize
```

### Store-Interface

```typescript
interface IElementStore<TDataProps> {
  id: string;
  type: ElementTypes;
  title: MultilingualText;
  layout: Layout;
  dataProps: TDataProps;
  serialize(): object;
  deserialize(data: object): void;
}
```

### Datenbindung (datengebundene Tiles)

```typescript
dataProps = {
  series: new DataflowProjectionStore(
    "dataflow.properties.series",
    DataPropertyType.SERIES,
  ),
};
```

`DataflowProjectionStore` kapselt:
- `dataSource` – Dataflow-ID, Space, Ladezustand
- `filters` – lokale/globale Filter
- `aggregation` – berechnete Werte
- `selection` – Chart-Selektion

---

## Erweiterung: Neuen Tile hinzufügen

Siehe `docs/new-tile-guide.md` und Kapitel 08.

**Pflichtschritte:**
1. Store + Renderer + Panel + Definition
2. `ElementTypes` + `ElementRegistryMap`
3. `ALLOWED_COMPONENT_TYPES` im Backend
4. i18n-Keys
5. User-Doku
