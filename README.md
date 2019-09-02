> <font
> size=4> 论文地址：[https://arxiv.org/abs/1901.02970](https://arxiv.org/abs/1901.02970)

> github链接：[https://github.com/hughw19/NOCS_CVPR2019](https://github.com/hughw19/NOCS_CVPR2019)
# Normalized Object Coordinate Space for Category-Level 6D Object Pose and Size Estimation
# 类别级6D物体位姿和尺寸估计的标准化物体坐标空间
## 简介
<font size=4> &#160; &#160; &#160; &#160;本文的目标是估计RGB-D图像中从未见过的物体实例的6D位姿和尺寸。与“实例级”6D位姿估计任务相反，作者假设在训练或测试期间没有精确的CAD模型可用。为了处理给定类别中不同的和从未见过的物体实例，作者引入了标准化物体坐标空间（简称NOCS），即同一个类别中的所有物体实例使用一个共享的标准模型来表示。然后，通过训练神经网络来推断观察到的像素与共享标准模型的对应关系以及其他信息，例如类别标签和mask。通过将预测图像与深度图相结合，共同估计杂乱场景中多个物体的6D位姿和尺寸。为了训练网络，作者提出了一种新的上下文感知技术来生成大量带注释的混合现实数据。为了进一步改进模型并评估它在真实数据上的性能，作者还提供了一个完全注释的真实场景下的数据集。大量实验表明，该方法能够鲁棒地估计真实场景中从未见过物体的位姿和大小。
## 问题的提出
<font size=4>&#160; &#160; &#160; &#160; 位姿估计的现有方法中SSD-6D、latent霍夫投票、BB8、YOLO-6D和poseCNN都预先提供了物体精确的CAD模型及大小，然而从未见过的物体是没有CAD模型的。在三维目标检测的论文中不需要物体的CAD模型就可以估计类别标签和边界框。但是三维目标检测是根据视点估计的，没有编码物体的精确方向。这两种方法都不能对从未见过的物体进行位姿估计。

 1. 第一个挑战：找到特定类别物体位姿和尺寸的表示方法。
 2. 第二个挑战：缺少训练和测试数据集。（现有三维目标检测数据集缺注释，或者不包含桌面级物体类别）

## 创新点

 1. 作者使用一个共享的标准坐标空间（NOCS）作为参考系来表示同一类别中的所有物体实例。
 2. 提出一个可以同时预测物体类别标签、mask和NOCS图的CNN，将NOCS图与深度图进行对应来估计从未见过物体的位姿和大小。
 3. 使用空间上下文感知的混合现实方法来自动生成大量数据用来训练和测试。
