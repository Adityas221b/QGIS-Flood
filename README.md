# 🌊 SAR-Based Flood Extent Detection using Sentinel-1 & QGIS

A flood mapping workflow that detects inundated regions from Sentinel-1 SAR imagery,
removes permanent water bodies (rivers, lakes, reservoirs), and generates clean flood
extent maps for visualization and GIS analysis.

---

## 📍 Study Areas

This repository contains flood mapping results for multiple districts in Sri Lanka.

- Gampaha
- Kalutara
- Matara
- Ratnapura

---

## 🛰 Data Source

- Sentinel-1 GRD (VV/VH)
- Copernicus Open Access Hub
- ESA SNAP
- OpenStreetMap (Permanent Water Bodies)

---

```

---
```
# SNAP Preprocessing

The SAR image is preprocessed using ESA SNAP.

1. Apply Orbit File
2. Radiometric Calibration
3. Speckle Filtering
4. Terrain Correction
5. Export GeoTIFF

---

# Flood Detection Pipeline

The Python notebook performs the following operations.

| Step | Operation |
|------|-----------|
| 1 | Read GeoTIFF |
| 2 | Convert Sigma0 → dB |
| 3 | Threshold low-backscatter pixels |
| 4 | Morphological filtering |
| 5 | Remove rivers & lakes using OpenStreetMap |
| 6 | Polygonize flood regions |
| 7 | Calculate flooded area |
| 8 | Export raster + vector outputs |

Notebook:

```
Flood_Detection_Pipeline_CLEAN.ipynb
```

---

# Outputs

| Output | Description |
|---------|-------------|
| Flood_Final.tif | Binary Flood Mask |
| Flood_QGIS_Overlay.tif | Transparent QGIS Overlay |
| Flood_Visual_RGBA.tif | RGBA Visualization |
| Flood_Extent_Vector.geojson | Flood Polygons |
| Flood_Overlay_Satellite.png | Satellite Overlay |
| Flood_Map_Satellite.html | Interactive Map |

---

# 📊 Results

## Gampaha

![Gampaha Flood](Result/gampaha_result.png)

---

## Kalutara

![Kalutara Flood](Result/kaluthara_result.png)

---

## Matara

![Matara Flood](Result/matara_result.png)

---

## Ratnapura

![Ratnapura Flood](Result/ratnapura_result.png)

---

# Repository Structure



```
```

# Installation


pip install rasterio geopandas shapely scikit-image scipy osmnx contextily folium
```

---
```
# How to Run

1. Download Sentinel-1 GRD imagery.
2. Preprocess in ESA SNAP.
3. Export GeoTIFF.
4. Upload to Kaggle.
5. Run `Flood_Detection_Pipeline_CLEAN.ipynb`.
6. Adjust threshold if required.
7. Export flood maps.

---

# Limitations

- Thresholds vary across SAR scenes.
- Results depend on OpenStreetMap water-body completeness.
- SAR shadows and smooth urban surfaces can produce false positives.
- Visual verification is recommended before operational use.

---

# Technologies

- Sentinel-1 SAR
- ESA SNAP
- QGIS
- Python
- Rasterio
- GeoPandas
- OSMnx
- Folium
- Scikit-Image

---

# Author

**Aditya Singh**

BITS Pilani

Computer Science
