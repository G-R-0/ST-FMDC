# ST-FMDC

ST-FMDC is a foundation-model-driven multi-modal framework for spatial gene expression prediction from H&E histology images.

---

## Overview

This repository provides the implementation of **ST-FMDC**, a multi-modal framework for predicting spatial gene expression from H&E histology images. The framework is designed to jointly model histological morphology, transcriptomic information, and cell-type semantics for biologically informed spatial transcriptomics analysis.

Existing methods for this task often face three major challenges:  
(1) insufficient biologically meaningful cross-modal alignment between morphology and gene expression,  
(2) limited modeling of long-range spatial dependencies in complex tissue microenvironments, and  
(3) reduced robustness under severe cell-type and gene-expression imbalance.

To address these issues, ST-FMDC integrates four main components:

- **MMFE**: a Multi-Modal Foundation Extractor for visual, molecular, and semantic representation learning
- **DA-CLIP**: a Dual-Aligned CLIP module for biologically meaningful cross-modal alignment
- **MNA**: a Mamba-based Neighboring Aggregator for long-range spatial dependency modeling
- **TGE-CS**: a tail-aware gene expression and cell-type supervision strategy for robust optimization under imbalanced data distributions

This codebase supports training, evaluation, and downstream analysis on multiple public and in-house spatial transcriptomics datasets, including melanoma, breast cancer, and cSCC cohorts, with optional external validation on datasets such as **10x Visium HD** and independent **in-house H&E cohorts**.

In the preprocessing pipeline, we additionally incorporate:

- **cell2location** for spatial cell-type annotation
- **BioMedCLIP** for text feature extraction from cell-type descriptions
- **scFoundation** for single-cell transcriptomic feature extraction

---

## Framework

ST-FMDC is designed to integrate morphology, molecular expression, and cell-type semantics in a unified learning pipeline.

### Main characteristics

- biologically informed cross-modal alignment between histology, gene expression, and cell-type text
- long-range spatial context modeling with a Mamba-based neighboring aggregator
- robust optimization under imbalance via tail-aware gene expression supervision and auxiliary cell-type guidance
- flexible integration of pretrained foundation models for both semantic and transcriptomic representation learning

---

## Repository Structure

```text
ST-FMDC/
├── configs/                  # configuration files
├── data/
│   ├── raw/                  # raw data
│   ├── processed/            # processed data for training/evaluation
│   └── external/             # optional external validation datasets
├── preprocessing/
│   ├── cell2location/        # cell-type annotation pipeline
│   ├── biomedclip/           # text feature extraction
│   └── scfoundation/         # single-cell feature extraction
├── models/                   # model definitions
├── training/                 # training scripts
├── evaluation/               # evaluation scripts
├── visualization/            # visualization and downstream analysis
├── environment.yml           # conda environment definition
└── README.md
```

---

## Environment

We recommend using **Conda** or **Miniconda** to create the environment from the provided `environment.yml`.

### Create the environment

```bash
conda env create -f environment.yml
conda activate ST-FMDC
```

### Environment Summary

The provided environment is configured with:

- **Python 3.12.11**
- **CUDA Toolkit 11.8**
- **PyTorch 2.6.0**
- **torchvision 0.21.0**
- **torch-geometric 2.6.1**
- **open-clip-torch 3.2.0**
- **transformers 4.56.2**
- **scanpy 1.11.4**
- **anndata 0.12.2**
- **scikit-learn 1.7.2**
- **pandas 2.3.3**
- **matplotlib 3.10.6**
- **umap-learn 0.5.9.post2**

The environment also includes CUDA compilation support (`cuda-nvcc=11.8.89`) and the scientific Python stack required for preprocessing, training, evaluation, and visualization.

### Notes

- The default environment name is **`mamba_env`**.
- The current environment file is configured for a **Linux + CUDA 11.8** setup.
- If your server or workstation uses a different CUDA runtime, compiler toolchain, or driver version, you may need to modify `environment.yml`.
- For CPU-only execution, remove CUDA-specific dependencies and install the corresponding CPU version of PyTorch.

---

## Installation

Clone the repository and create the environment:

```bash
git clone <your-repo-url>
cd /ST-FMDC
conda env create -f environment.yml
conda activate ST-FMDC
```

If the project is organized as a Python package, it can also be installed in editable mode:

