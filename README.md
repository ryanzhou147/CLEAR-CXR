# SCARCE-CXR

[![Blog](https://img.shields.io/badge/blog-rzhou.me%2Fthoughts%2Fscarce--cxr-111827?style=flat-square&logo=ghostery&logoColor=white)](https://rzhou.me/thoughts/scarce-cxr)
[![License: MIT](https://img.shields.io/badge/license-MIT-6366f1?style=flat-square)](LICENSE)

SSL pretraining on 112k unlabeled NIH chest X-rays, evaluated on 10 rare PadChest diseases at 1 to 50 shots. Four methods: MoCo v2, BarlowTwins, SparK, DINO.

---

## Setup

Requires Python 3.14 and [uv](https://github.com/astral-sh/uv).

```bash
git clone https://github.com/ryanzhou147/SCARCE-CXR && cd SCARCE-CXR
uv sync
uv run python -m data.download --dataset nih
uv run python -m data.download --dataset padchest
```

---

## Pretraining

```bash
uv run python main.py pretrain-moco   --config configs/moco.yaml    # ResNet50, ~63h
uv run python main.py pretrain-barlow --config configs/barlow.yaml  # ResNet18, ~15h
uv run python main.py pretrain-spark  --config configs/spark.yaml   # ResNet50, ~20h
```

---

## Evaluation

```bash
uv run python -m finetune.probe    --checkpoint outputs_cloud/moco-v2/best.pt
uv run python -m finetune.finetune --checkpoint outputs_cloud/moco-v2/best.pt
uv run python -m finetune.count_labels
uv run python -m data.eval.collapse_monitor --outputs-dir outputs/moco-v2
bash run_eval.sh
```

---

## Structure

```
ssl_methods/    MoCo, BarlowTwins, SparK, DINO training loops
finetune/       probe.py, finetune.py, _data.py, _plots.py
data/           RAM-cached loader, backbone loader, download, collapse monitor
configs/        per-method YAML hyperparameters
results/        eval summaries
```
