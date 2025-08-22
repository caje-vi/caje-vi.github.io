# License Plate Character Recognition using YOLOv9

A robust real-time system for detecting and recognizing individual characters on Brazilian vehicle license plates using YOLOv9 architecture.

## Project Overview

This project implements an automatic license plate recognition (ALPR) system focusing on individual character detection and classification. Unlike traditional approaches that detect the entire plate, our system identifies each alphanumeric character (0-9, A-Z) individually, providing more granular and accurate results.

## Key Results

- **mAP@0.5**: 92.7%
- **mAP@0.5:0.95**: 62.3%
- **Precision**: 87.4%
- **Recall**: 87.8%
- **F1-Score**: 87.6%
- **Real-time performance**: 45 FPS
- **Inference time**: 52ms per image

## Architecture

The system uses **YOLOv9-C** (Compact variant) which provides the optimal balance between accuracy and speed for our use case. Key architectural features:

- **GELAN modules** for improved parameter utilization
- **Programmable Gradient Information (PGI)** to combat information bottleneck
- **Single-shot detection** for real-time performance
- **Multi-scale detection** for handling various plate sizes

## Dataset

- **Total images**: 986
- **Training set**: 788 images
- **Validation set**: 100 images  
- **Test set**: 98 images
- **Classes**: 35 (digits 0-9, letters A-Z)
- **Annotation format**: YOLO bounding box format

## Technical Specifications

### Model Configuration
- **Input size**: 960x960 pixels (optimized for distant plates)
- **Batch size**: 8
- **Training epochs**: 265
- **Optimizer**: SGD with momentum (0.937)
- **Learning rate**: 0.01 (initial) with OneCycle scheduler

### Key Hyperparameters
```yaml
lr0: 0.01                 # Initial learning rate
momentum: 0.937           # SGD momentum
weight_decay: 0.0005      # L2 regularization
iou_t: 0.2               # IoU threshold for NMS
mixup: 0.15              # MixUp augmentation
copy_paste: 0.3          # Copy-paste augmentation
hsv_s: 0.7               # Saturation augmentation
```

## Installation & Setup

### Requirements
```bash
pip install ultralytics
pip install opencv-python
pip install pillow==9.5.0  # Specific version for TensorFlow compatibility
pip install torch torchvision
pip install numpy matplotlib
```

### Quick Start
```python
from ultralytics import YOLO

# Load the trained model
model = YOLO('yolov9c_best.pt')

# Run inference
results = model('path/to/license_plate.jpg')

# Display results
results[0].show()
```
## 🚀 Real-World Applications

- **Toll Systems**: Automatic vehicle identification
- **Parking Management**: Access control and billing
- **Traffic Monitoring**: Speed enforcement and violations
- **Security Systems**: Perimeter access control
- **Fleet Management**: Vehicle tracking and logistics

## Technical Challenges & Solutions

### 1. TensorFlow Compatibility Issue
- **Problem**: Pillow v10 compatibility with visualization utils
- **Solution**: Downgrade to Pillow 9.5.0
- **Code fix**:
```python
# Replace deprecated getsize() method
text_width, text_height = font.getbbox(text)[2:4]  # New approach
# Instead of: text_width, text_height = font.getsize(text)  # Deprecated
```

### 2. Dataset Contamination Prevention
- **Challenge**: Ensuring clean train/val/test splits
- **Solution**: Custom classification tool with automated organization
- **Result**: Prevented overfitting and improved generalization

## 🏅 Training Strategy

### Data Augmentation
- **Geometric**: Rotation (±15°), translation, scaling
- **Photometric**: HSV adjustments, brightness variations
- **Advanced**: MixUp (0.15), Copy-Paste (0.3), Mosaic (1.0)

### Learning Schedule
- **Warmup**: 3 epochs with gradual LR increase
- **Main phase**: OneCycle scheduler with cosine annealing
- **Final LR**: 0.01 (same as initial for fine-tuning)

## Links

- **Dataset**: [Google Drive](https://drive.google.com/drive/folders/1h3lFyCYbawHPLXMN5jg7CWMOBgwX6OP5?usp=sharing)
- **Repository**: [GitHub](https://github.com/caje-vi/PlateDetectionYolo)
- **YOLOv9 Official**: [WongKinYiu/yolov9](https://github.com/WongKinYiu/yolov9)

## Contributors

- **Jean Carlos Vieira Machado**
- **Lucas Leão Oliveira** 
- **João Marcos Maia Zoppi**
- **Tiago Cometti Lombardi**

**Supervisor**: Prof. Jairo Lucas de Moraes
