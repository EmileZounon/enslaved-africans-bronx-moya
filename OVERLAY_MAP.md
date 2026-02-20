# Enslaved Africans in the Bronx — Interactive Historical Map

## What This Is

An interactive map project for Moya combining a georeferenced 1750 colonial Bronx
map overlay with census data showing enslaved African people in the region (1698–1771).
Includes two georeferencing tools and a census data viewer.

**Live:** https://emilezounon.com/enslaved-africans-bronx-moya/

---

## Two Tools

### 1. `index.html` — Simple 4-Corner Warp (First Draft)

A quick-start tool with 4 draggable corner handles. Uses a CSS `matrix3d()`
projective transform (homography). Good for a rough first pass.

- 4 amber corner handles at NW, NE, SE, SW
- Drag corners to align the map
- Opacity slider to blend overlay
- Save/Load georef JSON

### 2. `georef-precise.html` — Multi-Point Mesh Warp (Precise)

An advanced tool with **14 draggable control pins** placed on identifiable
geographic features (peninsula tips, river mouths, islands, borders). Uses
**Delaunay triangulation** to create a mesh of triangles, each with its own
affine transform. This corrects for internal distortions in the hand-drawn map
that a single 4-corner warp cannot fix.

**Control points (pre-set from research):**

| Pin | Feature | Image Pixel | Modern Lat/Lng | Source |
|-----|---------|-------------|----------------|--------|
| Throgs Neck Tip | Fort Schuyler peninsula | (546, 649) | 40.805, -73.795 | NOAA tidal benchmark |
| Clason Point | Clason Point Park | (285, 656) | 40.806, -73.849 | USGS GNIS |
| Hunts Point | Barretto Point Park | (160, 649) | 40.804, -73.883 | USGS cape feature |
| Mott Haven | Port Morris waterfront | (85, 654) | 40.808, -73.926 | Google Maps |
| Bronx River Mouth | Soundview Park | (228, 590) | 40.809, -73.858 | Bronx River Alliance |
| Spuyten Duyvil | Harlem/Hudson junction | (110, 185) | 40.875, -73.918 | Wikipedia coords |
| Riverdale / Yonkers | NW border at Hudson | (135, 92) | 40.908, -73.904 | City boundary |
| Eastchester / Mt Vernon | NE border | (440, 82) | 40.898, -73.820 | City boundary |
| City Island | City Island center | (500, 260) | 40.847, -73.786 | USGS |
| Hart Island | Hart Island center | (645, 280) | 40.852, -73.769 | USGS |
| + 4 image corner pins | Full image coverage | corners | estimated | extrapolated |

**How the mesh warp works:**
1. Delaunay triangulation creates ~20 triangles from the 14 points
2. For each triangle, an affine transform maps image pixels → screen pixels
3. Canvas 2D clips to each triangle and draws the transformed image region
4. Redraws on every pan/zoom/drag to stay aligned

---

## How to Use `georef-precise.html`

1. Open in a browser (or visit the live URL + `/georef-precise.html`)
2. The 1750 map appears overlaid on the modern Bronx at ~60% opacity
3. **10 colored pins** sit on identifiable features:
   - **Red** = coastal peninsula tips (Throgs Neck, Clason Point, Hunts Point, Mott Haven)
   - **Blue** = river features (Bronx River mouth, Spuyten Duyvil)
   - **Green** = northern borders (Riverdale, Eastchester)
   - **Orange** = islands (City Island, Hart Island)
   - **Gray** = image corners (for full coverage)
4. Drag any pin to fine-tune alignment of that area
5. Use the **Opacity slider** to blend overlay
6. Toggle **Show Mesh** to see the Delaunay triangulation wireframe
7. Click a control point in the left panel to zoom to it
8. Click **Save Georef JSON** to export all pin positions for Phase 2

---

## File Structure

```
enslaved-africans-bronx-moya/
├── phase2.html                             ← CENSUS DATA VIEWER (Phase 2)
├── index.html                              ← simple 4-corner warp (first draft)
├── georef-precise.html                     ← multi-point mesh warp (precise)
├── BlacksInTheColonialBronx.png            ← 1750 colonial Bronx map
├── LehmanCensusDataSlavePeopleForMoya.xlsx ← original census data (Excel)
├── GEOREF_RESEARCH.md                      ← detailed research on map features
└── OVERLAY_MAP.md                          ← this file
```

---

## Google Maps Configuration

- **API Key:** shared with `lenape-bronx-map`
- **Map ID:** `a9461ba5f543306b87fc900f`
- **Center:** `{ lat: 40.8448, lng: -73.8648 }` — center of the Bronx
- **Initial Zoom:** 12

---

## Phase 2: Census Data Viewer (`phase2.html`)

Displays enslaved African census records (1698–1771) as interactive markers on
the map, combined with the 1750 colonial Bronx overlay.

### Data Source

Parsed from `LehmanCensusDataSlavePeopleForMoya.xlsx` (Lehman College). **17 records**
across 10 census years and 7 colonial towns. 202 people named individually; the
1771 county-wide count reaches 3,430.

### Colonial Town → Modern Location Mapping

| Colonial Town | Modern Area | Approx. Lat/Lng |
|---|---|---|
| Town of Westchester | Westchester Square | 40.839, -73.843 |
| Fordham | Fordham / Bronx Zoo area | 40.861, -73.889 |
| Morrisania | Morrisania neighborhood | 40.828, -73.905 |
| East Chester | Eastchester / Mt Vernon | 40.890, -73.825 |
| Yunkers (Yonkers) | Yonkers / Riverdale border | 40.900, -73.900 |
| Pelham | Pelham Bay area | 40.858, -73.805 |
| Westchester County | Center of Bronx (aggregate) | 40.845, -73.865 |

### Features

- **1750 map overlay** with mesh warp (same rendering as `georef-precise.html`)
- **Load saved georef JSON** to restore Moya's pin alignment
- **Opacity slider** and show/hide toggle for the overlay
- **Red dashed route** connecting all 7 colonial towns (toggleable on/off)
- **Census markers**: circle markers with enslaved count, color-coded by era
  - Gold dot = names are recorded in this census entry
- **Popup tooltips**: click any marker on the map to open a tooltip bubble
  - Multiple tooltips can be open simultaneously for comparison
  - Shows town name, year, count, and up to 8 name previews
  - "+ N more names..." link opens the full detail card
- **Liquid glass info card**: click a record in the left panel or "more names"
  link to open a glassmorphism detail card (backdrop blur, semi-transparent)
  - Desktop: × close button, card positioned bottom-right
  - Mobile: "Back" button that returns to the records drawer
- **Active marker glow**: pulsing yellow highlight shows which marker is selected
- **Year filter buttons** → show only markers from a specific census year
- **Left panel**: summary stats, overlay controls, route toggle, town legend
  with click-to-zoom, scrollable record list
- **Contextual notes** on info cards (e.g. earliest recorded names, distinguishing
  descriptors like "Samson Blind" or "Long Peter")

### How to Use

1. Open `phase2.html` in a browser (or visit the live URL)
2. The 1750 overlay appears at 50% opacity with census markers on top
3. Click year buttons to filter by census year
4. Click markers on the map to open popup tooltips (multiple at once)
5. Click records in the left panel to open the full detail card
6. Toggle the red route line or overlay on/off in the left panel
7. Adjust overlay opacity with the slider
