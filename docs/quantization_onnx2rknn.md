# ONNX to RKNN Conversion & INT8 Quantization

This document describes the verified process of converting a simplified ONNX model (`best-sim.onnx`) into an RKNN model using **RKNN-Toolkit2 v1.3.0** on an **Ubuntu 20.04 virtual machine**.

INT8 quantization is performed during the RKNN build process.

---

## Overview

- Input model: `best-sim.onnx`
- Output model: `best-sim.rknn`
- Toolkit: RKNN-Toolkit2 v1.3.0
- Environment: Ubuntu 20.04 (Virtual Machine)
- Method: Docker-based RKNN conversion

---

## Required Files

Download the required files from the **GitHub Release**, or from the **original authors / mirrors** listed below.

### Files Used in This Project

* `rknn-toolkit2-1.3.0.zip`

* `demianoh-rknntoolkit2-1.3.0-py38-docker.tar.gz`
  (Py38 version is used and verified in this project)

* *(Optional)* `demianoh-rknntoolkit2-1.3.0-py36-docker.tar`

### Download Sources

* **GitHub Release (recommended for toolchain files)**
  See the *Releases* page of this repository.

* **Docker Hub (official source)**
  [https://hub.docker.com/r/demianoh/rknntoolkit2](https://hub.docker.com/r/demianoh/rknntoolkit2)

* **Baidu Netdisk mirror (optional, for large Docker files)**
  [https://pan.baidu.com/s/166iWh073yyXtsfJGddRpug?pwd=rknn](https://pan.baidu.com/s/166iWh073yyXtsfJGddRpug?pwd=rknn)

> ⚠️ Docker images are **not built or maintained by this repository**.
> They are provided **only to improve download accessibility and reproducibility**.

---

## Ubuntu VM Preparation

### Disk Space Recommendation

When installing Ubuntu 20.04 in VMware, it is recommended to allocate **at least 64 GB of disk space**.

If you are not familiar with installing Ubuntu in VMware, please search for installation guides using Google.

---

### Initial System Setup

Set the root password:

```bash
sudo passwd root
```

Update system packages:

```bash
sudo apt-get update -y
sudo apt-get upgrade -y
```

Enable SSH access if it is not already enabled.
(Details are omitted here; please refer to standard Ubuntu SSH guides.)

---

## File Transfer to VM

1. Run the following command to obtain the VM IP address:

```bash
ifconfig
```

2. Install **FinalShell** on your host machine:
   [https://www.hostbuf.com/](https://www.hostbuf.com/)

3. Use FinalShell to connect to the Ubuntu VM.

4. Transfer the following files to the VM using FinalShell:

* `rknn-toolkit2-1.3.0.zip`
* `demianoh-rknntoolkit2-1.3.0-py38-docker.tar.gz`

Wait until file transfer is complete.

---

## Prepare RKNN-Toolkit2 Examples

Unzip RKNN-Toolkit2:

```bash
unzip rknn-toolkit2-1.3.0.zip
```

Enter the examples directory:

```bash
cd rknn-toolkit2-1.3.0/examples
```

Transfer `best-sim.onnx` into this directory using FinalShell.

---

## Install Docker

Install Docker on Ubuntu 20.04 following official instructions or standard installation guides.

---

## Load and Run Docker Container

Load the Docker image:

```bash
docker load --input demianoh-rknntoolkit2-1.3.0-py38-docker.tar.gz
```

Run the container:

```bash
docker run -ti --privileged -v /dev/bus/usb:/dev/bus/usb -v ~/rknn-toolkit2-1.3.0/examples:/examples demianoh/rknntoolkit2:1.3.0-py38 /bin/bash
```

---

## Prepare Quantization Dataset

Install the `lrzsz` tool inside the container:

```bash
apt-get install lrzsz
```

Enter example directory:

```bash
cd /examples/onnx/yolov5
```

Copy the ONNX model:

```bash
cp /examples/best-sim.onnx best-sim.onnx
```

Upload a test image:

```bash
rz
```

Select a test image, for example: `123.jpg`.

Create a dataset file:

```bash
vim mydataset.txt
```

Add the image filename:

```
123.jpg
```

Save and exit.

---

## Modify test.py for RKNN Conversion

Edit `test.py`:

```bash
vim test.py
```

Modify the following fields:

* `ONNX_MODEL` → `best-sim.onnx`
* `RKNN_MODEL` → `best-sim.rknn`
* `IMG_PATH` → `./123.jpg`
* `DATASET` → `./mydataset.txt`
* `CLASSES` → your custom YOLO class list

Locate the following line:

```python
ret = rknn.load_onnx(model=ONNX_MODEL, outputs=['378', '439', '500'])
```

Modify it to:

```python
ret = rknn.load_onnx(model=ONNX_MODEL)
```

Save and exit.

---

## Build RKNN Model

Run the script:

```bash
python3 test.py
```

If no error occurs, the file `best-sim.rknn` will be generated in the
current directory.

---

## (Optional) Save Inference Result Image

To save the inference output image, add the following line **before**
`rknn.release()` in `test.py`:

```python
cv2.imwrite("out.jpg", img_1)
```

Run the script again.

Download the output image:

```bash
sz out.jpg
```

The downloaded file will be saved to the default “Downloads” directory.

---

## Export RKNN Model

Once verified, export the RKNN model:

```bash
sz best-sim.rknn
```

The generated RKNN model can now be deployed to the RK3568 board.