# kimi解释3d卷积

先导知识，3d卷积动图：
![alt text](https://i-blog.csdnimg.cn/blog_migrate/c77fa7c32e2c2728fad91dea868398c7.gif)


对于这么一个卷积核对这个论文（PVBLiF）中图片上的输入进行卷积：

    self.h_conv1 = nn.Conv3d(in_channels=1, out_channels=64, kernel_size=(5,1,1), stride=(5,1,1))
![alt text](https://github.com/Matt-Li94/NOTE-study/blob/main/image/conv3d_1.png)
解释：
让我们逐步分解：

时间/深度维度：卷积核的大小是5，步长也是5。这意味着卷积核将覆盖5张连续的图像，然后向下移动5个单位，再次覆盖5张连续的图像。由于总共有25张图像，卷积核将覆盖以下位置的图像：

图像1到图像5
图像6到图像10
图像11到图像15
图像16到图像20
图像21到图像25
这总共是5次卷积操作。

高度和宽度维度：卷积核的大小是1，步长也是1。这意味着卷积核将在高度和宽度维度上覆盖整个图像。因此，在高度和宽度维度上，卷积核将分别进行32次卷积操作（每次移动1个单位）。

对于每次时间/深度维度上的卷积操作，都会在每个高度和宽度位置进行卷积，产生一个 32×32 的特征映射。由于时间/深度维度上有5次卷积操作，最终将产生5个 32×32 的特征映射。

然而，每个特征映射不是单独的一个通道，而是由 out_channels 参数决定的多个通道。在这种情况下，out_channels=64 意味着每个 32×32 的特征映射实际上是由64个通道组成的。因此，每次时间/深度维度上的卷积操作都会产生64个特征映射，而不是一个。

总结来说，使用 kernel_size=(5,1,1) 和 stride=(5,1,1) 的三维卷积层将导致在时间/深度维度上进行5次卷积操作，每次操作覆盖5张连续的图像，并在高度和宽度维度上覆盖整个图像。最终输出将是一个 5×32×32×64 的张量，其中64是输出通道的数量，由 out_channels 参数决定。

**因为3d卷积出来之后结果也是3d的，所以最终输出结果为5×32×32×64。具体的理解是：有64个5×32×32的立方体（5张32×32的图像组成的立方体）**
