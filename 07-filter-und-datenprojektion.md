# 07 – Filter und Datenprojektion

## 7.1 Konzeptuelle Einordnung

Die Filter-Architektur implementiert ein **Projektionsmodell**: Rohdaten aus SDMX werden nicht direkt in Charts gerendert, sondern durch einen Projektions-Layer transformiert. Dieser Layer wendet Filter, Aggregationen und Selektionen an.

**Vorteile:**
- Einheitliche Filterlogik für alle Tile-Typen
- Globale Filter wirken dashboardübergreifend ohne Tile-Kopplung
- Testbare, isolierte Datenverarbeitung
- Charts zeigen Kontext (Dimmen) statt hartem Ausblenden

## 7.2 Filterarten

### Globale Filter (dashboardweit)

- **Quelle:** `FilterStore` (`frontend/src/stores/editor/FilterStore.ts`)
- **Auslöser:** z. B. Klick/Selektion in Bar-Chart
- **Wirkung:** Alle Tiles, deren Dataflow die gefilterte Dimension enthält
- **Persistenz:** Session-lokal (nicht im Dashboard-JSON gespeichert)

### Lokale Filter (pro Tile)

- **Quelle:** `DataflowProjectionStore.filters`
- **Auslöser:** Konfiguration im Editor-Datenpanel
- **Wirkung:** Nur das jeweilige Tile
- **Persistenz:** Im Dashboard-JSON serialisiert

### Jahr-Filter (Zeitbereich)

- **Quelle:** `yearSelection` in `ProjectionFilters`
- **Auslöser:** `YearPicker`-Tile oder Property-Input
- **Wirkung:** Schneidet Observations auf Zeitraum

## 7.3 Projektions-Layer (Komponenten)

| Komponente | Pfad | Verantwortung |
|------------|------|---------------|
| `FilterStore` | `stores/editor/FilterStore.ts` | Globale Filter, Year-Range |
| `DataflowStore` | `stores/editor/DataflowStore.ts` | Rohdaten, kein Filtering |
| `ProjectionDataSource` | `stores/editor/projection/ProjectionDataSource.ts` | dataflowId, space, baseData |
| `ProjectionFilters` | `stores/editor/projection/ProjectionFilters.ts` | Filteranwendung |
| `ProjectionSelection` | `stores/editor/projection/ProjectionSelection.ts` | Selektion, Sync mit FilterStore |
| `ProjectionAggregation` | `stores/editor/projection/ProjectionAggregation.ts` | seriesResult, kpiValue |

## 7.4 Filterreihenfolge

In `ProjectionFilters.filteredData`:

```
1. Year-Selection (lokal)
2. Lokale Dimensions-Filter
3. Globale Dimensions-Filter
```

**Regeln:**
- Globale Filter nur, wenn Dimension im Dataflow vorhanden
- `ignoreGlobalDimensions` verhindert Selbst-Filtering beim selektierenden Chart
- Mehrere globale Filter wirken gleichzeitig (UND-Verknüpfung)

## 7.5 Interaktionsmodell: Bar-Chart-Selektion

```
Nutzer klickt Balken „Verkehr"
    ↓
PlotlySelectionHelper erkennt Selektion
    ↓
ProjectionSelection.setSelection(dimension, memberId)
    ↓
FilterStore.setGlobalFilter(dimension, memberId)
    ↓
Alle ProjectionFilters reagieren (MobX computed)
    ↓
Andere Tiles filtern auf „Verkehr"
Bar-Chart dimmt nicht-ausgewählte Balken (ignoreGlobalDimensions)
```

**Nutzerfreundlichkeit:** Das selektierende Chart behält alle Kategorien sichtbar (visueller Kontext), während andere Tiles hart filtern.

## 7.6 Aggregation

`ProjectionAggregation` berechnet aus gefilterten Daten:

| Output | Verwendung |
|--------|------------|
| `seriesResult` | Linien-/Balkendiagramme (eine Serie) |
| `multiSeriesResult` | Mehrere Serien (z. B. gestapelte Balken) |
| `kpiValue` | KPI-Kacheln (Einzelwert) |
| `kpiLabel` | Beschriftung des KPI |

Aggregationsmodus (Summe, Durchschnitt, letzter Wert etc.) ist pro Tile konfigurierbar.

## 7.7 Theoretische Einordnung

Das Modell entspricht Konzepten aus **Visual Analytics**:

- **Brushing & Linking** – Selektion in einem View beeinflusst andere Views
- **Focus + Context** – Dimmen statt Ausblenden im Selektions-Chart
- **Data Cube Operations** – Filter (Slice/Dice) vor Aggregation (Roll-up)

Es ist keine vollständige OLAP-Engine, sondern eine **domänenspezifische Vereinfachung** für SDMX-Dimensionen.

## 7.8 Grenzen des aktuellen Modells

| Grenze | Auswirkung |
|--------|------------|
| Nur UND-Verknüpfung | Kein ODER-Filter zwischen Dimensionen |
| Keine negierten Filter | „Alles außer X" nicht möglich |
| Keine berechneten Dimensionen | Nur SDMX-native Dimensionen |
| Globale Filter nicht persistiert | Gehen bei Reload verloren |
| Kein Filter-Undo | Nur über Chart-Doppelklick löschbar |

## 7.9 Erweiterungsmöglichkeiten

- Persistierte globale Default-Filter im Dashboard-JSON
- Filter-History/Reset-Button in Toolbar
- OR-Verknüpfung für Member innerhalb einer Dimension
- URL-Parameter für shareable Filterzustände
- Filter-Vorschau im Editor (welche Tiles betroffen)
