# 06 – SDMX-Datenintegration

## 6.1 Hintergrund: SDMX-Standard

**SDMX** (Statistical Data and Metadata eXchange) ist ein internationaler Standard für den Austausch statistischer Daten und Metadaten. Er wird von Eurostat, OECD, IWF und nationalen Statistikämtern eingesetzt.

Kernelemente:
- **Dataflow** – logische Datenlieferung (z. B. Treibhausgasemissionen)
- **DSD** (Data Structure Definition) – Schema mit Dimensionen
- **Codelist** – erlaubte Werte pro Dimension (oft hierarchisch)
- **Key** – Positionsstring, der eine Observation identifiziert
- **Observation** – einzelner Messwert zu einem Zeitpunkt

## 6.2 UBA SDMX-API

**Basis-URL:** `https://daten.uba.de/{space}/rest`

| Space | Zweck |
|-------|-------|
| `release` | Produktive, veröffentlichte Daten (Default) |
| `design` | Entwicklungs-/Testdaten |

### Typische Endpunkte

```
GET /dataflow/UBA/all/latest?detail=allstubs&format=sdmx-json
GET /dataflow/UBA/{ID}/{VERSION}/all?detail=full&references=descendants
GET /data/UBA,{ID},{VERSION}/{KEY}?startPeriod=…&endPeriod=…&dimensionAtObservation=AllDimensions&format=sdmx-json
```

## 6.3 Architektur der Integration

### Backend als Proxy (BFF-lite)

Das Frontend ruft **nicht direkt** `daten.uba.de` auf, sondern den eigenen Backend-Proxy:

```
Frontend → GET /api/uba-sdmx/dataflows/:id → UbaSdmxService → daten.uba.de
```

**Gründe für den Proxy:**
1. **Caching** – SDMX-Keys und Strukturen sind teuer zu berechnen
2. **Einheitliche API** – JSON-Format statt rohes SDMX-XML/JSON
3. **Timeout-Kontrolle** – 15s hartes Limit
4. **Rate Limiting** – Schutz vor Missbrauch
5. **CORS-Unabhängigkeit** – funktioniert auch wenn UBA-CORS restriktiver wird

### UbaSdmxService

Pfad: `backend/src/services/UbaSdmxService.ts`

**Kernfunktionen:**
1. Dataflow-Liste abrufen und parsen
2. DSD + Codelists laden (`references=descendants`)
3. SDMX-Key konstruieren aus Dimensionen
4. Observations abrufen und anreichern
5. Parent-Codes für Hierarchien ergänzen (`enrichDataWithCodeParents`)

**Caching:**
| Cache | Key | TTL |
|-------|-----|-----|
| Key-Cache | `${space}:${dataflowId}` | 24h |
| Structure-Cache | `${space}:${dataflowId}` | 24h |

Admin-Endpunkte: `GET /cache/stats`, `DELETE /cache`

### SDMX-Parsing

Pfad: `backend/src/services/sdmx-parsers.ts`

- XML-Parsing mit `fast-xml-parser`
- Extraktion: Dimensionen, Codelists, Code-Hierarchien (`<structure:Parent>`)
- Mehrsprachige Namen (`extractName` mit `lang`-Parameter)

## 6.4 Frontend-Datenpipeline

```
DataflowProjectionStore
  ├── dataSource: Dataflow-Bindung, space, isDataReady
  ├── filters: lokale + globale Filter, yearSelection
  ├── aggregation: seriesResult, kpiValue, multiSeriesResult
  └── selection: Chart-Selektion, Dimmen

DataFetcher (MobX Reaction)
  → DataApiService.getDataflow(id, space, periods)
  → DataflowStore.setData(id, space, data)
```

### Space-Persistenz

- Jede Dataflow-Bindung speichert `space: 'release' | 'design'`
- Alte Dashboards ohne Space: Default `release`
- UI: Auswahl in `DataflowSelectPropertyInput`

## 6.5 SDMX-POC als Vorarbeit

Pfad: `poc-sdmx-explorer/`

Der POC erkundet die SDMX-API **direkt im Browser** (ohne Backend):

| Aspekt | POC | Hauptanwendung |
|--------|-----|----------------|
| API-Zugriff | Browser-direkt | Backend-Proxy |
| Caching | Keins (Session) | 24h Backend-Cache |
| UI | Minimal (keine Library) | Mantine + Plotly |
| Filter-UI | Hierarchische Trees | Editor Property-Panels |
| Auth | Keine | Session + Rollen |

**Erkenntnisse aus dem POC:**
- CORS auf `daten.uba.de` ist für Browser-Zugriff freigegeben
- Hierarchische Codelists erfordern Parent-Parsing
- Key-Konstruktion ist fehleranfällig → zentraler Service sinnvoll
- `DOMParser` reicht für Struktur-XML (POC); Produktion nutzt `fast-xml-parser`

## 6.6 Datenfluss-Beispiel

**Szenario:** KPI-Kachel zeigt CO₂-Emissionen 2022 für Sektor „Verkehr"

1. Autor bindet `DF_CLIMATE_EMISSIONS` (release) an KPI-Tile
2. Lokaler Filter: Dimension `SECTOR` = `TRANSPORT`
3. Year-Selection: 2022
4. Beim Laden: `DataFetcher` → Backend → SDMX-Key `.A.TRANSPORT....GHG.T_CO2_EQ`
5. `ProjectionAggregation` berechnet `kpiValue` aus gefilterten Observations
6. `KpiTile` rendert formatierte Zahl

## 6.7 Herausforderungen und Lösungen

| Herausforderung | Lösung |
|-----------------|--------|
| Komplexe SDMX-Keys | Backend berechnet Key, cached Ergebnis |
| Hierarchische Codes | Parent-Anreicherung in `enrichDataWithCodeParents` |
| Große Dataflows | Zeitraumfilter (`startPeriod`/`endPeriod`) |
| Design vs. Release | Space-Parameter durchgängig |
| Langsame Upstream-API | 24h-Cache + 15s-Timeout |
| Mehrsprachige Labels | `lang`-Query an SDMX-API |

## 6.8 Verknüpfung mit UBA Data Cube

Das Frontend kann Deep-Links in den Data Cube generieren (`datacubeUrl.ts`), sodass Nutzer von einem Dashboard in die vollständige Cube-Visualisierung wechseln können – mit vorausgefüllten Filtern.

**Architekturentscheidung:** Dashboarding = kuratierte Narrative; Data Cube = explorative Analyse. Komplementär, nicht konkurrierend.

## 6.9 Offene technische Punkte

- Cache ist pro Backend-Instanz (nicht distributed)
- Keine inkrementellen Updates (immer Full-Fetch pro Dataflow)
- Keine Metadaten-Versionierung (latest-Version hardcoded als Fallback)
- SDMX-JSON vs. SDMX-XML: Backend unterstützt beides je nach Endpoint
