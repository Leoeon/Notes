# 卷积网络可视化

> [Zeiler, Matthew D., and Rob Fergus. "Visualizing and understanding convolutional networks." *European conference on computer vision*. Springer, Cham, 2014.](https://link.springer.com/chapter/10.1007/978-3-319-10590-1_53)

从欲可视化的神经元往输入层反向计算：

| 原运算      | 反向运算                                   |
| ----------- | ------------------------------------------ |
| 卷积        | 反卷积（将卷积展开成全连接形式，转置矩阵） |
| max pooling | 记录最大值位置，只激活最大值               |
| relu        | relu                                       |

