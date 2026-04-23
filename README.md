# ST-FMDC
ST-FMDC: A Dual-Aligned CLIP Framework for Spatial Transcriptomics Prediction with Textual Cell-Type Information

ST-FMDC is a two-stage foundation-model-driven multi-modal framework for spatial gene expression prediction from H&E histology images, comprising a multi-modal foundation extractor (MMFE) to encode histology, transcriptomics, and cell-type text knowledge, a dual-aligned CLIP module (DA-CLIP) to establish biologically meaningful visual--molecular alignment, a Mamba-based neighboring aggregator (MNA) to capture long-range spatial dependencies, and a tail-aware gene expression loss with auxiliary cell-type supervision to improve robustness under severe data imbalance. Compared with existing spatial transcriptomics prediction methods, ST-FMDC leverages cell-type-aware cross-modal alignment and long-range spatial modeling to achieve more accurate and robust prediction performance.

# Graphic Abstract

# Overview
The repository contains the general functionality for training and evaluating the proposed ST-FMDC model on spatial transcriptomics datasets. It implements the key components of ST-FMDC, including the Multi-Modal Foundation Extractor (MMFE), the Dual-Aligned CLIP module (DA-CLIP), the Mamba-based Neighboring Aggregator (MNA), and the tail-aware gene expression and cell-type supervision strategy for robust spatial gene expression prediction from H&E images.

# Installation (Environment)
