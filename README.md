# 🐟 鱼类检测系统 (Fish Detection System)

基于 **PyQt5** 和 **Ultralytics YOLO** 开发的桌面端鱼类目标检测系统,支持图片、批量文件夹、视频文件和实时摄像头四种检测模式,适用于水产养殖、生态监测、科研实验等场景。

---

## ✨ 功能特性

### 核心功能
- 🎯 **模型选择**:支持加载任意 YOLOv5/v8/v11 系列 `.pt` 权重
- ⚙️ **GPU 模型初始化**:启动时预热 CUDA 上下文与首次推理,避免运行时崩溃
- 📄 **YAML 类别加载**:支持 `list` 和 `dict` 两种 `names` 字段格式
- ✓ **类别筛选**:对话框勾选需要检测的鱼种,支持全选/全不选/反选
- 🖼️ **多源检测**
  - 单张图片
  - 图片文件夹(自动扫描)
  - 视频文件
  - 实时摄像头
- ▶️ **手动开始**:只有点击"开始检测"才执行推理
- ⏸️ **暂停 / 继续 / 停止**:运行中随时控制
- 💾 **结果保存**:带框图片 + Excel 统计表(按类别统计每帧)
- 📊 **实时统计**:侧边栏累计显示每类鱼检测次数
- 📝 **日志记录**:操作记录 + 检测日志,带时间戳
- 🀄 **中文显示**:通过 PIL + 自定义字体绘制中文标签,无乱码

### 优化增强
- 🎛️ **可调参数面板**:置信度 / IoU / 图像尺寸 直接在 UI 上调整
- ⚡ **实时 FPS 显示**:视频和摄像头模式下显示推理速度
- 📸 **快照功能**:摄像头/视频运行中一键保存当前帧(`Ctrl+S`)
- 📂 **快速打开保存目录**:检测完成后一键打开(`Ctrl+O`)
- 📤 **统计导出**:随时将累计统计导出为 Excel
- 🖱️ **拖拽支持**:直接拖图片/视频/文件夹到窗口
- ⌨️ **键盘快捷键**:空格暂停/继续、Esc 停止、F5 开始
- 🎨 **白色主题**:浅色专业界面
- 🚀 **UI 显示节流**:最高 30 FPS 刷新,减少界面卡顿
- 🔄 **默认路径**:启动自动加载预设的模型和 YAML

---

## 📦 环境要求

- **操作系统**:Windows 10/11(推荐)、Linux、macOS
- **Python**:3.8 ~ 3.11
- **显卡**(可选):支持 CUDA 的 NVIDIA GPU(推荐 GTX 1060 及以上)
- **内存**:≥ 8 GB
- **显存**:≥ 4 GB(使用 GPU 时)

---

## 🔧 依赖安装

```bash
pip install PyQt5 ultralytics opencv-python pandas pyyaml openpyxl pillow numpy
```

GPU 版本 PyTorch(根据 CUDA 版本选择):

```bash
# CUDA 11.8
pip install torch torchvision --index-url https://download.pytorch.org/whl/cu118

# CUDA 12.1
pip install torch torchvision --index-url https://download.pytorch.org/whl/cu121
```

安装验证:
```bash
python -c "import torch; print('CUDA 可用:', torch.cuda.is_available())"
```

---

## 🚀 快速开始

### 1. 准备文件

将以下文件放到对应位置(可在源码第 36-43 行修改路径常量):

| 文件 | 默认路径 |
|---|---|
| 中文字体 | `E:\DeepLearning\YoloCode\ultralytics-main\Font\DroidSansFallback.ttf` |
| 默认模型 | `E:\DeepLearning\YoloCode\ultralytics-main\runs\train\exp\weights\best.pt` |
| 默认 YAML | `E:\DeepLearning\YoloCode\ultralytics-main\runs\train\exp\weights\data.yaml` |

**YAML 格式示例**:
```yaml
names:
  0: 鲫鱼
  1: 鲤鱼
  2: 草鱼
  3: 鲢鱼
```

