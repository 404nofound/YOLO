# YOLO v3和YOLO v4
转载整理自[深入浅出Yolo系列之Yolov3&Yolov4核心基础知识完整讲解](https://blog.csdn.net/datawhale/article/details/106449828)
## YOLO v3

### 网络结构图
![p1](media/16111324276391/p1.jpeg)

**上图三个蓝色方框内表示Yolov3的三个基本组件：**

**CBL**：Yolov3网络结构中的最小组件，由Conv+Bn+Leaky_relu激活函数三者组成。

**Res unit**：借鉴Resnet网络中的残差结构，让网络可以构建的更深。

**ResX**：由一个CBL和X个残差组件构成，是Yolov3中的大组件。每个Res模块前面的CBL都起到下采样的作用，因此经过5次Res模块后，得到的特征图是608->304->152->76->38->19大小。

**其他基础操作：**

**Concat**：张量拼接，会扩充两个张量的维度，例如26*26*256和26*26*512两个张量拼接，结果是26*26*768。Concat和cfg文件中的route功能一样。

**add**：张量相加，张量直接相加，不会扩充维度，例如104*104*128和104*104*128相加，结果还是104*104*128。add和cfg文件中的shortcut功能一样。

**Backbone中卷积层的数量：**

每个ResX中包含1+2*X个卷积层，因此整个主干网络Backbone中一共包含1+（1+2*1）+（1+2*2）+（1+2*8）+（1+2*8）+（1+2*4）=52，再加上一个FC全连接层，即可以组成一个Darknet53分类网络。不过在目标检测Yolov3中，去掉FC层，不过为了方便称呼，仍然把Yolov3的主干网络叫做Darknet53结构。

## YOLO v4
### 网络结构图
![p2](media/16111324276391/p2.jpeg)

### 相对v3的改进
**输入端：**这里指的创新主要是训练时对输入端的改进，主要包括Mosaic数据增强、cmBN、SAT自对抗训练

**BackBone主干网络：**将各种新的方式结合起来，包括：CSPDarknet53、Mish激活函数、Dropblock

**Neck：**目标检测网络在BackBone和最后的输出层之间往往会插入一些层，比如Yolov4中的SPP模块、FPN+PAN结构

**Prediction：**输出层的锚框机制和Yolov3相同，主要改进的是训练时的损失函数CIOU_Loss，以及预测框筛选的nms变为DIOU_nms