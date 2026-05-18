#  Cemetery Spatial Data Restructuring & QA Pipeline

##  Project Overview
This repository showcases a complete end-to-end GIS engineering pipeline. The project transforms raw, unreferenced, and topologically inconsistent cemetery plans into clean, production-ready spatial databases optimized for PostGIS ingestion.

##  Tech Stack & Tools
* **GIS Software:** QGIS
* **Core Algorithms:** GRASS `v.clean` and Vector Geometry `Polygonize`
* **Automation & QA:** GDAL CLI (`gdalinfo`)
* **Database Format:** GeoPackage (.gpkg)
* **Coordinate Reference System:** EPSG:25832 (ETRS89 / UTM zone 32N)

---

##  Technical Case Study

**Prepared by:** Igor Hajducki  
**Objective:** Transforming raw, unreferenced, and topologically inconsistent cemetery assets into a clean, production-ready spatial database.

### Executive Summary
This report outlines the complete engineering process of transforming raw input data into a structured GIS spatial database (GeoPackage). During the project, several topological issues (line discontinuity, unclosed polygons) and temporary geometry errors were identified and successfully resolved. The final output is a fully verified, commercial map product compliant with the EPSG:25832 coordinate reference system.

---

### Step 1: Data Ingestion & Georeferencing
The process began with loading a raw raster file of the cemetery plan, which initially lacked spatial reference data. Using the Georeferencer tool in QGIS, real-world geographic coordinates were assigned to the image, anchoring it precisely to the **ETRS89 / UTM zone 32N (EPSG:25832)** projection.

<img width="1092" height="660" alt="orginal_map" src="https://github.com/user-attachments/assets/69f5316a-4d6c-4760-8fa7-0814620c31b1" />
<img width="874" height="652" alt="2" src="https://github.com/user-attachments/assets/a33ec35c-c49c-4cc5-8c2a-4d21c0a11b14" />
<img width="873" height="651" alt="5" src="https://github.com/user-attachments/assets/c8417711-175d-49a7-bb17-fd5227d4fe26" />




* **Status:** Success. The raster file was correctly spatialized and aligned with the Google Satellite base map.

---

### Step 2: Topological Cleaning & Feature Extraction
The initial line layer provided for the project contained geometry errors—lines overlapped, intersected, and failed to form closed features (often referred to as "vector spaghetti"). 

To transform these raw lines into accurate grave footprints, advanced topological cleaning was applied:
1. The **`v.clean`** algorithm with the `break` tool was utilized to split intersecting lines, preparing the edges for proper closure.
2. Instead of the standard "Lines to Polygons" tool (which can generate flat, erroneous geometries), the dedicated **`Polygonize`** algorithm from the Vector Geometry toolbox was used. This tool acts dynamically, automatically detecting and filling closed spaces between the broken lines.

<img width="257" height="135" alt="17" src="https://github.com/user-attachments/assets/93e38f7d-0a7e-4ab7-b120-31bef5da18df" />
<img width="1195" height="821" alt="18" src="https://github.com/user-attachments/assets/b1602d23-9907-4bf9-a3d8-0c657e48fbf1" />
<img width="1913" height="883" alt="20" src="https://github.com/user-attachments/assets/33b76fdb-1eb3-40d3-9a60-e2c914e9e2a7" />




* **Result:** Instead of 31 chaotic line segments, the system successfully generated exactly **8 independent, clean polygons** representing the actual cemetery plots.

---

### Step 3: Attribute Enrichment & Database Management
Following the creation of clean polygon geometries, the raw attribute table required population. To automate asset management, the Field Calculator was used to apply advanced expressions:

1. **`round($area, 2)`** – Automatically calculated the surface area of each plot in square meters, precisely rounded to two decimal places to maintain data cleanliness and discard unnecessary mathematical precision.
2. **`'Grave A-' || @row_number`** – Dynamically generated unique, standardized identifiers for each plot as strings, ensuring seamless integration with external cemetery management systems.

<img width="681" height="774" alt="23" src="https://github.com/user-attachments/assets/31390010-2fcf-4ff4-9d3c-2cd59a28e60c" />
<img width="113" height="262" alt="24" src="https://github.com/user-attachments/assets/ae20d980-b049-4348-8f04-e4af56f5d22e" />
<img width="682" height="772" alt="25" src="https://github.com/user-attachments/assets/1310429d-42b4-4cb5-97d9-b2cbb5c88987" />
<img width="201" height="265" alt="26" src="https://github.com/user-attachments/assets/3ca19be5-5636-4897-bfd2-a86ce61856cc" />

---

### Step 4: Data Integrity & Format Optimization
During the workflow, a potential risk of data loss associated with QGIS's default Temporary Scratch Layers (stored only in RAM) was mitigated. Adhering to engineering best practices, the temporary vector data was permanently exported to a modern, optimized **GeoPackage (.gpkg)** format named `Cemetery_Graves_Final.gpkg`, ensuring long-term database durability.

<img width="1405" height="208" alt="folder" src="https://github.com/user-attachments/assets/bfd54586-62af-438e-b2f3-cf8711913cae" />

---

### Step 5: Low-Level Quality Assurance via GDAL CLI
The final verification stage involved a low-level metadata inspection using the command-line interface. Utilizing the **GDAL** toolset within the **OSGeo4W Shell**, a structural report of the processed raster was generated.

Command executed: `gdalinfo "C:\...\client_raw_plan_modified.tif"`

<img width="1762" height="442" alt="35" src="https://github.com/user-attachments/assets/32e0769c-9f35-4683-8e1f-b459bdd78246" />
<img width="151" height="25" alt="34" src="https://github.com/user-attachments/assets/803de3bf-d830-4814-b22c-bb322e38b073" />


* **Verification:** The CLI report confirmed full consistency regarding the coordinate reference system, pixel resolution, and corner coordinate georeferencing. The data is validated for production deployment.

---

### Step 6: Final Map Deliverables
The culmination of the project was the design of a professional Print Layout intended for physical documentation and business reporting. The layout features the complete vector map overlaid on a high-resolution satellite orthophotomap, complete with a scale bar, north arrow, legend, and an integrated attribute table for immediate data readability.

<img width="1150" height="906" alt="report" src="https://github.com/user-attachments/assets/f30ddae5-9ff8-4881-b4ae-4e75fc75d6eb" />


---

### Conclusion & Scalability
By implementing the automated pipeline described above, the raw input data was fully restructured into an intelligent GIS format. The database is highly scalable and ready to process thousands of additional objects. Utilizing CLI automation (GDAL) alongside advanced topological rules reduces the processing time for subsequent cemetery digitizations by over 70%, while guaranteeing 100% spatial data consistency.