```bash
pip install -e .
```

---

## Data Preprocessing

Before training ST-FMDC, the datasets should be organized into a unified format containing:

- H&E histology images
- spatial coordinates
- spot-level or region-level gene expression matrices
- train / validation / test splits
- optional cell-type labels or cell-type text descriptions

### 1. Cell-type annotation with cell2location

We use **cell2location** to estimate spatial cell-type abundance by integrating spatial transcriptomics data with a single-cell RNA-seq reference.

Official tutorial:  
[https://cell2location.readthedocs.io/en/latest/notebooks/cell2location_tutorial.html](https://cell2location.readthedocs.io/en/latest/notebooks/cell2location_tutorial.html)

In our workflow, the inferred cell-type information is used to construct biologically informed semantic descriptions for downstream multimodal alignment.

### 2. Text feature extraction with BioMedCLIP

We use **BioMedCLIP** to extract semantic embeddings from cell-type descriptions or prompts.

Official model page:  
[https://huggingface.co/ZiyueWang/biomedclip/tree/main](https://huggingface.co/ZiyueWang/biomedclip/tree/main)

These text embeddings are used as semantic guidance for cross-modal alignment in ST-FMDC.

### 3. Single-cell feature extraction with scFoundation

We use **scFoundation** to obtain transcriptomic foundation features from single-cell data.

Official repository:  
[https://github.com/biomap-research/scFoundation](https://github.com/biomap-research/scFoundation)

In our workflow, scFoundation features serve as prior molecular representations for multimodal learning.

### 4. Final model inputs

After preprocessing, the final inputs typically include:

- H&E image patches or visual features
- spatial neighborhood information
- spot-level gene expression targets
- BioMedCLIP text embeddings
- scFoundation transcriptomic embeddings
- cell-type annotations inferred by cell2location

---

## Quick Start

> **Note**  
> Replace the example script names below with the actual scripts used in your repository if they differ.

### 1. Run preprocessing

```bash
python preprocessing/run_cell2location.py
python preprocessing/extract_biomedclip_text_features.py
python preprocessing/extract_scfoundation_features.py
```

### 2. Train ST-FMDC

```bash
python train.py --config configs/st_fmdc.yaml
```

### 3. Evaluate on the test set

```bash
python test.py --config configs/st_fmdc.yaml --ckpt path/to/checkpoint.pth
```

### 4. External validation

```bash
python test_external.py --config configs/st_fmdc.yaml --ckpt path/to/checkpoint.pth
```

### 5. Visualization and downstream analysis

```bash
python evaluate.py
python visualize.py
```

---

## Configuration

Hyperparameters are controlled through configuration files and/or command-line arguments depending on the training script.

Typical settings include:

- dataset name
- data split
- batch size
- learning rate
- number of epochs
- image encoder settings
- DA-CLIP alignment settings
- MNA neighborhood modeling settings
- loss weights for gene expression regression and auxiliary cell-type supervision

Please refer to the configuration files in `configs/` or the argument parser in the training script for details.

---

## Supported Data

This codebase is intended for experiments on spatial transcriptomics datasets containing paired histology and molecular measurements, including but not limited to:

- melanoma
- breast cancer
- cSCC
- external validation cohorts
- Visium / Visium HD style datasets after preprocessing into the expected format

---

## External Resources

The following external tools are used in the preprocessing and representation pipeline:

- [cell2location](https://cell2location.readthedocs.io/en/latest/notebooks/cell2location_tutorial.html)
- [BioMedCLIP](https://huggingface.co/ZiyueWang/biomedclip/tree/main)
- [scFoundation](https://github.com/biomap-research/scFoundation)

---

## Citation

If you find this repository useful in your research, please cite our work:

```bibtex
@article{stfmdc,
  title   = {ST-FMDC: A Foundation-Model-Driven Multi-Modal Dual-Aligned CLIP Framework for Spatial Gene Expression Prediction from H\&E Images},
  author  = {...},
  journal = {IEEE Transactions on Medical Imaging},
  year    = {...}
}
```

---

## Acknowledgements

We thank the authors and maintainers of the following projects for making their resources publicly available:

- cell2location
- BioMedCLIP
- scFoundation

---

## License

Please specify the license for this repository here, for example:

```text
MIT License
```

or

```text
Apache-2.0
```
