# Deployment on RK3568 (Debian 10)

This section describes **how to deploy and run the RKNN YOLOv5 model on RK3568** using the **official 正点原子 Debian 10 BSP**.

---

## 1. Prerequisites

Before deployment, make sure:

* The system image is **the official 正点原子 Debian 10 image**
* You already have:

  * `best-sim.rknn` (converted RKNN model)
  * `rknpu2-1.3.0.zip`

---

## 2. Serial Connection & Enable Root SSH

### 2.1 Connect via Serial Port

Use tools such as **MobaXterm** to connect to the board’s serial port.

Power on the board and wait until the system boots.

---

### 2.2 Enable Root Login via SSH

Edit the SSH configuration file:

```bash
vim /etc/ssh/sshd_config
```

Find the following line:

```text
#PermitRootLogin prohibit-password
```

Change it to:

```text
PermitRootLogin yes
```

Save and restart SSH service:

```bash
systemctl restart sshd
```

Set a password for the root user:

```bash
passwd root
```

---

## 3. Network Configuration

### 3.1 Connect to a Hotspot

* Enable a hotspot on your **PC or mobile phone**
* Make sure your PC is also connected to the same hotspot

On the serial terminal, run:

```bash
nmtui
```

Use the interactive UI to connect the board to the hotspot.

---

### 3.2 Find Board IP and Connect via SSH

From the hotspot management page, find the **IP address of the RK3568 board**.

Use **FinalShell** to connect SSH

After successful SSH login:

* You may close the serial terminal
* Unplug the USB cable between PC and board

---

## 4. Switch Debian 10 to Archive Source

Since **Debian 10 (buster) is no longer actively maintained**, the APT source must be switched to the Debian archive.

Edit the sources list:

```bash
vim /etc/apt/sources.list
```

Replace **all content** with the following:

```text
deb http://archive.debian.org/debian buster main contrib non-free
deb http://archive.debian.org/debian-security buster/updates main contrib non-free
deb http://archive.debian.org/debian buster-updates main contrib non-free
```

Update the package list (this may be slow, be patient):

```bash
apt update
```

---

## 5. Upload Required Files

Go to the userdata directory:

```bash
cd /userdata
```

Using **FinalShell**, upload:

* `rknpu2-1.3.0.zip`
* `best-sim.rknn`

Unzip RKNPU2:

```bash
unzip rknpu2-1.3.0.zip
```

---

## 6. Replace RKNN Model

Replace the default YOLOv5 RKNN model with your own:

```bash
cp best-sim.rknn userdata/rknpu2-1.3.0/examples/rknn_yolov5_demo/model/RK356X/yolov5s-640-640.rknn
```

---

## 7. Install Build Dependencies

Install required build tools:

```bash
apt-get install gcc g++ cmake -y
apt-get install make -y
```

---

## 8. Fix Script Line Endings (Important)

If you extracted `rknpu2-1.3.0` on **Windows**, shell scripts may have CRLF issues.

Install `dos2unix`:

```bash
apt install dos2unix -y
```

Convert the build script:

```bash
cd /userdata/rknpu2-1.3.0/examples/rknn_yolov5_demo
dos2unix build-linux_RK356X.sh
```

---

## 9. Modify Post-Processing Parameters

Edit `postprocess.h`:

```bash
cd include
vim postprocess.h
```

Modify the following macro to match **your model’s number of classes**:

```c
#define OBJ_CLASS_NUM <your_class_count>
```

Save and exit.

---

## 10. Prepare Test Image and Labels

### 10.1 Upload Test Image

Go to the model directory:

```bash
cd /userdata/rknpu2-1.3.0/examples/rknn_yolov5_demo/model
```

Upload your test image (e.g. `123.jpg`) using FinalShell.

---

### 10.2 Modify Class Labels

Backup the original label file:

```bash
cp coco_80_labels_list.txt coco_80_labels_list_bak.txt
```

Edit labels:

```bash
vim coco_80_labels_list.txt
```

Replace all labels with **your own class names**, one per line.

Save and exit.

---

## 11. Build the Demo

Return to the demo directory and build:

```bash
cd ..
./build-linux_RK356X.sh
```

If successful, an `install` directory will be generated.

Copy it to `/userdata`:

```bash
cp -a install /userdata/
```

Enter the runtime directory:

```bash
cd /userdata/rknn_yolov5_demo_linux
```

---

## 12. Run Inference on Board

Assuming your test image is `123.jpg`, run:

```bash
./rknn_yolov5_demo model/RK356X/yolov5s-640-640.rknn model/123.jpg
```

If no errors occur, an output image `out.jpg` will be generated in the same directory.

---

## 13. Download Result Image

Install `lrzsz`:

```bash
apt install lrzsz -y
```

Download the output image:

```bash
sz out.jpg
```

The file will be saved to your PC’s default download directory.

---

## 14. Notes

* Exact **RKNPU2 and RKNN model versions must match**
* Debian 10 archive source is mandatory
* Class count and label order **must match training**