或:
```yaml
names: [鲫鱼, 鲤鱼, 草鱼, 鲢鱼]
```

### 2. 运行程序

```bash
python fish_detection_system.py
```

### 3. 操作流程

如果已配置默认路径,启动后前两步会自动完成:

1. **模型 → 模型选择** — 选择 `.pt` 权重文件
2. **模型 → 模型初始化 (GPU)** — 加载模型并预热 GPU
3. **类别 → 类别文件选择 (YAML)** — 加载类别定义
4. **类别 → 类别筛选**(可选)— 只检测感兴趣的鱼类
5. **数据源** → 选择输入:
   - 🖼 图片选择
   - 🗂 图片文件夹选择
   - 🎬 视频选择
   - 📷 实时摄像头
6. **(可选)** 调整右上方参数:置信度 / IoU / 图像尺寸
7. **▶ 开始检测** — 运行推理
8. 运行中可以:
   - ⏸ 暂停 / ▶ 继续
   - 📸 拍摄快照(摄像头/视频)
   - ⏹ 停止
9. 检测完成后点击 **📂 打开保存目录** 查看结果

---

## ⌨️ 快捷键

| 快捷键 | 功能 |
|---|---|
| `F5` | 开始检测 |
| `Space` | 暂停 / 继续 |
| `Esc` | 停止 |
| `Ctrl+S` | 快照(摄像头/视频) |
| `Ctrl+O` | 打开保存目录 |

---

## 📁 输出文件结构

每次检测会在 `runs/detect/expN/` 下生成独立文件夹:

```
runs/detect/exp1/
├── det_image1.jpg                              # 单图/批量:每张标注图
├── det_video.mp4                               # 视频:标注视频
├── cam_20260421_153045_30.jpg                  # 摄像头:自动关键帧
├── snapshot_20260421_153102.jpg                # 手动快照
├── detection_results_20260421_153000.xlsx      # 图片/批量结果表
├── video_results_20260421_153000.xlsx          # 视频统计表
└── camera_results_20260421_153000.xlsx         # 摄像头统计表
```

### Excel 表格字段

**图片/批量模式**

| 文件名 | 检测时间 | 检测总数 | 鲫鱼 | 鲤鱼 | 草鱼 | ... |
|---|---|---|---|---|---|---|
| img001.jpg | 2026-04-21 15:30:00 | 5 | 2 | 1 | 2 | ... |

**视频模式**

| 帧序号 | 时间戳(s) | 检测总数 | 鲫鱼 | 鲤鱼 | ... |
|---|---|---|---|---|---|
| 120 | 4.80 | 3 | 1 | 2 | ... |

**摄像头模式**

| 时间 | 帧序号 | 检测总数 | 鲫鱼 | 鲤鱼 | ... |
|---|---|---|---|---|---|
| 2026-04-21 15:30:45 | 30 | 4 | 2 | 2 | ... |

---

## 🎛️ 参数说明

| 参数 | 默认值 | 说明 |
|---|---|---|
| **置信度 (conf)** | 0.25 | 低于此值的预测框会被过滤,建议 0.15~0.40 |
| **IoU (iou)** | 0.45 | NMS 交并比阈值,越低去重越严格 |
| **图像尺寸 (imgsz)** | 640 | 推理输入尺寸,越大精度越高但越慢 |

调参经验:
- 漏检多 → 降低 `conf`(如 0.15)
- 误检多 → 提高 `conf`(如 0.4)
- 重叠框多 → 降低 `iou`(如 0.3)
- 小目标多 → 提高 `imgsz`(如 960 或 1280)

---

## 🏗️ 代码结构

