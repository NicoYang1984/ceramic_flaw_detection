### 算法选择
在瓷砖缺陷检测中，基于目标检测的方法是一种常用的算法，可以自动地检测出瓷砖表面的缺陷。常见的基于目标检测的算法包括 Faster R-CNN、YOLO、SSD 等。

## Faster R-CNN
**Faster R-CNN** 是目标检测领域的经典算法之一，它通过引入区域提议网络（Region Proposal Network, RPN）来实现端到端的目标检测。Faster R-CNN 基于深度卷积神经网络，将输入图像经过卷积和池化等处理得到一系列的特征图，然后在特征图上利用 RPN 生成候选目标区域，再对这些候选目标区域进行分类和位置回归，最终输出检测结果。Faster R-CNN 在检测精度和速度上都有不错的表现。
  
## YOLO
**YOLO**（You Only Look Once）是一种实时目标检测算法，能够快速地检测出图像中的目标，并输出它们的类别和位置信息。YOLO 将输入图像分成 S x S 个网格，每个网格预测 B 个边界框和对应的置信度和类别概率，然后根据阈值筛选出最终的检测结果。相比 Faster R-CNN，YOLO 具有更快的检测速度，但在小目标检测上表现相对较差。
  
## SSD
**SSD**（Single Shot MultiBox Detector）是一种单阶段目标检测算法，与 Faster R-CNN 和 YOLO 不同的是，SSD 直接在特征图上进行目标检测，省去了复杂的区域提议过程。SSD 将不同尺度的特征图融合起来，用不同大小和宽高比的锚框进行目标检测，最终输出检测结果。SSD 在速度和准确率上都有不错的表现.
   
如果需要**快速实现实时检测**，可以选择 **YOLO 或 SSD**等单阶段算法；   
如果需要**更高的检测精度**，可以选择 **Faster R-CNN** 等两阶段算法。
***
### 几个比较常见的术语：

卷积层：<img width="733" alt="1680604187663" src="https://user-images.githubusercontent.com/105412420/229765143-5cacc29b-dcd4-4536-ae51-2b2b04c57368.png">

激活层：<img width="749" alt="1680604275207" src="https://user-images.githubusercontent.com/105412420/229765279-99db2bb7-2b0f-4af1-8119-4437ef8bf1d6.png">

池化层：<img width="761" alt="1680604325916" src="https://user-images.githubusercontent.com/105412420/229765444-16f777fa-c56f-46ec-8f7f-bd663cfc50e5.png">

# 全局平均池化：
原帖链接：https://zhuanlan.zhihu.com/p/377572391
论文地址: https://arxiv.org/pdf/1312.4400.pdf%20http://arxiv.org/abs/1312.4400
代码链接: https://worksheets.codalab.org/worksheets/0x7b8f6fbc6b5c49c18ac7ca94aafaa1a7
背景
在卷积神经网络训练初期，卷积层通过池化层后一般要接多个全连接层进行降维，最后再Softmax分类，这种做法使得全连接层参数很多，降低了网络训练速度，且容易出现过拟合的情况。在这种背景下，M Lin等人提出使用全局平均池化Global Average Pooling[1]来取代最后的全连接层。用很小的计算代价实现了降维，更重要的是GAP极大减少了网络参数(CNN网络中全连接层占据了很大的参数)。
定义
全局平均池化是一种特殊的平均池化，只不过它不划分若干矩形区域，而是将整个特征图中所有的元素取平均输出到下一层。其定义如下：
![image](https://user-images.githubusercontent.com/103879136/229973727-a29db19a-3d83-4f9b-af16-087e948feed1.png)
![image](https://user-images.githubusercontent.com/103879136/229973771-e4c8a0de-d1ca-4876-a6ac-315530d3c43a.png)
解说
作为全连接层的替代操作，GAP对整个网络在结构上做正则化防止过拟合，直接剔除了全连接层中黑箱的特征，直接赋予了每个channel实际的类别意义。除此之外，使用GAP代替全连接层，可以实现任意图像大小的输入，而GAP对整个特征图求平均值，也可以用来提取全局上下文信息，全局信息作为指导进一步增强网络性能。

几个深度学习框架的简介和选择：<img width="763" alt="1680611022458" src="https://user-images.githubusercontent.com/105412420/229790639-3cca55b7-7b3f-45ef-bfc3-bd338591b70e.png">

交叉熵代价函数（Cross-entropy cost function）优点及其原理：<img width="517" alt="1680611737323" src="https://user-images.githubusercontent.com/105412420/229793496-8dcaf030-6609-49ae-a031-79fdd7e2ee3a.png">







