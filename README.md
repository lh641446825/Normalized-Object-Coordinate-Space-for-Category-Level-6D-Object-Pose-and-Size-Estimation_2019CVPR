# Normalized Object Coordinate Space for Category-Level 6D Object Pose and Size Estimation
> <font
> size=4> 论文地址：[https://arxiv.org/abs/1901.02970](https://arxiv.org/abs/1901.02970)

> github链接：[https://github.com/hughw19/NOCS_CVPR2019](https://github.com/hughw19/NOCS_CVPR2019)

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

![](https://github.com/lh641446825/picture/blob/master/QQ%E6%B5%8F%E8%A7%88%E5%99%A8%E6%88%AA%E5%9B%BE20190902101043.png?raw=true) 
## 核心思想

<font size=4> &#160; &#160; &#160; &#160;作者定义了一个共享的标准坐标空间，可以为从未见过的物体定义位姿和尺寸。标准物体坐标空间是包含在单位立方体中的三维空间。对于给定的物体类别，作者将物体实例放到NOCS中标准化（相当于所有相机都使用同一个模型，将中心和方向对齐后进行缩放来表示）。作者通过NOCS在RGB图像上的投影训练作者的网络。在测试时，网络回归NOCS图，然后将其与深度图一起用于6D位姿和尺寸估计。
 
![](https://github.com/lh641446825/picture/blob/master/QQ%E6%B5%8F%E8%A7%88%E5%99%A8%E6%88%AA%E5%9B%BE20190902165637.png?raw=true) 


## 数据集

<font size=4> &#160; &#160; &#160; &#160;因为现有的三维目标检测数据集NYU v2 和SUN RGB-D只关注三维边界框，无法获得6D位姿和大小的ground truth。作者提出空间上下文感知的混合现实方法来自动生成大量数据用来训练和测试。选取31个真实的室内场景共553张图像，从ShapeNetCore数据集中挑选了6种物体：瓶子、碗、相机、罐子、笔记本电脑和杯子。**作者还创建了一个干扰物类别，由上面没有列出的类别中的物体类别组成，如显视器、电话和吉他。使场景中存在其他物体，这也提高了对主要类别进行预测的鲁棒性。**
 
<font size=4> &#160; &#160; &#160; &#160;**上下文感知合成**:为了提高真实感，作者以上下文感知的方式合成虚拟物体，将物体放在支撑平面上，使用合理的光照。使用平面检测算法对真实图像进行像素级平面分割。随后，在分割平面上随机采样位置和方向。然后放置几个虚拟光源来模拟真实的室内照明条件。最后，将渲染图像和真实图像结合起来，生成一个具有ground truth NOCS图、mask和类别标签的合成图。作者总共渲染了30万张图片。
 
  ![](https://github.com/lh641446825/picture/blob/master/QQ%E6%B5%8F%E8%A7%88%E5%99%A8%E6%88%AA%E5%9B%BE20190902172947.png?raw=true)
  
  <font size=4> &#160; &#160; &#160; &#160;为了评估在真实世界下的性能，作者提供了真实场景下的数据集，并提出一种自动标注ground truth物体位姿和大小的方法。（**但是作者没有说真实场景中的NOCS图的ground truth是怎么得到的**）
 
## 模型

<font size=4> &#160; &#160; &#160; &#160;作者的网络结构是基于Mask R-CNN框架构建的，增加了预测NOCS图的分支。RGB图和深度图作为输入，CNN通过RGB图预测物体的类别标签、mask和NOCS图，之后将NOCS图与深度图进行拟合得到物体的6D位姿和大小（作者在CNN中没有使用深度图，因为作者使用COCO数据集来提高网络的鲁棒性，而COCO不包含深度图）。
 ![](https://github.com/lh641446825/picture/blob/master/QQ%E6%B5%8F%E8%A7%88%E5%99%A8%E6%88%AA%E5%9B%BE20190902170903.png?raw=true)

<font size=4> &#160; &#160; &#160; &#160;作者增加的分支分别预测NOCS图的x、y、z坐标，通过回归每个像素值或者离散化像素值，把它当做一个分类问题。实验表明，图4中B=32的像素分类比直接回归更好.
 ![](https://github.com/lh641446825/picture/blob/master/QQ%E6%B5%8F%E8%A7%88%E5%99%A8%E6%88%AA%E5%9B%BE20190902195624.png?raw=true)

### 损失函数
 
<font size=4> &#160; &#160; &#160; &#160;网络中的类别、边界框和mask使用的损失函数与Mask R-CNN相同，NOCS部分作者使用了两个损失函数：一个标准的softmax用于分类，另一个softL1函数用于回归。
 ![](https://github.com/lh641446825/picture/blob/master/QQ%E6%B5%8F%E8%A7%88%E5%99%A8%E6%88%AA%E5%9B%BE20190902201452.png?raw=true)

其中y是ground truth NOCS图像素值，y*是预测的NOCS图像素值，n为感兴趣区域内的mask像素个数。
 
## 6D位姿和尺寸估计

<font size=4> &#160; &#160; &#160; &#160;作者的目标是通过使用NOCS图和深度图来估计被检测物体的6D位姿和大小。为此，作者使用RGB-D相机内参和外参来将深度图像与彩色图像对齐，使用预测的物体mask来获得物体的3D点云Pm，使用NOCS图来获得预测位姿Pn。然后，估计将Pn转换为Pm的比例、旋转和平移。对于这个7维刚性变换估计问题，作者使用Umeyama算法，而对于离群点去除，作者使用RANSAC。
 
 ## 实验和结果
<font size=4> &#160; &#160; &#160; &#160;作者使用IoU来评估三维目标检测和尺寸的估计，使用平均精度来评估平移误差小于m厘米，旋转误差小于n°的物体位姿估计。将目标检测与位姿估计解耦，将检测阈值设为10%来保证大部分物体都包含在评估中。因为不知道其他类别级6D位姿和大小估计的方法，所以作者使用Mask RCNN+ICP建立baseline来帮助比较性能。
 
<font size=4> &#160; &#160; &#160; &#160;在合成数据集（CAMERA*）上测试:对于50%的3D IoU，mAP为83.9%，位姿使用(5cm，5°)度量，mAP为40.9%。
![](https://github.com/lh641446825/picture/blob/master/QQ%E6%B5%8F%E8%A7%88%E5%99%A8%E6%88%AA%E5%9B%BE20190903214312.png?raw=true)

<font size=4> &#160; &#160; &#160; &#160;在真实数据集（REAL）上测试:在COCO的弱监督下，使用CAMERA* 与REAL* 共同训练网络，并在真实世界的测试集中对其进行评估。由于COCO没有ground truth NOCS图，在训练中不使用NOCS损失。为了平衡这些数据集，作者从三个数据源中为每个小批次选择图像，CAMERA* 的概率为60%，COCO 为20%，REAL*为20%。对于50%的3D IoU， mAP为76.4%，位姿使用(5cm，5°)，mAP为10.2%，使用(5cm，10°) ，mAP为23.1%。相比之下，baseline在50%的3D IoU时，mAP为43.8%，而(5cm，5°)和(5cm，10°)的mAP为0.8%，明显低于本文的性能。
![](https://github.com/lh641446825/picture/blob/master/QQ%E6%B5%8F%E8%A7%88%E5%99%A8%E6%88%AA%E5%9B%BE20190903215657.png?raw=true)

<font size=4  > &#160; &#160; &#160; &#160;作者还创建了一个CAMERA* 的变体，其中图像是以非上下文感知的方式合成的(在表中由B表示)。如表中所示，仅在REAL* 或REAL* 和COCO上进行培训，由于数据集较小，会产生过拟合。CAMERA* 与COCO和REAL* 一起进行训练，可以获得最佳效果。

![](https://github.com/lh641446825/picture/blob/master/QQ%E6%B5%8F%E8%A7%88%E5%99%A8%E6%88%AA%E5%9B%BE20190903215921.png?raw=true)

<font size=4> 作者给出了5个不同指标的AP，其中3D25和3D25分别代表25%和50%的3D IoU。

<font size=4> &#160; &#160; &#160; &#160;作者还与PoseCNN进行了比较。使用2D重投影误差（测量ground truth和估计目标位姿之间的平均像素距离小于5个像素），作者的方法2D重投影误差的mAP为30.2%。PoseCNN 2D重投影误差的mAP为17.2%。
![](https://github.com/lh641446825/picture/blob/master/QQ%E6%B5%8F%E8%A7%88%E5%99%A8%E6%88%AA%E5%9B%BE20190903222420.png?raw=true)
 
## 不足

<font size=4> &#160; &#160; &#160; &#160;存在缺失检测、错误分类和预测坐标图不一致。
![](https://github.com/lh641446825/picture/blob/master/QQ%E6%B5%8F%E8%A7%88%E5%99%A8%E6%88%AA%E5%9B%BE20190903224728.png?raw=true)
![](https://github.com/lh641446825/picture/blob/master/QQ%E6%B5%8F%E8%A7%88%E5%99%A8%E6%88%AA%E5%9B%BE20190903224741.png?raw=true)
 
## 代码

### 环境要求

<font size=4> 
 
 •	cuda80（作者没有提到这个问题，但是我在cuda90上运行会报错）
 
 •	Python 3.5
 
 •	Tensorflow 1.3.0
 
 •	Keras 2.1.5
 
 •	cPickle

### 训练

<font size=4> &#160; &#160; &#160; &#160;训练时会报错：ImportError：No module named ‘visualize’。 
![](https://github.com/lh641446825/picture/blob/master/1524330-20190903165228019-1838692003.jpg?raw=true)
<font size=4> &#160; &#160; &#160; &#160;原因是作者没有将[<font color=red size=4>Mask-RCNN代码](https://codeload.github.com/matterport/Mask_RCNN/zip/master)</font>中的visualize.py文件打包，但是从pycharm上可以看到import visualize是灰色的，作者并没有用到，所以我就先把import visualize注释掉了。
预训练的COCO权重mask_rcnn_coco.h5作者也没有提供，下载地址：[<font color=red size=4>mask_rcnn_coco.h5](https://github.com/matterport/Mask_RCNN/releases/download/v2.0/mask_rcnn_coco.h5)</font>下载之后放在logs文件夹内。

### 测试
<font size=4> &#160; &#160; &#160; &#160;测试结果部分截图：
 
![](https://github.com/lh641446825/picture/blob/master/QQ%E6%B5%8F%E8%A7%88%E5%99%A8%E6%88%AA%E5%9B%BE20190903152915.png?raw=true)
![](https://github.com/lh641446825/picture/blob/master/QQ%E6%B5%8F%E8%A7%88%E5%99%A8%E6%88%AA%E5%9B%BE20190903153113.png?raw=true)
