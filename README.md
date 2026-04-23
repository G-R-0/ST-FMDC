# ST-FMDC

ST-FMDC: A Dual-Aligned CLIP Framework for Spatial Transcriptomics Prediction with Textual Cell-Type Information

## Overview

This repository provides the implementation of ST-FMDC, a multi-modal framework for predicting spatial gene expression from H&E histology images. The framework is designed to jointly model histological morphology, transcriptomic information, and cell-type semantics for biologically informed spatial transcriptomics analysis.ST-FMDC integrates four main components:

- **MMFE**: a Multi-Modal Foundation Extractor for visual, molecular, and semantic representation learning
- **DA-CLIP**: a Dual-Aligned CLIP module for biologically meaningful cross-modal alignment
- **MNA**: a Mamba-based Neighboring Aggregator for long-range spatial dependency modeling
- **TGE-CS**: a tail-aware gene expression and cell-type supervision strategy for robust optimization under imbalanced data distributions

This codebase supports training, evaluation, and downstream analysis on multiple public and in-house spatial transcriptomics datasets, including melanoma, breast cancer, and cSCC cohorts, with optional external validation on datasets such as 10x Visium HD and independent in-house H&E cohorts.

In the preprocessing pipeline, we additionally incorporate:

- **cell2location** for spatial cell-type annotation
- **BioMedCLIP** for text feature extraction from cell-type descriptions
- **scFoundation** for single-cell transcriptomic feature extraction

# Graphic Abstract

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

- The default environment name is **`ST-FMDC`**.
- The current environment file is configured for a **Linux + CUDA 11.8** setup.
- If your server or workstation uses a different CUDA runtime, compiler toolchain, or driver version, you may need to modify `environment.yml`.
- For CPU-only execution, remove CUDA-specific dependencies and install the corresponding CPU version of PyTorch.

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

## Data Preprocessing

All datasets used in this project are preprocessed in advance and stored in the **`.h5ad`** format. Each dataset is represented as an **AnnData** object and already contains the information required for training, evaluation, and downstream analysis.

In our data organization, the preprocessed `.h5ad` files typically include:

- gene expression matrices stored in `X` and/or `layers`
- gene-level annotations stored in `var`
- spot-level metadata stored in `obs`, such as sample identifiers, patient information, split labels, cell-type-related annotations, text descriptions, and fold assignments
- multimodal representations stored in `obsm`, such as spatial coordinates, H&E patch features, cell abundance estimates, and transcriptomic foundation features
- graph-related information stored in `obsp`, including neighborhood connectivities and distances
- dataset-level metadata stored in `uns`

For example, a preprocessed dataset may contain:

- spatial coordinates in `obsm['spatial']`
- histology patch features in `obsm['patches_scale_1.0']`
- scFoundation features in `obsm['X_scF']`
- cell-type abundance estimates in `obsm['means_cell_abundance_w_sf']`
- text descriptions for semantic encoding in `obs['text_for_clip']`
- train/test split information in `obs['split']`
- cross-validation fold assignments in `obs['fold1']` to `obs['fold5']`

This design allows the datasets to be directly loaded into the ST-FMDC pipeline without additional manual reformatting.

### Preprocessing resources

The preprocessing pipeline additionally incorporates the following external resources:

1. **cell2location** for spatial cell-type annotation  
   Official tutorial:  
   [https://cell2location.readthedocs.io/en/latest/notebooks/cell2location_tutorial.html](https://cell2location.readthedocs.io/en/latest/notebooks/cell2location_tutorial.html)

2. **BioMedCLIP** for text feature extraction from cell-type descriptions  
   Official model page:  
   [https://huggingface.co/ZiyueWang/biomedclip/tree/main](https://huggingface.co/ZiyueWang/biomedclip/tree/main)

3. **scFoundation** for single-cell transcriptomic feature extraction  
   Official repository:  
   [https://github.com/biomap-research/scFoundation](https://github.com/biomap-research/scFoundation)

### Final model inputs

After loading a preprocessed `.h5ad` file, the model inputs typically include:

- H&E image patches or pre-extracted visual patch features
- spatial coordinates and neighborhood relations
- spot-level gene expression targets
- BioMedCLIP-based text embeddings or text descriptions
- scFoundation transcriptomic embeddings
- cell-type abundance or annotation information

## Quick Start

> **Note**  
> The example commands below assume that each dataset has already been preprocessed and saved as an `.h5ad` file. Replace the script names and configuration paths with those used in your repository if they differ.

### 1. Prepare the `.h5ad` input data

Before training ST-FMDC, all datasets are preprocessed in advance and stored in a unified **`.h5ad`** format., for example:

```bash
data/
├── processed/
│   ├── melanoma.h5ad
│   ├── breast.h5ad
│   └── cscc.h5ad
```

Each `.h5ad` file should contain the required AnnData fields used by the pipeline, such as expression matrices, spatial coordinates, patch features, transcriptomic features, text descriptions, and split/fold annotations.

### 2. Train ST-FMDC

```bash
python train.py --config configs/st_fmdc.yaml --data_path data/processed/breast.h5ad
```

### 3. Evaluate on the test set

```bash
python test.py --config configs/st_fmdc.yaml --data_path data/processed/breast.h5ad --ckpt path/to/checkpoint.pth
```

### 4. Run cross-validation

```bash
python train_cv.py --config configs/st_fmdc.yaml --data_path data/processed/breast.h5ad --fold fold1
```

Repeat the command for `fold2` to `fold5` as needed.

### 5. External validation

```bash
python test_external.py --config configs/st_fmdc.yaml --data_path data/external/external_cohort.h5ad --ckpt path/to/checkpoint.pth
```

### 6. Visualization and downstream analysis

```bash
python evaluate.py --data_path data/processed/breast.h5ad --ckpt path/to/checkpoint.pth
python visualize.py --data_path data/processed/breast.h5ad --ckpt path/to/checkpoint.pth
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

This codebase is intended for experiments on spatial transcriptomics datasets stored in **AnnData (`.h5ad`)** format, containing paired histology and molecular measurements. The current pipeline is designed to support datasets such as:

- melanoma
- breast cancer
- cSCC
- external validation cohorts
- Visium / Visium HD style datasets after preprocessing into the expected `.h5ad` format

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
