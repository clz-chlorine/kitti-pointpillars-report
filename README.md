# kitti-pointpillars-report
KITTI 3D Object Detection with PointPillars - Reimplementation and Analysis
## 项目简介
本项目基于 [OpenPCDet](https://github.com/open-mmlab/OpenPCDet) 框架，在 KITTI 数据集上复现了 PointPillars 3D目标检测模型，并尝试了数据增强优化（扩大随机旋转和缩放范围）。主要针对 **Car** 类别进行训练和评估。

- 基线模型 Car Moderate 3D AP (R40)：**75.72%**
- 改进模型（数据增强优化） Car Moderate 3D AP (R40)：**74.80%**

详细分析见 `results/` 目录下的技术复盘报告。

---

## 环境配置
- **GPU**：NVIDIA Tesla V100 (32GB)
- **CUDA**：11.3
- **PyTorch**：1.10.0+cu113
- **OpenPCDet**：commit `233f849`（2022年稳定版）
- 其他依赖：`spconv-cu113`、`numpy`、`matplotlib` 等

---

## 数据集准备
1. 从 [KITTI官网](http://www.cvlibs.net/datasets/kitti/eval_object.php?obj_benchmark=3d) 下载 **3D object detection** 数据集（仅需 `training` 部分，7481帧）。
2. 解压后目录结构如下：
```

data/kitti/
├── training/
│   ├── calib/
│   ├── image_2/
│   ├── label_2/
│   └── velodyne/

```
3. 生成数据索引文件：
   ```bash
   cd OpenPCDet
   python -m pcdet.datasets.kitti.kitti_dataset create_kitti_infos tools/cfgs/dataset_configs/kitti_dataset.yaml
   python -m pcdet.datasets.kitti.kitti_dataset create_kitti_dbinfos tools/cfgs/dataset_configs/kitti_dataset.yaml
```

---

训练命令

基线模型（PointPillars）

```bash
cd tools
python train.py --cfg_file cfgs/kitti_models/pointpillar.yaml --batch_size 4 --epochs 80
```

改进模型（数据增强优化）

```bash
python train.py --cfg_file cfgs/kitti_models/pointpillar_aug.yaml --batch_size 4 --epochs 80
```

---

测试命令

训练完成后，使用以下命令在验证集上评估：

```bash
python test.py --cfg_file cfgs/kitti_models/pointpillar.yaml --batch_size 1 --ckpt ../output/kitti_models/pointpillar/default/ckpt/checkpoint_epoch_80.pth
```

（改进模型需替换配置文件路径和 checkpoint 路径）

---

结果

模型 Car Moderate 3D AP (R40)
基线 75.72%
改进后 74.80%

评估结果截图见 results/ 文件夹。

---

失败案例分析

在 results/ 中提供了三个典型失败样本的 BEV 可视化图片

详细分析见技术复盘报告。

---

文件说明

· configs/：训练配置文件（基线和改进版本）
· logs/：训练日志文件
· results/：技术复盘报告（PDF）、评估结果截图、失败案例图片
· predictions/：部分样本的预测结果（.txt 文件）
