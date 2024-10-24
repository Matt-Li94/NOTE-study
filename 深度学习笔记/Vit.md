# Vit：Vision Transformer——出圈到cv领域的transformer模型

Vit的大概思想是将cv中的图像直接当作nlp中的的向量输入从而获得一个通用模型。

在此前也有相应的一些思想，比如把图像的每一个像素当成一个向量，但是计算量太大了；还有就是把纵轴和横轴的每一行每一列看成是向量进行输入，这个可行一点。

Vit则是将图像看成是很多个16*16的图像块（patch），从而对图像进行划分，然后将这些小矩阵patch展平成向量，从而就完成把这些patch看成是nlp里面的向量的处理。

以目标检测任务为例，下面具体解释Vit的原理：至于Transformer Encoder具体是什么操作就不管了吧。
![alt text](https://picx.zhimg.com/v2-5afd38bd10b279f3a572b13cda399233_r.jpg)
按照上面的流程图，一个ViT block可以分为以下几个步骤：

(1) patch embedding：例如输入图片大小为224x224，将图片分为固定大小的patch，patch大小为16x16，则每张图像会生成224x224/16x16=196个patch，即输入序列个数为196，每个patch大小维度16x16x3=768（因为RGB有三个通道所以乘3）。线性投射层的维度为768xN (N=768)（线性投射层其实就是全连接层），因此输入通过线性投射层之后的维度依然为196x768，（此处的计算过程可以解释为两个矩阵相乘：[196,768]*[768,N],最终输出的矩阵大小就是[196,N]），即一共有196个token，每个token的维度是768。这里还需要加上一个特殊字符cls（cls的大小维度是1x768），因此最终的维度是197x768。到目前为止，已经通过patch embedding将一个视觉问题转化为了一个seq2seq问题

(2) positional encoding（standard learnable 1D position embeddings）：ViT同样需要加入位置编码，位置编码可以理解为一张表，表一共有N行（就是不能直接简单写为1 2 3之类的数字，而是要对其进行编码，使之每个数字都有对应的向量，注意这个的向量的维度是1x768），N的大小和输入序列长度相同，每一行代表一个向量，向量的维度和输入序列embedding的维度相同（768）。注意位置编码的操作是直接相加sum，而不是拼接concat。加入位置编码信息之后，维度依然是197x768。这样子就可以让图像块不割裂，容易恢复成原来的图像？

(3) LN/multi-head attention/LN：LN输出维度依然是197x768。多头自注意力时，先将输入映射到q，k，v，如果只有一个头，qkv的维度都是197x768，如果有12个头（768/12=64），则qkv的维度是197x64，一共有12组qkv，最后再将12组qkv的输出拼接起来，输出维度是197x768，然后在过一层LN，维度依然是197x768

(4) MLP：将维度放大再缩小回去，197x768放大为197x3072，再缩小变为197x768