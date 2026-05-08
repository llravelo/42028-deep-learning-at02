# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Context

UTS 42028 Deep Learning and CNN — Assignment 2 (2026), student ID 26045171.
**Due: Monday 11 May 2026, 11:59pm**. Worth 30% of total subject marks.

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
| 1 | Image classification — baseline CNN | `Experiment_1_Classification.ipynb` | Need to verify |
| 2 | Image classification — customized CNN | `Experiment_1_Classification.ipynb` | Need to verify |
| 3 | Object detection — Faster R-CNN | `Part_2_Object_Detection.ipynb` | ✅ Complete (test mAP@50=0.9411) |
| 4 | Object detection — SSD300 | `Part_2_Object_Detection.ipynb` | ✅ Complete (test mAP@50=0.9246) |

`Part_2_Object_Detection_Augment.ipynb` is extra work (not required by spec).
`Assignment-2-Notebook-LeikoRavelo-26045171.ipynb` is currently empty — submission notebook.

## Environment

- Python 3.13, managed with `uv`
- Start JupyterLab: `uv run jupyter lab`
- Run a notebook non-interactively: `uv run jupyter nbconvert --to notebook --execute <notebook.ipynb>`

## Repository structure

```
Assignment-2-Notebook-LeikoRavelo-26045171.ipynb  # Main submission notebook (currently empty)
Experiment_1_Classification.ipynb                 # Part 1: dog breed classification (Exp 1 & 2)
Part_2_Object_Detection.ipynb                     # Part 2: Faster-RCNN (Exp 3) + SSD (Exp 4) — COMPLETE
Part_2_Object_Detection_Augment.ipynb             # Extra: same but with augmentation (not required)
Assignment2_Specification-2026.pdf                # Official spec
lab/                                               # Weekly lab notebooks (Weeks 5–7)
26045171/                                          # Gitignored: datasets
  Image_Classification/dataset/                   # Stanford Dogs (10 breeds)
  Object_Detection/coco/                           # Lettuce Pallets, COCO format (train/valid/test)
  Object_Detection/pascal/                         # Same data in Pascal VOC format
  Object_Detection/yolo/                           # Same data in YOLO format
runs/                                              # Gitignored: saved model checkpoints + plots
  exp3_fasterrcnn_no_augment/best.pth
  exp3_fasterrcnn/best.pth
  exp4_ssd_no_augment/best.pth
  exp4_ssd/best.pth
26045171/metadata.json                             # Student-specific dataset configuration
```

## Two distinct frameworks in use

- **Assignment notebooks** (`Experiment_1_Classification.ipynb`, `Part_2_Object_Detection*.ipynb`): PyTorch + torchvision + torchmetrics
- **Lab notebooks** (`lab/Week*.ipynb`): TensorFlow/Keras (run on AWS SageMaker, not locally)

Do not mix frameworks between these two groups.

## Object detection architecture (Exp 3 & 4)

Both experiments are in `Part_2_Object_Detection.ipynb` (no augmentation):

- **Exp 3**: Faster-RCNN ResNet-50 FPN (pretrained COCO → fine-tuned, batch=2). Head replaced via `FastRCNNPredictor`.
- **Exp 4**: SSD300 VGG16 (pretrained COCO → fine-tuned, batch=16). Head replaced via `SSDClassificationHead`.

Both share the same training loop: SGD (lr=0.005, momentum=0.9), CosineAnnealingLR (T_max=300), early stopping (patience=10–15), gradient clipping (max_norm=5.0). Evaluation uses `torchmetrics.detection.MeanAveragePrecision` (COCO protocol).

`LettuceDataset` reads COCO-format JSON annotations and serves `(FloatTensor[3,H,W], target_dict)` pairs. Images with no annotations are filtered out (Faster-RCNN's loss breaks on empty boxes). The `collate_fn` returns a tuple of individual tensors — required because torchvision detection models do not accept stacked batches.

## Dataset details

- **Image classification**: 10 dog breeds from Stanford Dogs dataset (affenpinscher, briard, Samoyed, Weimaraner, Lakeland terrier, schipperke, Yorkshire terrier, Appenzeller, otterhound, Leonberg). Dataset NOT pre-split — must split using `STUDENT_ID = 26045171` as random seed.
- **Object detection**: Lettuce Pallets (5 classes: Ready, empty_pod, germination, pod, young). Class index 0 is reserved for background; COCO category IDs are 1-indexed and match `CLASS_NAMES[1:]` directly. Pre-split: 70/15/15 → 1050/224/226 images.

## Reproducibility

All notebooks seed with `STUDENT_ID = 26045171`:
```python
torch.manual_seed(STUDENT_ID)
random.seed(STUDENT_ID)
np.random.seed(STUDENT_ID)
```
