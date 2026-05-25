# Assessing the Transferability of Machine Learning Models for Operational Open-Pit Mine Monitoring Using Sentinel-2 Imagery

## Overview

This project investigates whether machine learning models trained to identify operational mining disturbance at one open-pit mine can generalise to a geographically and geologically distinct mine site. Sentinel-2 imagery was used to classify disturbed mining land and surrounding background terrain at Chuquicamata, Chile, and Mount Whaleback, Australia.

The project focuses on model transferability: a key issue for operational Earth observation workflows. If a model performs well only at the site where it was trained, its usefulness for scalable mine monitoring is limited. This project therefore tests direct cross-site transfer, investigates spectral domain shift using PCA and feature importance analysis, and evaluates whether limited site-specific training data can improve model generalisation.

## Research Question

Can a Random Forest model trained to classify operational mining disturbance at one open-pit mine generalise effectively to another mine with different geological, environmental and spectral characteristics?

## Study Sites

Two large open-pit mining environments were selected:

- **Chuquicamata, Chile** – a major copper mine located in the Atacama Desert.
- **Mount Whaleback, Australia** – a large iron ore mine in the Pilbara region of Western Australia.

These sites were chosen because they represent contrasting geological and spectral environments, making them suitable for testing cross-site transferability.

## Data

![Remote sensing workflow overview](key%20figs/remote_sensing_workflow_overview.jpeg)
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

### 1. Site-specific Random Forest models performed well

Random Forest models trained and tested within the same mine site were able to classify broad operational disturbance patterns using Sentinel-2 spectral features. This demonstrated that the feature stack contained useful information for separating disturbed mining surfaces from surrounding background terrain.

**Chuquicamata local classification**

![Chuquicamata local Random Forest classification](key%20figs/Local_RF_chuq.png)

**Mount Whaleback local classification**

![Mount Whaleback local Random Forest classification](key%20figs/Local_RF_whale.png)

---

### 2. Direct cross-site transferability was poor

When the Chuquicamata-trained model was applied directly to Mount Whaleback, the model substantially overpredicted disturbed land. This suggests that models trained on one mining environment do not automatically generalise to a geologically and spectrally distinct site.

![Direct transfer result](key%20figs/direct_transfer_result.png)

---

### 3. Feature importance suggested spectral dependence varied between mine sites

Feature importance analysis was used to identify which Sentinel-2 bands and derived indices contributed most strongly to each local Random Forest model. NDVI was the most important feature for both mine sites, suggesting that vegetation absence and bare surface conditions were key drivers of disturbance classification.

**Chuquicamata feature importance**

![Chuquicamata feature importance](key%20figs/Chuq_FI.png)

**Mount Whaleback feature importance**

![Mount Whaleback feature importance](key%20figs/Whale_FI.png)

---

### 4. PCA revealed cross-site domain shift

Principal Component Analysis showed that Chuquicamata and Mount Whaleback occupied different regions of the multispectral feature space. This suggests that site-specific spectral differences were stronger than the disturbed/background class separation alone, helping to explain the poor direct transferability.

![PCA domain shift](key%20figs/PCA_domain_shift.png)

---

### 5. Mixed-site training improved cross-mine transferability

To reduce domain shift, a limited amount of labelled data from the target mine was added to the training dataset. This mixed-site adaptation substantially improved Macro F1-score in both transfer directions.

![Transferability improvement](key%20figs/transfer_improvement.png)

The mixed-site classification maps also showed improved spatial coherence compared with direct transfer alone.

**Chuquicamata mixed-site classification**

![Chuquicamata mixed-site RF classification](key%20figs/mixed_site_chuq.png)

**Mount Whaleback mixed-site classification**

![Mount Whaleback mixed-site RF classification](key%20figs/mixed_site_RF.png)

## Environmental Cost and Sustainability Assessment

This project considered the environmental cost of the research workflow in two ways: the computational cost of running the machine learning analysis, and the wider environmental relevance of scalable satellite-based mine monitoring.

### Computational cost of the workflow

The modelling workflow used a Random Forest classifier rather than a deep learning model. This was a deliberate choice because Random Forests are computationally lightweight, interpretable and suitable for tabular pixel-based spectral features. The models trained quickly in Google Colab and did not require prolonged GPU usage or large-scale hyperparameter tuning.

The main computational steps were:

- loading and resampling Sentinel-2 bands
- calculating NDVI and BSI
- rasterising polygon labels
- training Random Forest models
- running PCA and feature importance analysis

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
### Environmental relevance of mine monitoring

Although mining is environmentally disruptive, satellite-based monitoring can support better environmental management. An automated EO workflow could help identify:

- expansion of disturbed land
- growth of waste dumps or tailings areas
- rehabilitation progress
- unexpected or unlicensed disturbance
- areas requiring closer inspection

In this sense, the environmental cost of computation must be balanced against the potential benefit of improved monitoring and accountability. A lightweight, reproducible model using open satellite data may support more frequent monitoring without requiring repeated site visits or expensive aerial surveys.

### Limitations of the environmental assessment

This project did not perform a full carbon audit of cloud computing, satellite infrastructure or data storage. The assessment is therefore qualitative rather than a precise emissions calculation. Future work could improve this by recording runtime, hardware type, energy use and estimated carbon emissions using tools such as CodeCarbon or CarbonTracker.

## How to Run

This project was developed in Google Colab.

To reproduce the workflow:

1. Open `finalproject.ipynb` in Google Colab.
2. Mount Google Drive.
3. Ensure the Sentinel-2 ZIP files and GeoJSON label files are stored in the expected project folder.
4. Run the notebook cells sequentially from top to bottom.

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

## Contact

Author: Talal Khan
Email: talal.khan.25@ucl.ac.uk
Institution: University College London
Course: GEOL0069 - AI for Earth Observation (AI4EO)

## Acknowledgements

This project was created for GEOL0069 at University College London, taught by Dr. Michel Tsamados.
