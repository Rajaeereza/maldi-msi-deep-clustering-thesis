# Unsupervised Deep Clustering of MALDI-MSI Ion Images

**Master Thesis · Physics of Data · University of Padova · 2023–2024**  
Reza Rajaee · Supervisor: Prof. Jacopo Pegoraro · Co-examiner: Prof. Alberto Testolin

📄 [Official thesis record — University of Padova](https://thesis.unipd.it/handle/20.500.12608/66544)

---

## Context

This work was carried out during an internship at **Datrix S.p.A., Milan**, in collaboration with the **Istituto Nazionale dei Tumori (INT), Milan**, which provided the clinical tissue dataset.

The project applies unsupervised deep learning to Mass Spectrometry Imaging (MALDI-MSI) data, with the goal of clustering ion images to reveal molecular co-localisation patterns relevant to cancer diagnostics and biomarker discovery.

---

## Datasets

**Dataset 1 — Mouse urinary bladder** (public)  
Benchmark MALDI-MSI dataset ([Römpp et al., 2010](https://doi.org/10.1002/anie.200905559)), available via the PRIDE repository: [PXD001283](https://www.ebi.ac.uk/pride/archive/projects/PXD001283)

**Dataset 2 — Human lung tissue** (proprietary)  
Provided by the Istituto Nazionale dei Tumori (INT), Milan. Not publicly available.

---

## Methods implemented

### Existing methods (reimplemented as baselines)

- **K-Means** — vectorisation-based clustering applied to MALDI-MSI ([Alexandrov et al., 2010](https://doi.org/10.1021/pr100734z))
- **GMM** — Gaussian Mixture Model clustering applied to MALDI-MSI ([Abdelmoula et al., 2021](https://doi.org/10.1038/s41467-021-21055-8))
- **Xception + K-Means** — Xception ([Chollet, 2017](https://doi.org/10.1109/CVPR.2017.195)) pretrained on ImageNet as feature extractor, followed by K-Means
- **Noise-robust deep clustering network** — the primary deep learning baseline ([Guo et al., 2024](https://doi.org/10.1093/bioinformatics/btad067)), combining a convolutional autoencoder for denoising with a CNN trained using pairwise pseudo-labels and adaptive self-paced learning
- **CNN-only variant** — the clustering CNN component of the above network in isolation, excluding its denoising stage (not evaluated in the original paper — introduced here as an ablation to isolate the contribution of denoising)

### Novel contributions

- **Denoise-K-Means** — convolutional autoencoder denoising step prepended to K-Means
- **Denoise-GMM** — convolutional autoencoder denoising step prepended to GMM
- **Denoise-Xception** — convolutional autoencoder denoising step prepended to Xception + K-Means

The denoising autoencoder architecture follows the design introduced in Guo et al. (2024). Applying it as a transferable preprocessing component to other clustering methods, and studying its isolated effect, is the core novel contribution of this work.

---

## Experiments

### Dataset 1 — Mouse urinary bladder

**Preprocessing** was performed using the **Cardinal R package** ([Bemis et al., 2015](https://doi.org/10.1093/bioinformatics/btv146)), following the pipeline described in Guo et al. (2024), including TIC normalisation, peak detection, peak filtering, and peak binning. Data was exported to imzML format for Python-based analysis.

**Hyperparameter tuning** — a systematic grid search over CNN architecture depth, learning rate, and training epochs for both the denoising and clustering stages of the noise-robust deep clustering network. This analysis was not performed in the original paper and constitutes an independent methodological contribution.

**Comparative evaluation** — all implemented methods were compared at the optimal configuration found during tuning. Evaluation used isotope-based clustering metrics (isotope ratio and relative isotope ratio) and cluster homogeneity analysis.

---

### Dataset 2 — Human lung tissue

**Custom Python preprocessing pipeline** — a full reimplementation of Cardinal-style MALDI preprocessing in Python, covering:

- Smoothing (multiple methods)
- Baseline correction (multiple methods)
- TIC normalisation
- Peak picking with multiple noise estimation strategies (multiple methods)
- Peak filtering
- Rebinning (multiple methods)

**Comparative evaluation** — this pipeline enabled a systematic comparison of preprocessing choices across all clustering methods, producing a comprehensive study of how preprocessing decisions affect clustering outcomes on noisy clinical MALDI data.

---

## Pipeline overview

![Research pipeline](pipeline.png)

---

## Key questions addressed

- Does adding a denoising step improve clustering performance for methods beyond the original noise-robust network?
- What is the isolated contribution of the CNN clustering stage, separate from denoising?
- How sensitive are clustering results to hyperparameter choices in the noise-robust deep clustering network?
- How significantly do preprocessing choices affect clustering outcomes on noisy clinical MALDI data?

---

## Findings

The denoise-Xception method achieved clustering performance comparable to 
the state-of-the-art noise-robust deep clustering network. On the mouse 
urinary bladder dataset it matched the baseline method's performance; on 
the human lung tissue dataset it surpassed it in several preprocessing 
conditions, demonstrating the strength of Xception-based feature extraction 
when combined with effective denoising.

The preprocessing comparison on the human lung dataset revealed that each 
method — smoothing, baseline correction, normalisation, and peak picking — 
produces meaningfully different clustering outcomes. These variations affect 
the reliability and interpretability of results, making preprocessing choice 
a critical and often underestimated factor in MALDI-MSI analysis workflows.

Across both datasets, the denoising step was consistently the decisive 
factor in clustering quality — more so than the choice of clustering 
architecture itself. This points toward denoising as a transferable and 
high-value component for future MALDI-MSI analysis pipelines.

---

## Technical skills

| Area | Details |
|---|---|
| Deep learning | Convolutional autoencoders · Xception · adaptive self-paced training · pairwise pseudo-labelling |
| Unsupervised learning | K-Means · GMM · deep clustering · hyperparameter grid search |
| Signal processing | MALDI spectral preprocessing: smoothing · baseline correction · normalisation · peak picking · rebinning |
| Scientific Python | pyimzML · NumPy · SciPy · scikit-learn · custom reimplementation of Cardinal preprocessing pipeline |
| R / Bioinformatics | Cardinal R package for MSI data import, binning, and imzML export |
| Evaluation design | Isotope-based clustering metrics · silhouette analysis · cluster homogeneity analysis |
| Medical imaging | MALDI-MSI data structure · ion images · mass spectra · m/z space |

---

## References

- Guo D, Föll MC, Bemis KA, Vitek O. *A noise-robust deep clustering of biomolecular ions improves interpretability of mass spectrometric images.* Journal of Computational Biology, 2024. — **Primary deep clustering baseline**
- Chollet F. *Xception: Deep learning with depthwise separable convolutions.* CVPR, 2017. — **Feature extraction architecture**
- Bemis KD, Harry A, Eberlin LS, Ferreira C, van de Ven SM, Mallick P, Stolowitz M, Vitek O. *Cardinal: an R package for statistical analysis of mass spectrometry-based imaging experiments.* Bioinformatics, 31(14), 2418–2420, 2015. — **Preprocessing package**
- Alexandrov T, Becker M, Deininger S, Ernst G, Wehder L, Grasmair M, von Eggeling F, Thiele H, Maass P. *Spatial segmentation of imaging mass spectrometry data with edge-preserving image denoising and clustering.* Journal of Proteome Research, 9, 6535–6546, 2010. — **K-Means on MALDI-MSI**
- Abdelmoula WM, Lopez BG-C, Randall EC, Kapur T, Sarkaria JN, White FM, Agar JN, Wells WM, Agar NYR. *Peak learning of mass spectrometry imaging data using artificial neural networks.* Nature Communications, 12, 2021. — **GMM on MALDI-MSI**
- Römpp A, Guenther S, Schober Y, Schulz O, Takats Z, Kummer W, Spengler B. *Histology by mass spectrometry: label-free tissue characterization obtained from high-accuracy bioanalytical imaging.* Angewandte Chemie International Edition, 49, 3834–3838, 2010. — **Mouse urinary bladder dataset**
- Savitzky A, Golay MJE. *Smoothing and differentiation of data by simplified least squares procedures.* Analytical Chemistry, 36(8), 1627–1639, 1964. — **Smoothing**
- Ryan CG, Clayton E, Griffin WL, Sie SH, Cousens DR. *SNIP, a statistics-sensitive background treatment for the quantitative analysis of PIXE spectra in geoscience applications.* Nuclear Instruments and Methods B, 34(3), 396–402, 1988. — **Baseline correction**
- Zhang W, Claesen M, Moerman T, Groseclose MR, Waelkens E, De Moor B, Verbeeck N. *Spatially aware clustering of ion images in mass spectrometry imaging data using deep learning.* Analytical and Bioanalytical Chemistry, 413, 2803, 2021.

---

## Repository note

This repository documents the research methodology and experimental design of the thesis. The original implementation was developed during an internship at Datrix S.p.A., Milan. Code and datasets are not included here; the human lung dataset is proprietary clinical data from INT Milan.

---

*Master thesis completed July 2024.*
