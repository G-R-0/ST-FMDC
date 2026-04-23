# ST-FMDC

ST-FMDC: A Dual-Aligned CLIP Framework for Spatial Transcriptomics Prediction with Textual Cell-Type Information

ST-FMDC is a multimodal learning framework for predicting spatial gene expression from H&E whole-slide images.  
It jointly models histological morphology, transcriptomic foundation features, and cell-type semantic text information to improve biological interpretability and robustness.

This repository contains the main training/evaluation pipeline and core modules used in our experiments on melanoma, breast cancer, and cSCC cohorts.

# Graphic Abstract

## Overview

ST-FMDC integrates four main components:

- **MMFE**: Multi-Modal Foundation Extractor for visual, molecular, and semantic feature representation.
- **DA-CLIP**: Dual-Aligned CLIP module for multimodal contrastive alignment.
- **MNA**: Mamba-based Neighboring Aggregator for spatial context modeling.
- **TGE-CS**: Tail-aware optimization strategy for gene expression and cell-type supervision.

Preprocessing typically includes:

- **cell2location** for cell-type abundance estimation
- **BioMedCLIP** for text semantic encoding
- **scFoundation** for transcriptomic embeddings

## Repository Structure

- `main.py`: main entrypoint (pretrain + finetune + test)
- `data.py`: AnnData dataset loader and data preparation
- `models.py`: ST-FMDC model components
- `train.py`: training/evaluation/checkpoint logic
- `pathology_models.py`: pathology image encoder wrappers
- `dataset_configs.py`: built-in dataset presets
- `utils.py`: utilities

## Installation (Environment)

We recommend Conda.

### Create the environment

```bash
conda env create -f environment.yml
conda activate ST-FMDC
```

## Requirements

Main packages:

- `pytorch`
- `torchvision`
- `anndata`
- `scanpy`
- `open-clip-torch`
- `timm`
- `scikit-learn`
- `matplotlib`

See `environment.yml` for details.

## Dataset Preparation

Input data should be preprocessed into `.h5ad` (AnnData) format.  
Typical fields used by this code:

- `obsm['spatial']`
- `obsm['patches_scale_1.0']`
- `obsm['X_scF']`
- `obs['text_for_clip']`
- `obs['split']`

Built-in dataset aliases (`dataset_configs.py`):

- `melanoma`
- `breast`
- `cscc`

## Configuration

Hyperparameters are configured via `main.py` arguments:

```bash
python main.py --help
```

## How to Run ST-FMDC

```bash
python main.py \
  --dataset breast \
  --uni_pretrained_path /path/to/phikon-v2 \
  --biomedclip_weight_path /path/to/open_clip_pytorch_model.bin \
  --mamba_model_path /path/to/mamba-790m-hf \
  --output_dir /path/to/output \
  --pretrain_mode full \
  --spatial_aggregator mamba \
  --image_encoder phikon_v2 \
  --loss_mode combined \
  --predict_celltype
```

Outputs are saved under `--output_dir`, including metrics JSON files and checkpoints.

## Optional: Upload Code to GitHub

```bash
python github_upload.py \
  --repo-path . \
  --remote-url git@github.com:your-name/your-repo.git \
  --branch main \
  --init-if-needed \
  --commit-message "chore: update project files"
```

## Citation

If you find this repository useful, please cite:

```bibtex
@article{stfmdc,
  title   = {ST-FMDC: A Foundation-Model-Driven Multi-Modal Dual-Aligned CLIP Framework for Spatial Gene Expression Prediction from H\&E Images},
  author  = {...},
  journal = {...},
  year    = {...}
}
```

## References

- [1] **cell2location maps fine-grained cell types in spatial transcriptomics**  
  https://www.nature.com/articles/s41587-021-01139-4

- [2] **BioMedCLIP: a multimodal biomedical foundation model pretrained from fifteen million scientific image-text pairs**  
  https://arxiv.org/abs/2303.00915

- [3] **scFoundation: a foundation model for cell biology discovery**  
  https://www.nature.com/articles/s41592-024-02305-7

- [4] **Phikon-v2 (official model/code page)**  
  https://huggingface.co/owkin/phikon-v2
  

