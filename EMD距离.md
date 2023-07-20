# EMD距离

对于离散的概率分布，Wasserstein距离也被描述为推土距离(EMD)。如果我们将分布想象为两个有一定存土量的土堆，那么EMD就是将一个土堆 转换 为另一个土堆所需的最小总工作量。工作量的定义是 单位泥土 的总量乘以它移动的距离。两个离散的土堆分布记作 �� 和 �� ，以以下两个任意的分布为例。

![image-20230710004500157](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20230710004500157.png)

![image-20230710004518997](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20230710004518997.png)

有很多种推土的方式，我们的目标是找到其中工作量最少的那一种，这是个优化问题。

![image-20230710004655043](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20230710004655043.png)

![image-20230710004716135](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20230710004716135.png)