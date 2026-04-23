# 🐟 Fish Detection System

A desktop fish target detection system developed based on **PyQt5** and **Ultralytics YOLO**. It supports four detection modes: single image, batch folder, video file, and real-time camera. It is suitable for aquaculture, ecological monitoring, and scientific research.

---

## ✨ Features

### Core Functions
- 🎯 **Model Selection**: Supports loading any YOLOv5/v8/v11 series `.pt` weights.
- ⚙️ **GPU Model Initialization**: Warms up the CUDA context and performs initial inference at startup to prevent runtime crashes.
- 📄 **YAML Class Loading**: Supports both `list` and `dict` formats for the `names` field.
- ✓ **Class Filtering**: Select specific fish species to detect via a dialog box (supports Select All/None/Invert).
- 🖼️ **Multi-source Detection**
  - Single Image
  - Image Folder (Auto-scanning)
  - Video File
  - Real-time Camera
- ▶️ **Manual Start**: Inference only executes after clicking "Start Detection."
- ⏸️ **Pause / Resume / Stop**: Full control during execution.
- 💾 **Result Saving**: Saves annotated images/videos + Excel statistics (per-frame statistics by category).
- 📊 **Real-time Statistics**: Sidebar displays cumulative detection counts for each fish category.
- 📝 **Logging**: Operation records + detection logs with timestamps.
- 🀄 **Chinese Support**: Renders Chinese labels via PIL + custom fonts to prevent garbled text.

### Optimizations
- 🎛️ **Adjustable Parameters**: Adjust Confidence, IoU, and Image Size directly on the UI.
- ⚡ **Real-time FPS Display**: Shows inference speed in video and camera modes.
- 📸 **Snapshot Function**: One-click save of the current frame (`Ctrl+S`) during camera/video operation.
- 📂 **Quick Open Save Directory**: Open the results folder with one click (`Ctrl+O`) after detection.
- 📤 **Statistics Export**: Export cumulative statistics to Excel at any time.
- 🖱️ **Drag and Drop**: Drag images, videos, or folders directly into the window.
- ⌨️ **Keyboard Shortcuts**: Space for Pause/Resume, Esc for Stop, F5 for Start.
- 🎨 **White Theme**: A light, professional interface.
- 🚀 **UI Refresh Throttling**: Refreshes at a maximum of 30 FPS to reduce interface lag.
- 🔄 **Default Paths**: Automatically loads preset models and YAML files on startup.

---

## 📦 Requirements

- **Operating System**: Windows 10/11 (Recommended), Linux, macOS
- **Python**: 3.8 ~ 3.11
- **GPU** (Optional): NVIDIA GPU with CUDA support (GTX 1060 and above recommended)
- **RAM**: ≥ 8 GB
- **VRAM**: ≥ 4 GB (when using GPU)

---

## 🔧 Installation

```bash
pip install PyQt5 ultralytics opencv-python pandas pyyaml openpyxl pillow numpy
```

GPU version of PyTorch (Choose based on your CUDA version):

```bash
# CUDA 11.8
pip install torch torchvision --index-url https://download.pytorch.org/whl/cu118

# CUDA 12.1
pip install torch torchvision --index-url https://download.pytorch.org/whl/cu121
```

Verification:
```bash
python -c "import torch; print('CUDA Available:', torch.cuda.is_available())"
```

---

## 🚀 Quick Start

### 1. Prepare Files

Place the following files in the corresponding locations (paths can be modified in lines 36-43 of the source code):

| File | Default Path |
|---|---|
| Chinese Font | `...\Font\DroidSansFallback.ttf` |
| Default Model | `...\weights\best.pt` |
| Default YAML | `...\weights\data.yaml` |

**Example YAML format**:
```yaml
names:
  0: Crucian Carp
  1: Common Carp
  2: Grass Carp
  3: Silver Carp
```

OR:
```yaml
names: [Crucian Carp, Common Carp, Grass Carp, Silver Carp]
```

### 2. Run Program

```bash
python fish_detection_system.py
```

### 3. Operation Workflow

If default paths are configured, the first two steps will complete automatically:

