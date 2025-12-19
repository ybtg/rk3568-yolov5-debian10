# ONNX Model Export & Simplification

This document describes the verified process for exporting a YOLOv5 v6.0 PyTorch model to ONNX format and simplifying the ONNX graph before RKNN conversion.

---

## Prerequisites

- YOLOv5 version: v6.0
- A trained YOLOv5 model (`best.pt`)
- Conda environment with Python 3.x

If you do not already have a trained YOLOv5 v6.0 model, please train the
model first before proceeding.

---

## Python Dependencies

Install the required ONNX-related packages in your Conda environment:

```bash
pip install onnx==1.9.0 onnx-simplifier==0.3.6
```

These versions have been verified to work correctly with YOLOv5 v6.0.

---

## Modify YOLOv5 Model Forward Function

In the YOLOv5 repository, locate the following file:

```bash
models/yolo.py
```

Modify the forward function as shown below.

### Original Purpose

This modification ensures that the model outputs raw feature maps compatible with ONNX export and subsequent RKNN conversion.

### Modified forward Function

```python
def forward(self, x):
    z = []  # inference output
    for i in range(self.nl):
        x[i] = self.m[i](x[i])  # conv
    return x
```

> ⚠️ This change is required for successful ONNX export in this workflow.

---

## Modify ONNX Opset Version

Open the following file in the YOLOv5 directory:

export.py

Locate the parse_opt function and change the ONNX opset version to:

opset = 12

---

## Export ONNX Model

1. Copy your trained weights file (best.pt) to the same directory as export.py.
2. Run the export script: python export.py

### Common Error: Protobuf Version

If you encounter an error related to protobuf, install the following version in your Conda environment:

```bash
pip install protobuf==3.19.0
```

Then rerun the export command.

---

## ONNX Output

If the export is successful, the following file will be generated:

best.onnx

---

## Simplify ONNX Model

Simplify the exported ONNX model using onnxsim:

```bash
python -m onnxsim best.onnx best-sim.onnx
```

The simplified model (best-sim.onnx) is used in subsequent RKNN conversion steps.