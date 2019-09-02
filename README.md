# 2019 CVPR Normalized Object Coordinate Space for Category-Level 6D Object Pose and Size Estimation
## 摘要
本文的目标是估计RGB-D图像中从未见过的物体实例的6D位姿和尺寸。与“实例级”6D位姿估计任务相反，我们的问题假设在训练或测试期间没有精确的对象计算机辅助设计模型可用。为了在给定类别中处理不同的和不可见的对象实例，我们引入了标准化对象坐标空间——一个类别中所有可能对象实例的共享标准表示。然后，我们基于区域的神经网络被训练来直接推断从观察到的像素到这个共享对象表示(NOCS)的对应关系以及其他对象信息，例如类标签和实例掩码。这些预测可以与深度图相结合，共同估计杂乱场景中多个对象的度量6D位姿和维度。为了训练我们的网络，我们提出了一种新的上下文感知技术来生成大量完全公开的混合现实数据。为了进一步改进我们的模型并评估它在真实数据上的性能，我们还提供了一个带有大环境和实例变化的完全注释的真实世界数据集。大量实验表明，该方法能够在真实环境中鲁棒地估计不可见物体实例的位姿和大小，同时在标准6D位姿估计基准上获得最新的性能。
