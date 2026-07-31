# QGIS-Flood
QGIS made easy this doesn't require any high computation machine, even if you have a VV_db , VH_db file(import it as GEOtiff and use it here) you'll be able to create flooded region here effortlessly 


# SAR-Based Flood Extent Detection — Colombo

Detects flooded areas in a city using Sentinel-1 SAR imagery, excluding permanent
water bodies (rivers, lakes, reservoirs), and overlays the result on a live map.

## Overview

Flood water and other smooth, low-backscatter surfaces show up as dark regions in
Synthetic Aperture Radar (SAR) imagery. This project takes a Sentinel-1 GRD scene,
preprocesses it in SNAP, then runs a Python pipeline (built to run in a Kaggle
notebook) that thresholds the radar backscatter, cleans up noise, strips out
permanent water bodies using OpenStreetMap, and produces a final flood map as both
raster and vector layers.

**Pipeline in one line:** `SNAP (calibrate → speckle filter → terrain correct) → Python (threshold → clean → remove permanent water → polygonize → overlay)`

## Data Source

- **Sentinel-1 GRD** (VV/VH polarization), Copernicus Open Access Hub
- Area of interest: Colombo, Sri Lanka

## Part 1 — Preprocessing (SNAP)

Done in ESA SNAP before the Python pipeline:
1. Apply Orbit File
2. Radiometric Calibration → Sigma0
3. Speckle Filtering (noise reduction)
4. Range-Doppler Terrain Correction (geocoding)
5. Export as GeoTIFF

Output: a calibrated, terrain-corrected, multi-band `.tif` (linear-scale Sigma0).

## Part 2 — Flood Detection (Python / Kaggle)

See [`Flood_Detection_Pipeline_CLEAN.ipynb`](./Flood_Detection_Pipeline_CLEAN.ipynb).

| Step | What it does |
|---|---|
| 1. Load & inspect | Reads the GeoTIFF, checks band count/CRS/stats to identify the correct polarization band |
| 2. Linear → dB + crop | Converts linear Sigma0 to dB (`10·log10`), crops out the empty padding border left by terrain correction |
| 3. Threshold | Picks a dB cutoff (histogram-guided) to flag low-backscatter (water-like) pixels |
| 4. Speckle cleanup | Morphological opening/closing + small-object removal to strip salt-and-pepper noise |
| 5. Remove permanent water | Pulls rivers/lakes/reservoirs from OpenStreetMap, rasterizes them, subtracts from the mask — leaving *only* newly flooded area |
| 6. Polygonize | Converts the cleaned raster mask into vector polygons, reprojects to UTM, drops sub-500 m² noise polygons, computes total flooded area |
| 7. Export | Saves final raster + vector outputs |
| 8. Overlay | Plots flood extent on satellite imagery and an interactive map |

### Outputs

| File | Description |
|---|---|
| `Flood_Final.tif` | Binary raster (0 = dry, 1 = flooded), NoData = 0 |
| `Flood_QGIS_Overlay.tif` | Same result, single band with real NoData (255) so it drops straight into QGIS with a transparent background — no symbology workarounds needed |
| `Flood_Visual_RGBA.tif` | Red-on-transparent version with a properly tagged alpha channel, for viewers/tools that expect RGBA |
| `Flood_Extent_Vector.geojson` / `.shp` | Flood polygons with area (m²) attribute |
| `Flood_Overlay_Satellite.png` | Static image: flood extent over satellite imagery |
| `Flood_Map_Satellite.html` | Interactive, pannable/zoomable map (satellite + street toggle, optional building footprints) |

### Parameters you may need to tune

- `band_index` — which band is VV (check band stats printed in Step 1)
- `threshold` (dB) — where water separates from land in the histogram (Step 3)
- `min_size` in `remove_small_objects` — speckle-noise pixel cutoff (Step 4)
- `area_m2 > 500` — minimum polygon size kept as a real flood patch, not noise (Step 6)

## Requirements

```
rasterio
geopandas
shapely
scikit-image
scipy
osmnx
contextily
folium
```
Installed automatically in the notebook's first cell.

## How to Run

1. Upload your terrain-corrected SNAP GeoTIFF as a Kaggle dataset (or to `/kaggle/working/`).
2. Import `Flood_Detection_Pipeline_CLEAN.ipynb` into Kaggle (**File → Import Notebook**).
3. Turn on **Internet** in the notebook's session settings (needed for OSM water data + basemap tiles).
4. Set `tif_path` in the first code cell.
5. Run all cells top to bottom; adjust `band_index` and `threshold` based on the printed diagnostics.

## Results

<!--
Add screenshots below. Drop image files into a `results/` folder next to this
README, then reference them like:
![Flood extent overlay](./results/flood_overlay.png)
-->

*(Results to be added)*

## Notes & Limitations

- Permanent water removal depends on OpenStreetMap coverage in the AOI — sparse OSM
  data in some areas may leave a river/pond partially unmasked; add it manually as
  an extra polygon if so.
- The dB threshold is scene-specific — re-check the histogram for each new SAR scene
  rather than reusing a fixed value.
- SAR can also flag other low-backscatter surfaces (shadow, some paved/flat areas)
  as false positives — visual cross-check against the satellite overlay is
  recommended before treating results as ground truth.
