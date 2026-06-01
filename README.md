# Train Colab Classification

Google Colab notebook for training image classification models from a public dataset URL and sending the result back to a Label Forge backend.

## Overview

This repository contains a Colab-based training workflow in `train_classification_notebook.ipynb`.

The notebook is designed to:

- Receive training parameters from Label Forge.
- Validate that dataset and callback URLs are public.
- Install training dependencies.
- Download and extract the dataset.
- Train a YOLO model with Ultralytics.
- Evaluate the trained model.
- Generate sample predictions.
- Upload `best.pt` back through the backend.
- Send final training metrics to the callback URL.

## Main File

| File | Purpose |
| --- | --- |
| `train_classification_notebook.ipynb` | Main Google Colab notebook for dataset download, training, evaluation, upload, and callback. |

## Requirements

The notebook installs its Python dependencies automatically in Colab:

```bash
ultralytics torch torchvision requests gdown pyyaml
```

Recommended runtime:

- Google Colab
- GPU runtime enabled
- Public dataset download URL
- Public backend callback URL

## Training Parameters

The first notebook cell exposes the main parameters:

| Parameter | Description |
| --- | --- |
| `JOB_ID` | Training job identifier from the backend. |
| `DATASET_URL` | Public URL to the dataset zip file. |
| `CALLBACK_URL` | Public callback endpoint used to send training results. |
| `ARCHITECTURE` | YOLO model size, for example `yolov8n`, `yolov8s`, `yolov8m`, `yolov8l`, or `yolov8x`. |
| `EPOCHS` | Number of training epochs. |
| `IMAGE_SIZE` | Input image size. |
| `BATCH_SIZE` | Training batch size. |
| `LEARNING_RATE` | Initial learning rate. |
| `PATIENCE` | Early stopping patience. |
| `DEVICE` | Colab device, usually `0` for GPU. |

The notebook also includes augmentation and optimizer settings such as `OPTIMIZER`, `MOSAIC`, `MIXUP`, `FLIPLR`, `HSV_H`, `HSV_S`, and others.

## Dataset Format

For classification training, the extracted dataset should normally look like this:

```text
dataset/
  train/
    class_a/
      image1.jpg
    class_b/
      image2.jpg
  val/
    class_a/
    class_b/
  test/
    class_a/
    class_b/
```

If the dataset uses `valid/`, the notebook renames it to `val/` because Ultralytics classification training expects the validation folder to be named `val`.

For detection datasets, the notebook checks for `data.yaml`.

## How To Run

1. Open `train_classification_notebook.ipynb` in Google Colab.
2. Enable GPU runtime.
3. Fill in `JOB_ID`, `DATASET_URL`, and `CALLBACK_URL`.
4. Adjust model and training parameters if needed.
5. Run all cells from top to bottom.
6. Wait for training to complete and for the callback request to be sent.

## Public URL Requirement

`DATASET_URL` and `CALLBACK_URL` must not point to localhost addresses such as:

```text
localhost
127.0.0.1
0.0.0.0
```

If running the backend locally, expose it with a public tunnel such as Cloudflare Tunnel, then use the generated HTTPS URL.

## Git Push Note

If a commit was removed locally with `git reset --hard` and GitHub still has that commit, a normal push can fail with:

```text
rejected non-fast-forward
```

To keep the GitHub history and integrate remote changes:

```bash
git pull --rebase origin main
git push origin main
```

To intentionally make GitHub match the local branch after a reset:

```bash
git push --force-with-lease origin main
```

Use `--force-with-lease` only when you are sure the remote commit should be removed.
