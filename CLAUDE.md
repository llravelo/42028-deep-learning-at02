# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Context

UTS 42028 Deep Learning and CNN — Assignment 2 (2026), student ID 26045171.
**Due: Friday 15 May 2026, 11:59pm**. Worth 30% of total subject marks.

## Assignment requirements (from spec)

4 experiments total — no augmentation comparison is required by the spec:

### Part 1 — Image Classification (2 experiments)
- **Exp 1**: Baseline/standard CNN (e.g. AlexNet, GoogleNet, ResNet) trained on the classification dataset
- **Exp 2**: Customized version of the baseline — reduce or increase CONV layers, then train/test

### Part 2 — Object Detection (2 experiments)
- **Exp 3**: Faster R-CNN — train/validate/test on the object detection dataset
- **Exp 4**: SSD **or** YOLO **or** RF-DETR (any one) — train/validate/test on the same dataset

### Submission (both required — incomplete without both)
- **Part A**: iPython/Colab notebooks (zip if multiple) — all cell outputs must be visible
- **Part B**: Report PDF or Word (~10 pages max)
- Submit via Canvas. Final submission is what gets marked.
- Notebook results must match the report exactly — penalties apply for inconsistencies
- Code must work on AWS SageMaker (per spec)

### Report structure
1. Introduction
2. Dataset (sample images per class)
3. CNN architecture for image classification (Exp 1 baseline + Exp 2 customized, with model summaries and diagrams)
4. CNN architecture for object detection (Exp 3 Faster R-CNN + Exp 4 SSD/YOLO, with model summaries)
5. Experimental results and discussion (settings, results, discussion of wrong predictions)
6. Conclusion

## Status of experiments

| Exp | Task | Notebook | Status |
|-----|------|----------|--------|
| 1 | Image classification — baseline CNN | `A2_Classification_P1_Training.ipynb` | ✅ Complete (train=1.0000, val=0.9488, test=0.9525) |
| 2 | Image classification — customized CNN | `A2_Classification_P1_Training.ipynb` | ✅ Complete (train=0.9579, val=0.8737, test=0.8256) |
| 3 | Object detection — Faster R-CNN | `A2_Detection_P1_Training.ipynb` | ✅ Complete (test mAP@50=0.9364, mAP@50-95=0.6561) |
| 4 | Object detection — SSD300 | `A2_Detection_P1_Training.ipynb` | ✅ Complete (test mAP@50=0.9107, mAP@50-95=0.6134) |

## Environment

- Python 3.13, managed with `uv`
- Start JupyterLab: `uv run jupyter lab`
- Run a notebook non-interactively: `uv run jupyter nbconvert --to notebook --execute <notebook.ipynb>`

## Repository structure

```
A2_Classification_P1_Training.ipynb               # Part 1: dog breed classification training (Exp 1 & 2)
A2_Classification_P1_Evaluation.ipynb             # Part 1: evaluation, confusion matrices, Grad-CAM
A2_Detection_P1_Training.ipynb                    # Part 2: Faster-RCNN (Exp 3) + SSD300 (Exp 4)
visualise_unannotated_detection.ipynb             # Utility: inspect unannotated images in dataset
Assignment2_Specification-2026.pdf                # Official spec
lab/                                               # Weekly lab notebooks (Weeks 5–7)
26045171/                                          # Gitignored: datasets
  Image_Classification/dataset/                   # Stanford Dogs (10 breeds)
  Object_Detection/coco/                           # Lettuce Pallets, COCO format (train/valid/test)
  Object_Detection/pascal/                         # Same data in Pascal VOC format
  Object_Detection/yolo/                           # Same data in YOLO format
runs/                                              # Gitignored: saved model checkpoints + plots
  exp1_resnet18/best.pth                          # ← active Exp 1 checkpoint
  exp2_resnet18_shallow/best.pth                  # ← active Exp 2 checkpoint
  exp3_fasterrcnn_v2/best.pth                     # ← active Faster-RCNN checkpoint
  exp4_ssd_v2/best.pth                            # ← active SSD checkpoint
  exp3_fasterrcnn/, exp3_fasterrcnn_no_augment/   # older runs (inactive)
  exp4_ssd/, exp4_ssd_no_augment/                 # older runs (inactive)
26045171/metadata.json                             # Student-specific dataset configuration
```

## Two distinct frameworks in use

- **Assignment notebooks** (`A2_Classification_P1_Training.ipynb`, `A2_Classification_P1_Evaluation.ipynb`, `A2_Detection_P1_Training.ipynb`): PyTorch + torchvision + torchmetrics + pytorch-grad-cam
- **Lab notebooks** (`lab/Week*.ipynb`): TensorFlow/Keras (run on AWS SageMaker, not locally)

