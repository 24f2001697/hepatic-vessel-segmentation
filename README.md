# Hepatic Vessel Segmentation using Deep Learning
## JNU Research Internship | MSD Task08

A comparison of SwinUNETR (Transformer-based) and SegResNet (CNN-based) architectures
for 3D hepatic vessel and tumor segmentation from CT scans.

---

## Results

| Model | Parameters | Mean Dice | Vessel Dice | Tumor Dice | Steps |
|---|---|---|---|---|---|
| SwinUNETR (from scratch) | 62M | 0.6375 | 0.5634 | 0.3491 | 5000 |
| **SegResNet (from scratch)** | **4M** | **0.6906** | **0.5926** | **0.4799** | **10000** |
| nnUNet (published baseline) | — | 0.663 | 0.631 | 0.694 | — |

**Key finding:** SegResNet with 4M parameters outperforms SwinUNETR with 62M parameters
(+0.053 Dice) and exceeds the published nnUNet baseline (+0.028 Dice), without any
pretrained weights.

---

## Sample Predictions

### SegResNet (Best Model — Dice 0.6906)
| CT Image | Ground Truth | Model Prediction |
|---|---|---|
| ![](outputs/predictions/segresnet_case_0.png) | | |

---

## Dataset
- **Name:** MSD Task08 HepaticVessel (Medical Segmentation Decathlon)
- **Volumes:** 303 CT scans (283 training, 20 validation)
- **Classes:** 0=Background, 1=Hepatic Vessel, 2=Tumor
- **Source:** http://medicaldecathlon.com/

---

## Architecture Overview

### SwinUNETR
- Swin Transformer encoder + UNet decoder
- 62M parameters
- Captures long-range spatial relationships
- Best with pretrained backbone weights

### SegResNet
- Pure 3D residual CNN encoder-decoder
- 4M parameters (15× smaller than SwinUNETR)
- Trains 3× faster than SwinUNETR
- Competitive without pretrained weights

---

## Methodology

### Preprocessing
- HU windowing: [-175, 250] → [0, 1]
- Resampling: 1mm × 1mm × 1mm isotropic spacing
- Orientation: RAS standard
- Patch size: 96 × 96 × 96 voxels
- Foreground cropping to remove background

### Augmentation
- Random flips (×3 axes, prob=0.5)
- Random 90° rotation (prob=0.25)
- Random intensity shift (prob=0.5)
- Random intensity scale (prob=0.5)

### Training Setup
- Loss: DiceCE Loss (Dice + CrossEntropy combined)
- Optimizer: AdamW (lr=2e-4, weight_decay=1e-5)
- LR Schedule: Linear warmup (500 steps) + Cosine annealing
- Multi-cycle training: 3 LR restart cycles
- Mixed precision: FP16 (GradScaler)
- Gradient clipping: max_norm=1.0
- Validation: Sliding window inference (overlap=0.5)

---

## Repository Structure

```
hepatic-vessel-segmentation/
├── notebooks/
│   ├── swinunetr_segmentation.ipynb   ← SwinUNETR training notebook
│   └── segresnet_segmentation.ipynb   ← SegResNet training notebook
├── outputs/
│   ├── swinunetr_training_curves.png
│   ├── segresnet_training_curves.png
│   └── predictions/
├── report/
│   └── hepatic_vessel_segmentation_report.pdf
├── requirements.txt
└── README.md
```

---

## Setup

```bash
pip install -r requirements.txt
```

---

## Pretrained Checkpoints (Kaggle)

| Model | Kaggle Dataset | Dice |
|---|---|---|
| SegResNet (best) | kaggle.com/datasets/vivekkumar30112006/segresnet-checkpoint | 0.6906 |
| SwinUNETR | kaggle.com/datasets/vivekkumar30112006/hepatic-checkpoint | 0.6375 |

---

## Environment
- Platform: Kaggle (T4 GPU, 16GB VRAM)
- PyTorch: 2.10.0
- MONAI: 1.5.2
- Python: 3.12

---

## References
1. Tang et al. "Self-Supervised Pre-Training of Swin Transformers for 3D Medical Image Analysis" CVPR 2022
2. Myronenko. "3D MRI Brain Tumor Segmentation Using Autoencoder Regularization" BrainLes 2018
3. Simpson et al. "A Large Annotated Medical Image Dataset for the Development and Evaluation of Segmentation Algorithms" 2019
4. Isensee et al. "nnU-Net: a self-configuring method for deep learning-based biomedical image segmentation" Nature Methods 2021

---

*JNU Research Internship — Medical Image Segmentation*
*Duration: May–July 2026*