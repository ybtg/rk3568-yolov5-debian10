# YOLOv5 Deployment on RK3568 (Debian 10)

A **fully verified and reproducible** pipeline for deploying YOLOv5 on  
**RK3568 (Debian 10, 正点原子 BSP)** using **RKNN-Toolkit2**.

This repository focuses on **what actually works**, including exact versions
and real pitfalls encountered during deployment.

---

## 1. Hardware & Software Environment

**Tested and verified only on the following setup:**

- SoC: RK3568
- Board: 正点原子 RK3568
- OS: Debian 10 (official BSP)
- RKNN-Toolkit2: 1.3.0
- YOLOv5-v6.0: https://github.com/ultralytics/yolov5/tree/v6.0

> ⚠️ This repo does NOT aim to support all RK platforms or OS versions.

---

## 2. What This Repo Covers

- [x] YOLOv5 → ONNX export
- [x] ONNX → RKNN conversion
- [x] INT8 quantization
- [x] Deployment on RK3568 (Debian 10)
- [x] Board-side inference verification

---

## 3. What This Repo Does NOT Cover

- Training YOLOv5 from scratch
- Supporting other RK chips (RK3588, RK3399, etc.)
- Non-Debian OS (Ubuntu, Buildroot, Android)
- Performance marketing benchmarks

---

## 4. Repository Structure

rk3568-yolov5-debian10/

├── README.md

├── docs/

│ ├── environment.md

│ ├── conversion(pt2onnx).md

│ ├── quantization(onnx2rknn).md

│ ├── deployment.md

│ └── troubleshooting.md

└── LICENSE

---

## 5. Quick Start (High-Level)

1. Export YOLOv5 model to ONNX
2. Convert ONNX to RKNN using RKNN-Toolkit2-1.3.0
3. Quantize model (INT8)
4. Deploy RKNN model to RK3568 board
5. Run inference demo

Detailed steps are provided in the `docs/` directory.

---

## 6. Key Notes Before You Start

- **Exact versions matter** for RKNN
- Quantization dataset quality directly affects accuracy
- Debian 10 has dependency pitfalls (documented)

See: `docs/troubleshooting.md`

---

## 7. Common Pitfalls (Highlights)

- RKNN-Toolkit2 version mismatch
- RKNPU version mismatch
- Board-side runtime errors

These are documented with real error logs and fixes.

---

## 8. Contribution & Feedback

This repo is primarily for **knowledge sharing and reproducibility**.

- Bug reports with **environment details** are welcome
- PRs should focus on correctness, not feature expansion

---

## 9. License

This project is licensed under the **MIT License**.

---

## 10. Acknowledgements

- Rockchip RKNN Toolkit
- YOLOv5 by Ultralytics
- 正点原子 documentation
- Fruit-Pi rknn-toolkit2 examples  
  https://github.com/Fruit-Pi/rknn-toolkit2
- demianoh RKNN-Toolkit2 Docker image  
  https://hub.docker.com/r/demianoh/rknntoolkit2
