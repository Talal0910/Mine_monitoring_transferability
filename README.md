# Assessing the Transferability of Machine Learning Models for Operational Open-Pit Mine Monitoring Using Sentinel-2 Imagery

## Overview

This project investigates whether machine learning models trained to identify operational mining disturbance at one open-pit mine can generalise to a geographically and geologically distinct mine site. Sentinel-2 imagery was used to classify disturbed mining land and surrounding background terrain at Chuquicamata, Chile, and Mount Whaleback, Australia.

The project focuses on model transferability: a key issue for operational Earth observation workflows. If a model performs well only at the site where it was trained, its usefulness for scalable mine monitoring is limited. This notebook therefore tests direct cross-site transfer, investigates spectral domain shift using PCA and feature importance analysis evaluating whether limited site-specific training data can improve model generalisation.

## Research Question

Can a Random Forest model trained to classify operational mining disturbance at one open-pit mine generalise effectively to another mine with different geological, environmental and spectral characteristics?

## Study Sites

Two large open-pit mining environments were selected:

- **Chuquicamata, Chile** – a major copper mine located in the Atacama Desert.
- **Mount Whaleback, Australia** – a large iron ore mine in the Pilbara region of Western Australia.

These sites were chosen because they represent contrasting geological and spectral environments, making them suitable for testing cross-site transferability.

## Data

Sentinel-2 Level-2A imagery was used because it provides atmospherically corrected surface reflectance data suitable for land-surface monitoring.

The following bands were used:

| Band | Description |
|---|---|
| B02 | Blue |
| B03 | Green |
| B04 | Red |
| B08 | Near Infrared |
| B11 | SWIR 1 |
| B12 | SWIR 2 |

Two derived spectral indices were also calculated:

- **NDVI** – used to represent vegetation presence or absence.
- **BSI** – used to represent exposed bare ground and disturbed surface conditions.

## Methodology

The workflow consisted of the following steps:

1. Sentinel-2 imagery was downloaded for both mine sites.
2. Bands were loaded, resampled and aligned to a common spatial grid.
3. NDVI and BSI were calculated and added to the feature stack.
4. Training polygons were manually annotated using Folium.
5. GeoJSON polygons were rasterised into pixel-based labels.
6. Random Forest classifiers were trained using labelled pixels.
7. Local and cross-site transferability experiments were conducted.
8. Feature importance and PCA were used to interpret model behaviour.
9. Mixed-site training was tested as a transferability improvement strategy.

## Key Results

Direct transfer between mine sites performed poorly, indicating limited cross-site generalisation. However, mixed-site training using a small amount of labelled data from the target mine substantially improved performance.

## Environmental Cost and Sustainability Assessment

This project considered the environmental cost of the research workflow in two ways: the computational cost of running the machine learning analysis, and the wider environmental relevance of scalable satellite-based mine monitoring.

### Computational cost of the workflow

The modelling workflow used a Random Forest classifier rather than a deep learning model. This was a deliberate choice because Random Forests are computationally lightweight, interpretable and suitable for tabular pixel-based spectral features. The models trained quickly in Google Colab and did not require prolonged GPU usage or large-scale hyperparameter tuning.

The main computational steps were:

- loading and resampling Sentinel-2 bands,
- calculating NDVI and BSI,
- rasterising polygon labels,
- training Random Forest models,
- running PCA and feature importance analysis.

Compared with CNNs, U-Nets or transformer-based Earth observation models, this workflow has a relatively low computational footprint. This reduces energy demand and makes the approach more accessible for small research projects or operational teams without high-performance computing resources.

### Data efficiency

The project used freely available Sentinel-2 Level-2A imagery. Sentinel-2 provides global, regularly updated surface reflectance data, meaning no additional airborne surveys or field campaigns were required to collect imagery for this experiment.

However, satellite data is not environmentally cost-free. Earth observation systems depend on satellite manufacturing, launch infrastructure, data storage, processing and cloud distribution. The environmental cost of this project is therefore not zero, but the marginal cost of using existing open-access Sentinel-2 imagery is relatively low compared with collecting new bespoke imagery.

### Annotation and retraining cost

A key motivation for investigating transferability is that labelled training data is expensive to produce. If every new mine site requires a fully new set of manual labels and a separately trained model, the workflow becomes less efficient and less scalable.

The direct transfer experiments showed that models trained on one mine did not generalise well to another. However, the mixed-site training results showed that adding a limited amount of target-site data substantially improved performance. This suggests a more efficient operational approach:

```text
train a base model
+ add limited local labels
→ improve transferability at a new mine site
```

## How to Run section

```markdown
## How to Run

This project was developed in Google Colab.

To reproduce the workflow:

1. Open `finalproject.ipynb` in Google Colab.
2. Mount Google Drive.
3. Ensure the Sentinel-2 ZIP files and GeoJSON label files are stored in the expected project folder.
4. Run the notebook cells sequentially from top to bottom.
```
Required Python libraries include:

- rasterio
- geopandas
- numpy
- matplotlib
- scikit-learn
- folium
- pandas

## Limitations and Future Work


Key limitations include:

- Sentinel-2 spatial resolution may miss smaller operational features.
- Manual polygon labels introduce some subjectivity.
- Only two mine sites were analysed.
- The model classifies broad disturbance rather than separating pits, waste dumps, haul roads and infrastructure.

Future work could include testing additional mines, using higher-resolution imagery, separating operational feature classes, and exploring domain adaptation or deep learning segmentation approaches.
