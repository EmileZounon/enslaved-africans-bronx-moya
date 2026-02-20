# Enslaved Africans in the Bronx — Phase 1: Georeferencing Tool

## What This Is

An interactive georeferencing tool for Moya that lets a user align a scanned
1750 colonial Bronx map on top of a modern Google Map. Phase 2 will add
enslaved African historical datapoints from census data (Excel file).

**Live:** https://emilezounon.com/enslaved-africans-bronx-moya/

---

## How to Use

1. Open `index.html` in a browser (or visit the live URL above)
2. The pre-loaded 1750 map (`BlacksInTheColonialBronx.png`) appears overlaid
   on the Bronx with 4 amber corner handles — one at each corner
3. **Drag each corner handle** to match a geographic feature on the historical
   map to the modern map (e.g. tip of a peninsula, river junction, coastline)
4. Use the **Opacity slider** (bottom bar) to blend between the historical
   overlay and the modern map as you align
5. If you upload your own scan, click **Upload Map** in the top-right
6. When aligned, click **Save Georef JSON** → downloads a JSON file with
   the 4 corner lat/lng coordinates for use in Phase 2

---

## File Structure

```
enslaved-africans-bronx-moya/
├── index.html                              ← this tool (everything in one file)
├── BlacksInTheColonialBronx.png            ← default 1750 colonial Bronx map
├── LehmanCensusDataSlavePeopleForMoya.xlsx ← Phase 2 census data (not yet used)
└── OVERLAY_MAP.md                          ← this file
```

---

## Technical Approach

### Why not `GroundOverlay`?

Google Maps' built-in `GroundOverlay` only supports rectangular bounds with no
distortion — insufficient for a hand-drawn 1750 map that has projection
differences. This tool uses a proper **4-point projective transform** instead.

### Solution: Custom `OverlayView` + CSS `matrix3d()` Perspective Warp

1. A subclass of `google.maps.OverlayView` renders an `<img>` element inside
   the map's pane layer
2. Four **draggable `AdvancedMarkerElement` handles** (amber circles) sit at
   the four corners of the historical image
3. When handles are dragged, a **homography matrix** is computed that maps
   the image's 4 corners to the 4 handle pixel positions
4. The homography is converted to a CSS `transform: matrix3d(...)` and applied
   to the `<img>` element in real time (perspective warp)
5. On every `bounds_changed` / `zoom_changed` event, the overlay redraws so
   it stays locked to the corner lat/lngs as the user pans and zooms

### Math

A 3×3 projective transform (homography H) is solved from the 4 point
correspondences using an 8×8 linear system + Gaussian elimination (~60 lines
of vanilla JS). H is then embedded into the 16-value CSS `matrix3d()` notation.

### Saved JSON Format

```json
{
  "corners": [
    { "position": "NW", "lat": 40.9176, "lng": -73.9330 },
    { "position": "NE", "lat": 40.9176, "lng": -73.7654 },
    { "position": "SE", "lat": 40.7855, "lng": -73.7654 },
    { "position": "SW", "lat": 40.7855, "lng": -73.9330 }
  ],
  "opacity": 0.6,
  "savedAt": "2026-02-20T..."
}
```

This JSON will be consumed in Phase 2 to restore the exact overlay alignment
alongside the enslaved African historical datapoints.

---

## Google Maps Configuration

- **API Key:** shared with `lenape-bronx-map`
- **Map ID:** `a9461ba5f543306b87fc900f` (same dark-theme styled map)
- **Center:** `{ lat: 40.8448, lng: -73.8648 }` — center of the Bronx
- **Initial Zoom:** 12

---

## Phase 2 (Future)

The `LehmanCensusDataSlavePeopleForMoya.xlsx` file contains enslaved African
census data for the colonial Bronx. Phase 2 will:
- Load the georef JSON to restore the 1750 map overlay automatically
- Parse the Excel data and render historical datapoints as markers on the map
- Allow filtering, searching, and exploring individual records
