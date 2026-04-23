# ST-FMDC
ST-FMDC: A Dual-Aligned CLIP Framework for Spatial Transcriptomics Prediction with Textual Cell-Type Information

ST-FMDC is a two-stage foundation-model-driven multi-modal framework for spatial gene expression prediction from H&E histology images, comprising a multi-modal foundation extractor (MMFE) to encode histology, transcriptomics, and cell-type text knowledge, a dual-aligned CLIP module (DA-CLIP) to establish biologically meaningful visual--molecular alignment, a Mamba-based neighboring aggregator (MNA) to capture long-range spatial dependencies, and a tail-aware gene expression loss with auxiliary cell-type supervision to improve robustness under severe data imbalance. Compared with existing spatial transcriptomics prediction methods, ST-FMDC leverages cell-type-aware cross-modal alignment and long-range spatial modeling to achieve more accurate and robust prediction performance.

# Graphic Abstract

# Overview
This repository contains the general functionality for training and evaluating the proposed ST-FMDC framework for spatial gene expression prediction from H&E histology images. The framework integrates histology, transcriptomics, and textual cell-type knowledge to achieve biologically meaningful cross-modal alignment, long-range spatial dependency modeling, and robust optimization under imbalanced data distributions.

The repository includes the key implementation details of ST-FMDC, including the Multi-Modal Foundation Extractor (MMFE), the Dual-Aligned CLIP module (DA-CLIP), the Mamba-based Neighboring Aggregator (MNA), and the tail-aware gene expression and cell-type supervision strategy. The model can be benchmarked on multiple public and in-house spatial transcriptomics datasets, including melanoma, breast cancer, and cSCC cohorts, with additional validation on a public 10x Visium HD dataset and an independent in-house H&E image cohort.

For data preprocessing, we additionally use cell2location (https://cell2location.readthedocs.io/en/latest/notebooks/cell2location_tutorial.html) for cell-type annotation in spatial transcriptomics data, BioMedCLIP (https://huggingface.co/ZiyueWang/biomedclip) for extracting text features from cell-type descriptions, and scFoundation (https://github.com/biomap-research/scFoundation) for extracting single-cell transcriptomic features. Cell2location provides a Bayesian framework for mapping reference cell types onto spatial transcriptomics data; BioMedCLIP is a biomedical vision-language foundation model built on PubMedBERT and ViT; and scFoundation is a large-scale foundation model for single-cell transcriptomics with pretrained cell embeddings and downstream utilities.

# Installation (Environment)
We recommend using Anaconda/Miniconda to manage the environment.

Create the environment from the provided environment.yml file:
```bash
conda env create -f environment.yml
conda activate ST-FMDC
