# Enslaved Africans in the Bronx — Georeferencing Tools

## What This Is

Two interactive georeferencing tools for Moya that align a scanned 1750 colonial
Bronx map on top of a modern Google Map. Phase 2 will add enslaved African
historical datapoints from census data (Excel file).

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
├── index.html                              ← simple 4-corner warp (first draft)
├── georef-precise.html                     ← multi-point mesh warp (precise)
├── BlacksInTheColonialBronx.png            ← 1750 colonial Bronx map
├── LehmanCensusDataSlavePeopleForMoya.xlsx ← Phase 2 census data (not yet used)
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

## Phase 2 (Future)

The `LehmanCensusDataSlavePeopleForMoya.xlsx` file contains enslaved African
census data for the colonial Bronx. Phase 2 will:
- Load the georef JSON to restore the 1750 map overlay automatically
- Parse the Excel data and render historical datapoints as markers on the map
- Allow filtering, searching, and exploring individual records
