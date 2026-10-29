# traverse-maps

Map data for the [Traverse](https://github.com/dbudnikau-personal/traverse) iPhone app: a catalog of **feature cells** — the lifts,
pistes, ski areas and roads of a 0.25° × 0.25° box, as GeoJSON — that the app fetches when someone downloads an offline map of an area, so
the downloaded map also knows which parts of a track were a lift ride, a run or a drive.

This repository holds **release assets only**; there is no source here. The cells are built by `ios/Scripts/mappack/build-cells.py` in the
app's repository and uploaded by hand.

## What is where

| Release tag | Asset | What it is |
|---|---|---|
| `catalog` | `catalog.json` | The list of cells that exist. Replaced on every build. The app reads this one URL (`TRAVERSE_MAP_CATALOG_URL`). |
| `cells-<yyyymmdd>` | `<id>.geojson` | One cell per file, named by its south-west corner: `c<lat×4>_<lon×4>` (so `c167_179` is the box whose south-west corner is 41.75° N, 44.75° E; negative values take a leading `m`, e.g. `cm1_m280`). |

`catalog.json` is `{"format": "traverse-cells/1", "cellDegrees": 0.25, "cells": [...]}`; each row carries the cell's `id`, `bbox`
(`[west, south, east, north]`), asset `url`, `bytes`, `sha256`, the two source snapshot dates, the OSM `highwayClasses` the cell keeps and its
`attribution`. A cell is at most 5,000,000 bytes. A cell file is a GeoJSON `FeatureCollection` of curated features (`properties.layer` is
`lift`, `run`, `skiArea` or `highway`); a feature that crosses a cell's edge is kept whole in every cell it touches.

## Licence and attribution

The data is **derived from OpenStreetMap** and is therefore made available under the
[Open Database Licence (ODbL) 1.0](https://opendatacommons.org/licenses/odbl/1-0/) — this repository's cells are a derivative database, and the
share-alike terms of the ODbL apply to them: if you use them, you must keep this notice and attribute the sources, and a database you derive
from them is also under the ODbL.

- **© OpenStreetMap contributors** — the lifts, pistes, ski areas and roads originate from OpenStreetMap
  (<https://www.openstreetmap.org/copyright>). Roads are read from the Overpass API.
- **OpenSkiMap.org** — lift, piste and ski-area records come from OpenSkiMap's open exports
  (<https://openskimap.org>), which are themselves derived from OpenStreetMap.

Each catalog row repeats these credits in its `attribution` field, and the app prints them on every map that uses a cell.

## Using the cells

Everything needed is in the catalog: fetch `catalog.json`, pick the cells whose `bbox` meets your area, fetch each `url`, and check its
`sha256`. Several cells may carry the same feature; the identity of a feature is (`properties.layer`, `properties.id` — or `osmId` for a
highway). There is no API and no guarantee of availability: the assets are regenerated when the app's maps are refreshed.
