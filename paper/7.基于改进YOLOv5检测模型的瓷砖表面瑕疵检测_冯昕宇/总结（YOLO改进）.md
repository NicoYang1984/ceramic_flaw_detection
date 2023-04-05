本总结主要记录对传统YOLO算法的优化改进部分：



# YOLO原本结构：

输入端：预处理数据（进行包括平移颜色等的数据增强），锚框（预先设计的目标检测先验框），对图片进行缩放

Backbone：特征提取：

Neck ：对 Backbone 各层提取的特征信息进行融合

 Prediction ：预测输出层
![image-20230405103952817](https://user-images.githubusercontent.com/103879136/230069413-71067a41-70f1-436e-aad5-97a1a3a563f4.png)





# 优化操作：

## SE注意力模块

在神经网络中添加注意力机制，进而使得网络能够从图像中筛选出重要的信息：

本论文提到了4个轻量级（指对模型速度影响不大）注意力模块，以下是对比分析后的结果：

![image-20230405153109858](https://user-images.githubusercontent.com/103879136/230069708-4a30c2b8-eff7-48f6-9f20-d5b802c2e5c4.png)


通过以上对比分析，添加 SE 注意力模块后，模型在瓷砖瑕疵数据集上的平均检测精度 mAP 提升最大，并且 SE 模块是一个轻量化的模块，加入到 YOLOv5 中几乎 不会影响模型的大小和检测速度，所以将添加 SE 注意力模块的 YOLOv5 作为后续优 化的基础。

以下简要介绍SE注意力模块：

SE （Squeeze-and-Excitation）注意力模块分为两个过程：

Squeeze：将一个通道上的 空间信息压缩为一个全局特征，具体做法是通过一个全局平均池化（global average  pooling）将每个通道上的二维特征编码为一个实数，其表达式为：

![image-20230405153257867](https://user-images.githubusercontent.com/103879136/230069737-0e0e9c30-4e8a-4cd7-a65f-900507328040.png)

Excitation：是为每个压缩后的特征图的每个通道动态生成不同的权重值，具体做法是通过使用两个全连接层（FC）组成一个 Bottleneck 结构去构建通道 间的相关性，输出每个通道的权重值。其表达式为：
![image-20230405153415606](https://user-images.githubusercontent.com/103879136/230069814-d19938ec-89dd-4498-86b3-c4e3b360f778.png)

全过程流程如下图所示：

![image-20230405153415606](https://user-images.githubusercontent.com/103879136/230069814-d19938ec-89dd-4498-86b3-c4e3b360f778.png)

与此同时，SE注意力机制还能嵌入ResNet中提高效率：
![image-20230405154843150](https://user-images.githubusercontent.com/103879136/230069884-96fd36dd-8c1b-4d0c-9998-8be9e2743c0c.png)

## 双向特征融合BiFPN
![image-20230405155941885](https://user-images.githubusercontent.com/103879136/230069908-4a0a6e31-4583-46b0-bb09-b2cc94f99742.png)

通过实验可以发现对 YOLOv5s 的特征融合结构进行改进后，在瓷砖瑕疵检测数 据集上的平均检测精度提升了 1.26 个百分点，并且对小目标的检测精度也有所提升， 再添加 SE 注意力模块后，又提升了 0.9 个百分点，说明改进特征融合结构对提升 YOLOv5s 检测模型再瓷砖瑕疵数据集上的性能是有效的。

以下对BiFPN进行介绍：

在YOLOv5 中的特征融合结构为 PAN 结构，相当于在自顶向下的 FPN 结构中添加了自底向上的结构：

![image-20230405161722738](https://user-images.githubusercontent.com/103879136/230069935-0cff9084-d429-4b11-b537-46df87225724.png)

但在BiFPN中进行特征融合前会对每个输入特征图增加一个额外的权值，让网络学习 不同输入特征图的重要程度，为了尽量减少特征融合的时间成本，采用如下图所示的快速归一化的融合策略：

![image-20230405161937609](https://user-images.githubusercontent.com/103879136/230069965-269bf2ba-2fba-4b64-8820-d8845b237738.png)

在其结构中，BiFPN不仅删除了只有一个输入的节点（如P7和P3），还引入了跨通道的跳跃连接（虚线箭头）其结构如下图所示

![image-20230405162229503](https://user-images.githubusercontent.com/103879136/230069987-e6f5d306-5981-48f7-9d8d-89de6341e92c.png)

## 结构重参化RepVGG：改进卷积块

在引入残差结构的基础上，采用 RepVGG[54]中的思想，对残差结构进行结构重参数化，其结构如图所示：

![image-20230405163612543](https://user-images.githubusercontent.com/103879136/230070005-bdec0803-bea2-454f-acc6-cbb7ca992bea.png)

对比ResNet（每隔两层或三层增加一个分支结构），RepVGG 在每一个 3×3 的卷积层都平行增加一个 1×1 的卷积层分支和一个恒等映射分支，构成一个 RepVGG Block 这样可以提升训练的性能。（下面还介绍了卷积的可加性）

最后实验结果如图所示：
![image-20230405163728652](https://user-images.githubusercontent.com/103879136/230070024-04d265de-dd00-4151-ac36-50c56089b954.png)

将这种策略和前面所述策略结合后，发现准确率能够提高3.47 个百分点

# 基于YOLOv5本身的轻量化改进

由于这些轻量化改进会有牺牲检测精度换取计算量的情况，所以在项目初级阶段先不予考虑