```
fish_detection_system.py
├── 全局配置常量(字体/模型/YAML 路径)
├── 工具函数
│   ├── cv2_put_chinese_text()     # PIL 中文渲染
│   └── get_class_color()          # 按类别 id 生成稳定颜色
├── DetectionThread(QThread)
│   ├── _predict_and_filter()      # 推理 + 按类别筛选
│   ├── _draw_annotations()        # 绘制框和中文标签
│   ├── _run_images()              # 图片/批量主循环
│   ├── _run_video()               # 视频主循环(含 FPS)
│   ├── _run_camera()              # 摄像头主循环(含 FPS)
│   └── take_snapshot()            # 快照
├── ClassFilterDialog(QDialog)     # 类别筛选对话框
├── FishDetectionSystem(QMainWindow)
│   ├── _build_ui()                # UI 布局
│   ├── _build_menu()              # 菜单栏
│   ├── _setup_shortcuts()         # 快捷键
│   ├── dragEnterEvent / dropEvent # 拖拽处理
│   ├── select_*()                 # 各种选择操作
│   ├── start/pause/stop_detection # 检测控制
│   ├── take_snapshot()            # 快照
│   ├── open_save_folder()         # 打开目录
│   └── export_stats()             # 导出统计
├── apply_light_theme()            # 白色主题
└── __main__                       # 入口
```

---

## 🔒 注意事项

1. **字体路径**:若找不到中文字体,中文会用默认字体显示(可能乱码)
2. **首次运行慢**:GPU 模式首次推理需要编译 CUDA kernels,初始化阶段会预热
3. **摄像头权限**:Windows 下首次打开需要授权,系统设置 → 隐私 → 摄像头
4. **大批量处理**:处理超过 1000 张图片时建议先用小批量测试参数
5. **视频输出格式**:默认保存为 `.mp4` (H.264/mp4v codec)
6. **UI 刷新节流**:视频/摄像头模式 UI 最高 30 FPS 刷新,但所有帧仍会被推理和保存

---

## 🐛 常见问题

### ❓ 启动报错 `ModuleNotFoundError: No module named 'PyQt5'`
```bash
pip install PyQt5
```

### ❓ GPU 初始化失败
- 检查 `nvidia-smi` 能否正常输出
- 验证 PyTorch 的 CUDA 版本:`python -c "import torch; print(torch.version.cuda)"`
- 确认 CUDA 驱动和 PyTorch 版本匹配

### ❓ 中文标签乱码
- 确认字体文件 `DroidSansFallback.ttf` 存在于配置路径
- 修改 `CHINESE_FONT_PATH` 为你的实际字体路径
- 可以换成其他中文字体(如 `msyh.ttc`、`simhei.ttf`)

### ❓ 摄像头打不开
- 检查摄像头被其他程序占用
- 尝试修改 `_run_camera()` 中的 `VideoCapture(0)` 为 `VideoCapture(1)`

### ❓ Excel 文件打不开
- 安装 openpyxl:`pip install openpyxl`

### ❓ 视频保存后无法播放
- 换视频播放器(如 VLC/PotPlayer)
- 或修改代码中 `fourcc = cv2.VideoWriter_fourcc(*'mp4v')` 为 `*'XVID'` + `.avi` 后缀

---

## 📋 修改默认路径

编辑 `fish_detection_system.py` 顶部的全局常量:

```python
# 中文字体路径
CHINESE_FONT_PATH = r"你的字体路径.ttf"
# 默认模型路径
DEFAULT_MODEL_PATH = r"你的模型路径.pt"
# 默认 YAML 路径
DEFAULT_YAML_PATH = r"你的 data.yaml 路径"
```

---

## 📜 License

本项目基于 MIT 协议,可自由修改和使用。YOLO 模型遵循其各自 License(YOLOv8 为 AGPL-3.0)。

---

## 🙏 致谢

- [Ultralytics YOLO](https://github.com/ultralytics/ultralytics) — 目标检测框架
- [PyQt5](https://pypi.org/project/PyQt5/) — GUI 框架
- [OpenCV](https://opencv.org/) — 图像处理
- [Pillow](https://python-pillow.org/) — 中文字体渲染

---

## 📮 反馈

如有问题或改进建议,欢迎提交 Issue 或 Pull Request。