1. **Model → Select Model** — Choose the `.pt` weight file.
2. **Model → Initialize Model (GPU)** — Load model and warm up GPU.
3. **Class → Select Class File (YAML)** — Load class definitions.
4. **Class → Class Filter** (Optional) — Select specific fish species.
5. **Data Source** → Choose input:
   - 🖼 Image
   - 🗂 Image Folder
   - 🎬 Video
   - 📷 Real-time Camera
6. **(Optional)** Adjust parameters: Confidence / IoU / Image Size.
7. **▶ Start Detection** — Run inference.
8. While running:
   - ⏸ Pause / ▶ Resume
   - 📸 Take Snapshot (Camera/Video)
   - ⏹ Stop
9. After completion, click **📂 Open Save Directory** to view results.

---

## ⌨️ Shortcuts

| Shortcut | Function |
|---|---|
| `F5` | Start Detection |
| `Space` | Pause / Resume |
| `Esc` | Stop |
| `Ctrl+S` | Snapshot (Camera/Video) |
| `Ctrl+O` | Open Save Directory |

---

## 📁 Output Structure

Each session generates an independent folder under `runs/detect/expN/`:

```
runs/detect/exp1/
├── det_image1.jpg                              # Single/Batch: Annotated images
├── det_video.mp4                               # Video: Annotated video
├── cam_20260421_153045_30.jpg                  # Camera: Auto keyframes
├── snapshot_20260421_153102.jpg                # Manual snapshot
├── detection_results_20260421_153000.xlsx      # Image/Batch results table
├── video_results_20260421_153000.xlsx          # Video statistics table
└── camera_results_20260421_153000.xlsx         # Camera statistics table
```

---

## 🎛️ Parameter Descriptions

| Parameter | Default | Description |
|---|---|---|
| **Confidence (conf)** | 0.25 | Filters boxes below this value. Recommended: 0.15~0.40 |
| **IoU (iou)** | 0.45 | NMS threshold. Lower is stricter for removing duplicates |
| **Img Size (imgsz)** | 640 | Inference input size. Larger is more accurate but slower |

---

## 🏗️ Code Architecture

```
fish_detection_system.py
├── Global Configuration Constants (Font/Model/YAML paths)
├── Utility Functions
│   ├── cv2_put_chinese_text()     # PIL Chinese rendering
│   └── get_class_color()          # Stable color generation by class ID
├── DetectionThread(QThread)
│   ├── _predict_and_filter()      # Inference + Class filtering
│   ├── _draw_annotations()        # Drawing boxes and Chinese labels
│   ├── _run_images()              # Main loop for Image/Batch
│   ├── _run_video()               # Main loop for Video (with FPS)
│   ├── _run_camera()              # Main loop for Camera (with FPS)
│   └── take_snapshot()            # Snapshot logic
├── ClassFilterDialog(QDialog)     # Class selection dialog
├── FishDetectionSystem(QMainWindow)
│   ├── _build_ui()                # UI Layout
│   ├── _setup_shortcuts()         # Shortcut mapping
│   ├── dragEnterEvent / dropEvent # Drag-and-drop handling
│   └── export_stats()             # Statistics export
├── apply_light_theme()            # White theme stylesheet
└── __main__                       # Entry point
```

---

## 🔒 Notes

1. **Font Path**: If the Chinese font is not found, text will revert to the default font (may cause garbled characters).
2. **First Run**: GPU mode takes time for the first inference due to CUDA kernel compilation.
3. **Camera Permissions**: Windows requires authorization for camera access (Privacy settings).
4. **UI Throttling**: UI refreshes at 30 FPS, but every frame is still processed and saved in the background.

---

## 📜 License

This project is licensed under the MIT License. YOLO models follow their respective licenses (YOLOv8 is AGPL-3.0).

---

## 🙏 Acknowledgements

- [Ultralytics YOLO](https://github.com/ultralytics/ultralytics)
- [PyQt5](https://pypi.org/project/PyQt5/)
- [OpenCV](https://opencv.org/)
- [Pillow](https://python-pillow.org/)