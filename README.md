# 🌲 Forest Health Assessment Using NDVI, EVI, and LAI

A Google Earth Engine (GEE) script that computes a composite Forest Health Index (FHI) by combining Sentinel-2 NDVI, EVI and MODIS LAI. This README explains what the script does, how to use it, and how to customize it.

---

## ✨ Table of Contents
- 📌 [Overview](#-overview)
- 🛠️ [Prerequisites](#️-prerequisites)
- ▶️ [How to Run](#️-how-to-run)
- 🧭 [Script Overview (section-by-section)](#-script-overview-section-by-section)
- ⚙️ [Parameters & Customization](#️-parameters--customization)
- 📝 [Notes & Best Practices](#-notes--best-practices)
- 📤 [Exporting Results](#-exporting-results)
- 📞 [Contact & Credits](#-contact--credits)
- 📜 [License](#-license)

---

## 📌 Overview
This script:
- Loads Sentinel-2 surface reflectance and computes NDVI & EVI.
- Loads MODIS LAI and handles empty collections by substituting a zero image if needed.
- Aggregates mean NDVI, mean EVI and mean LAI across a user-defined AOI and time range.
- Combines indices into a single Forest Health Index (FHI) with configurable weights.
- Visualizes the FHI on the map and adds a categorical legend.
- Optionally exports the FHI image to Google Drive.

---

## 🛠️ Prerequisites
- Google Earth Engine account (https://earthengine.google.com/)
- An AOI asset or a FeatureCollection path in your GEE assets (update the `aoi` variable).
- Basic familiarity with the GEE Code Editor.

---

## ▶️ How to Run
1. Open the GEE Code Editor: https://code.earthengine.google.com/
2. Create a new script.
3. Copy the script (provided below) into the editor.
4. Update the `aoi` variable to your AOI asset path or replace with a geometry.
5. Adjust `startDate`, `endDate`, and other parameters as needed.
6. Run the script. Use the Tasks panel to run the Export if desired.

---

## 🧭 Script Overview (section-by-section)
Each heading below corresponds to the same headings in the script (with symbols added for clarity).

- 🔹 Step 1: Define Area of Interest (AOI)
  - Set `aoi` to a FeatureCollection/asset or geometry. Map is centered to AOI.

- 🔹 Step 2: Define Time Period
  - Configure `startDate` and `endDate` for the analysis period.

- 🔹 Step 3: Load Sentinel-2 Image Collection
  - Loads `COPERNICUS/S2_SR`, filtered by AOI, date, and cloud threshold.

- 🔹 Step 4: Calculate NDVI and EVI
  - Adds NDVI (B8, B4) and EVI (B8, B4, B2) bands to each image.

- 🔹 Step 5: Load MODIS LAI Dataset and handle empty collection
  - Loads `MODIS/006/MCD15A3H` 'Lai' band and clips to AOI.
  - If no LAI images are available in the date range, the script uses a default zero image, avoiding errors in downstream processing.

- 🔹 Step 6: Calculate Mean NDVI, EVI over AOI
  - Computes mean maps for NDVI and EVI across the filtered Sentinel-2 collection.

- 🔹 Step 7: Visualize Forest Health Indices (commented — optional)
  - Contains example visualization params (NDVI, EVI, LAI) you can enable.

- 🔹 Step 8: Combine Indices into Forest Health Index (FHI)
  - Weighted linear combination:
    - NDVI weight = 0.4
    - EVI weight  = 0.3
    - LAI weight  = 0.3 (LAI normalized by dividing by 6)

- 🔹 Step 9: Add Categorical Legend for Forest Health Index
  - Adds an interactive legend panel with Low / Medium / High categories.

- 🔹 Step 10: Export Results (Optional)
  - Export `fhi` to Google Drive with configurable `scale` and `region`.

---

## ⚙️ Parameters & Customization
- AOI: replace `var aoi = ee.FeatureCollection('projects/gee-trial2/assets/Shapfile/WMH_Distric');`
- Date range: `startDate`, `endDate`
- Cloud threshold: `filter(ee.Filter.lt('CLOUDY_PIXEL_PERCENTAGE', 20))`
- Weights (inside FHI): change NDVI/EVI/LAI multipliers
- LAI normalization factor: currently `divide(6)` — change according to expected LAI range
- Map palettes: adjust `palette` arrays in Map.addLayer calls

Example: to emphasize NDVI more, change weights:
fhi = meanNDVI.multiply(0.5).add(meanEVI.multiply(0.25)).add(meanLAI.divide(6).multiply(0.25));

---

## 📝 Notes & Best Practices
- The script gracefully handles an empty LAI collection by using a zero image. This prevents script errors when MODIS LAI is unavailable for your date range or AOI.
- Sentinel-2 surface reflectance (S2_SR) is subject to cloud/shadow masking for more accurate indices—consider adding cloud masking for robust results.
- LAI from MODIS is coarser resolution; when combining with Sentinel-2, normalize LAI and be aware of spatial resolution differences.
- Validate weights: the current weighting (NDVI 40%, EVI 30%, LAI 30%) is illustrative — tune based on field knowledge.

---

## 📤 Exporting Results
- The default Export uses:
  - description: 'Forest_Health_Index'
  - scale: 30
  - region: aoi
  - maxPixels: 1e13
- Start the task from the GEE Tasks tab after running the script.


---

## 🏆 Author

Tejas Chavan  
* GIS Expert at Government Of Maharashtra Revenue & Forest Department  
* tejaskchavan22@gmail.com  
* +91 7028338510  