Do not mix frameworks between these two groups.

## Image classification architecture (Exp 1 & 2)

Both experiments are in `A2_Classification_P1_Training.ipynb`. Models are custom `nn.Module` classes (`BasicBlock`, `ResNet18`, `ResNet18Shallow`) with pretrained ImageNet weights transferred from torchvision via `state_dict()`.

- **Exp 1**: ResNet-18 (baseline) — 17 conv layers, 512-dim features, 11.18M params. Checkpoint: `runs/exp1_resnet18/best.pth`.
- **Exp 2**: ResNet-18 Shallow (customised) — `layer4` removed, 13 conv layers, 256-dim features, 2.79M params. Checkpoint: `runs/exp2_resnet18_shallow/best.pth`.

Both share: Adam (lr=1e-4, weight_decay=1e-4), no LR scheduler (fixed LR), early stopping (patience=15) on **val_loss** (not val_acc). Train augmentation: `RandomResizedCrop(224, scale=(0.8,1.0))` + `RandomHorizontalFlip`. Val/test: `Resize(256)` + `CenterCrop(224)`. Split: 70/15/15 stratified (seed=STUDENT_ID) → 1194/256/257 images.

Evaluation is in `A2_Classification_P1_Evaluation.ipynb`: per-class accuracy, confusion matrices, misclassification grids, Grad-CAM analysis (pytorch-grad-cam, target layer: `layer3[-1]` for Exp 2, `layer4[-1]` for Exp 1).

## Object detection architecture (Exp 3 & 4)

Both experiments are in `Detection_P1_Training.ipynb`, structured as:
- **Section 1 (Training):** 1.1 Faster R-CNN, 1.2 SSD300 — each with training loop then history plot
- **Section 2 (Evaluation):** 2.1 FRCNN eval, 2.2 SSD eval, 2.3 comparison chart, 2.4 class prediction analysis

Models:
- **Exp 3**: Faster-RCNN ResNet-50 FPN (pretrained COCO → fine-tuned, batch=16). Head replaced via `FastRCNNPredictor`. Checkpoint: `runs/exp3_fasterrcnn_v2/best.pth`.
- **Exp 4**: SSD300 VGG16 (pretrained COCO → fine-tuned, batch=64). Head replaced via `SSDClassificationHead`. Checkpoint: `runs/exp4_ssd_v2/best.pth`.

Both share: SGD (lr=1e-3, momentum=0.9, weight_decay=5e-4), no LR scheduler, early stopping (patience=30) on `val_map` (mAP@50-95). History tracks both `val_map50` (mAP@50) and `val_map` (mAP@50-95). Evaluation uses `torchmetrics.detection.MeanAveragePrecision` (COCO protocol).

`LettuceDataset` reads COCO-format JSON annotations and serves `(FloatTensor[3,H,W], target_dict)` pairs. Nine unannotated images (7 train, 2 val) are excluded — visual inspection confirmed these contain plants but lack bounding box labels (annotation omissions, not empty scenes). Including them with empty targets would provide contradictory supervision. The `collate_fn` returns a tuple of individual tensors — required because torchvision detection models do not accept stacked batches.

## Dataset details

- **Image classification**: 10 dog breeds from Stanford Dogs dataset (affenpinscher, briard, Samoyed, Weimaraner, Lakeland terrier, schipperke, Yorkshire terrier, Appenzeller, otterhound, Leonberg). Dataset NOT pre-split — must split using `STUDENT_ID = 26045171` as random seed.
- **Object detection**: Lettuce Pallets (5 classes: Ready, empty_pod, germination, pod, young). Class index 0 is reserved for background; COCO category IDs are 1-indexed and match `CLASS_NAMES[1:]` directly. Pre-split: 70/15/15 → 1050/224/226 images (after excluding 9 unannotated images: 7 train, 2 val).

## Reproducibility

All notebooks seed with `STUDENT_ID = 26045171`:
```python
torch.manual_seed(STUDENT_ID)
random.seed(STUDENT_ID)
np.random.seed(STUDENT_ID)
```

## Known issues / decisions

- **Evaluation notebook `evaluate` function**: still uses fixed `batch_size` in loss accumulation (minor loss reporting inaccuracy only — accuracy metrics via torchmetrics are correct and unaffected).
- **Early stopping metric**: classification uses `val_loss` (more robust than `val_acc` for this dataset size). Detection uses `val_map` (mAP@50-95). Labs used `val_loss`.
- **Exp 2 weight_decay**: same as Exp 1 (1e-4) in final run — earlier runs used 1e-3, those checkpoints are inactive.
