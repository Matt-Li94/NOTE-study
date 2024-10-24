# pytorch学习 b站小土堆
## 1.dataset和dataloader的联系

dataset是告诉程序数据集的一些具体参数，其实可以直接理解成数据集吧

![alt text](https://production-media.paperswithcode.com/datasets/Screen_Shot_2021-01-29_at_2.16.15_PM.png)



dataloader是从dataset中读取设置，比如一次读取四张

dataloader的具体工作形式如下图

![alt text](https://i-blog.csdnimg.cn/blog_migrate/0484efcfdcaf6a58211e3b671cae24b9.png)

batch_size设置为4就每次从dataset里面读取四张并打包

dataloader返回的数据里面包含有图像以及对应的标签序号

#### 两者的区别总结如下：
Dataset是代表这一数据的抽象类（也就是基类）。我们可以通过继承和重写这个抽象类实现自己的数据类，只需要定义__len__和__getitem__这个两个函数。
DataLoader是Pytorch中用来处理模型输入数据的一个工具类。组合了数据集（dataset） + 采样器(sampler)，并在数据集上提供单线程或多线程(num_workers )的可迭代对象。在DataLoader中有多个参数，这些参数中重要的几个参数的含义说明如下：
 ```python

函数原型 torch.utils.data.DataLoader(dataset, batch_size=1, 
    shuffle=False, sampler=None, 
    batch_sampler=None, num_workers=0, 
    collate_fn=None, pin_memory=False, 
    drop_last=False, timeout=0, 
    worker_init_fn=None, multiprocessing_context=None)

1. epoch：所有的训练样本输入到模型中称为一个epoch； 
 2. iteration：一批样本输入到模型中，成为一个Iteration;
 3. batchszie：批大小，决定一个epoch有多少个Iteration；
 4. 迭代次数（iteration）=样本总数（epoch）/批尺寸（batchszie）
 5. dataset (Dataset) – 决定数据从哪读取或者从何读取；
 6. batch_size (python:int, optional) – 批尺寸(每次训练样本个数,默认为１）
 7. shuffle (bool, optional) –每一个 epoch是否为乱序 (default: False)；
 8. num_workers (python:int, optional) – 是否多进程读取数据（默认为０);
 9. drop_last (bool, optional) – 当样本数不能被batchsize整除时，最后一批数据是否舍弃（default: False) #默认设置false不丢弃 要想丢弃需要设置为true
 10. pin_memory（bool, optional) - 如果为True会将数据放置到GPU上去（默认为false） 

```

## 2.torch.nn里面的一些函数和参数
下图展示了自行搭建神经网络中的一些自定义函数操作

~~图片不见啦~~


### 2.1关于tensor张量的一些理解
以下是目前我看到最直观的张量解释：

在深度学习里，Tensor实际上就是一个多维数组（multidimensional array）。而Tensor的目的是能够创造更高维度的矩阵、向量。

![alt text](https://pic4.zhimg.com/v2-91fefb9a6227e8c11f8df316bc30cbb5_r.jpg)

现在将三维的张量用一个正方体来表示：

![alt text](https://pic1.zhimg.com/v2-b279b633e5f17f7a0519601d99689298_r.jpg)

这样子可以进一步生成更高维的张量：

![alt text](https://pic3.zhimg.com/80/v2-00d07246473e1b195b4b69a84fec280a_720w.jpg)

“其实深度学习里的tensor就是一种GPU上的数据结构，n维数组，没有物理和数学上的性质”没有必要泰国纠结意义。

这个右图里面的代码是写入了一个二维矩阵，这个二维矩阵就是对应左图的里面的图像，然后并将其转化为二维tensor的数据类型。

对于这个又该怎么理解 [64,3,32,32]

四个参数分别解释为：图片数量、通道数、长、宽
(batch_size channel hight weight)
如果出现-1 意味着自动计算 而不是真的-1



### 2.2池化操作Pooling--减少数据量
池化操作Pool是类似取核内的最大值、最小值或者平均值，平均池化如下
![alt text](https://i-blog.csdnimg.cn/blog_migrate/81276e1cf53ca571769e6d8a18cf1109.png)


padding（填充）：是否填充边界，默认是不填充的
![alt text](https://pica.zhimg.com/v2-2a2307d5c20551f1a3e8458c7070cf16_b.webp)

ceil model：池化的时候遇到边界不足的情况是否保留比池化核小的里面的最值 true的时候保留 false就不保留

strid（步长）：核每次滑动的距离，一般默认是kenel_size，没必要每次1步，因为会有一些重复的池化操作
滑动卷积核时，我们会先从输入的左上角开始，每次往左滑动一列或者往下滑动一行逐一计算输出，我们将每次滑动的行数和列数称为stride

![alt text](https://pic3.zhimg.com/v2-294159b043a917ea622e1794b4857a34_b.webp)

### 2.3卷积操作

深度学习里面所谓的卷积运算，其实它被称为互相关（cross-correlation）运算：将图像矩阵中，从左到右，由上到下，取与滤波器同等大小的一部分，每一部分中的值与滤波器中的值对应相乘后求和，最后的结果组成一个矩阵，其中没有对核进行翻转。卷积会影响原来图像中tensor的中的通道数

![alt text](https://pica.zhimg.com/v2-705305fee5a050575544c64067405fce_b.webp)

### 2.4多通道卷积
实际上，大多数输入图像都有 RGB 3个通道。这里就要涉及到“卷积核”和“filter”这两个术语的区别。在只有一个通道的情况下，“卷积核”就相当于“filter”，这两个概念是可以互换的。但在一般情况下，它们是两个完全不同的概念。每个“filter”实际上恰好是“卷积核”的一个集合，在当前层，每个通道都对应一个卷积核，且这个卷积核是独一无二的。

多通道卷积的计算过程：将矩阵与滤波器对应的每一个通道进行卷积运算，最后相加，形成一个单通道输出，加上偏置项后，我们得到了一个最终的单通道输出。如果存在多个filter，这时我们可以把这些最终的单通道输出组合成一个总输出。

这里我们还需要注意一些问题——滤波器的通道数、输出特征图的通道数。

（至于下面的图中为什么卷积之后出来的3* 3 *3 之后变成了 4 * 4 我也不懂。）

**复制b站上的一句话方便理解：几个卷积核就是几通道的。一个卷积核作用完RGB三个通道后会把得到的三个矩阵的对应值相加，也就是说会合并，所以一个卷积核产生一个通道。**

![alt text](https://pic3.zhimg.com/v2-fc70463d7f82f7268ee23b7235515f4a_r.jpg)

### 2.5非线性激活（ReLu，Sigmoid，Tanh）
激活函数是指在多层神经网络中，上层神经元的输出和下层神经元的输入存在一个函数关系，这个函数就是激活函数。如下图所示，上层神经元通过加权求和，得到输出值，然后被作用一个激活函数，得到下一层的输入值。引入激活函数的目的是为了增加神经网络的非线性拟合能力。

![alt text](https://pic1.zhimg.com/v2-5858ca1d385fa40c877b324ff488561a_b.webp?consumer=ZHI_MENG)

### 2.6 torch.nn里面的层
torch.nn里面包含很多封装好的层，需要重点关注的有线形层Linear，卷积层Conv2d、非线性激活函数Relu等等

![alt text](https://i-blog.csdnimg.cn/blog_migrate/db8d971ff3e42d103b383546b2e4fe66.png)



## 3.模型的保存和读取
此处只记录保存方式1（非官方），可以保存整个网络的结构和参数，具体操作如下：
```python
vgg = torchvision.models.vgg16(weights= None) #加载模型
torch.save(vgg16,"vgg16_modle.pth") #将vgg16模型另存为vgg16_modle.pth文件
vgg16_model = torch.load("vgg16_modle.pth") #将vgg16_modle.pth模型加载为vgg16_modle


#一般来说保存和加载是在两个分开不同的文件中进行的，在训练那边保存好模型之后再在应用那边加载模型
```

## 4.一些杂记
### 4.1
torchvision：专门用于处理是视觉的torch，里面有准备好的数据集，例如coco数据集

tensorboard：用于显示训练的一些损失函数的情况，我的理解就是显示张量的黑板

貌似torch里面都集成的都是一些类，具体使用还得要使用这些类来创造对象之后再通过对象来调用方法。具体有什么函数我不太记得了。

### 4.2
nn.Xxx和nn.functional.xxx的实际功能是相同的，即nn.Conv2d和nn.functional.conv2d 都是进行卷积，nn.Dropout 和nn.functional.dropout都是进行dropout，运行效率也是近乎相同。

![alt text](https://i-blog.csdnimg.cn/blog_migrate/ff059353a56c5d58049ccda19e5ee754.png)

## 5.训练模型的大致步骤
1.获得dataset

2.使用dataloader读取dataset

3.创建网络模型和必要的参数

4.设置损失函数和优化器，此处包含三个小步骤

    4.1梯度清零
    4.2反向传播
    4.3梯度更新