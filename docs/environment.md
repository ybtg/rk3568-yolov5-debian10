# Environment Setup

This project uses a **multi-stage workflow across different platforms**.
Each stage has been verified and is required for reproducibility.

---

## Stage 1: Model Export (Windows 11)

Used for exporting and simplifying the YOLOv5 model.

- OS: Windows 11
- Framework: PyTorch
- YOLOv5 version: v6.0
- Python: 3.8
- Export steps:
  - `.pt` → `.onnx`
  - ONNX simplification using `onnxsim`

Notes:
- No RKNN tools are required at this stage
- The output ONNX model is transferred to the Ubuntu VM

---

## Stage 2: Model Conversion & Quantization (Ubuntu 20.04 VM)

Used for converting ONNX to RKNN and performing INT8 quantization.

- OS: Ubuntu 20.04 (Virtual Machine)
- Conversion method: Docker container
- RKNN-Toolkit2: 1.3.0
- Python (inside container): 3.8

Docker image used:

```bash
demianoh/rknntoolkit2:1.3.0-py38
```

Notes:
- All RKNN-related operations are performed inside Docker
- RKNN-Toolkit2 is NOT installed directly on the VM host
- Exact toolkit version is critical for successful conversion
- Quantization is performed during this stage

---

## Stage 3: Board-side Inference Verification (RK3568)

This stage verifies inference correctness on the actual target hardware.

- SoC: RK3568
- Board: 正点原子 RK3568
- OS: Debian 10 (official BSP)
- Runtime: rknpu2
- rknpu2 version: 1.3.0

Notes:
- rknpu2 version must match RKNN-Toolkit2 version
- Mismatched runtime versions may cause model loading or inference failures
- Verification is performed using board-side inference demos
