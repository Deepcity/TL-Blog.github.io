---
title: PyTorch实战-CV-classification
date: 2024-09-03 21:49:02
categories:
- 数据分析
- 机器学习
- PyTorch
tags: [PyTorch, CV, 分类器]
---

# pyTorch

实战中领悟torch的函数含义，须有基础神经网络结构概念，并有一定线性代数基础

## 对图像识别的神经网络模型构建

目标：实现一个可分类不同衣物图像的神经网络

## 张量

<!--more-->

### 属性

shape、dtype、device，分别表示维度，数据类型与存储在什么设备上

### 函数使用

`torch.tensor`将数组转化为张量

`torch.from_numpy`从numpy转化为张量

`.numpy` 转换为numpy

`torch.ones_like`从一个张量复制到另一个张量

`torch.rand`随机填充（由元组或数组决定维度）填充小于1的小数，扩展`torch.ones`，`torch.zeros`填充1，0

`tensor.to` 将张量转存，常用参数如，‘cpu’,’cuda’

`tensor[ ],tensor[:, ] tensor[…, ]`行列下标索引

`tersor.T`即对tensor的转置

`torch.cat()`即对tensor的相连，即将列表或元组中的的tensor整合为一个tensor

加减乘除

- `*`指的是对每个对应位置元素分别相乘 `.mul`
- `@` 指的是矩阵乘法`.matmul` 

## 数据集 Dataset

### 引入数据集

通过`torchvision`加载对应数据集

```python
import torch
from torch.utils.data import Dataset
from torchvision import datasets
from torchvision.transforms import ToTensor, Lambda
import matplotlib.pyplot as plt

training_data = datasets.FashionMNIST(
    root="data",
    train=True,
    download=True,
    transform=ToTensor()
)

test_data = datasets.FashionMNIST(
    root="data",
    train=False,
    download=True,
    transform=ToTensor()
)
```

我们可以见到

```python
datasets.FashionMNIST(
    root="data",
    train=False,
    download=True,
    transform=ToTensor()
)
```

这个语法，解析一下，其中`root`参数是指定数据根目录，`train`表示当前这个数据集是否是训练集，`download`则表示该训练集是否不可用时在网上下载，`transform`则是指定转换数据为何种数据结构`target_transform`。

这就是使用torchvision中datasets包引用数据集的基本方法。

此处的trainning_data以及test_data均为datasets.FashionMNIST类型。

### 通过可视化直观感受数据集

```python
labels_map = {
    0: "T-Shirt",
    1: "Trouser",
    2: "Pullover",
    3: "Dress",
    4: "Coat",
    5: "Sandal",
    6: "Shirt",
    7: "Sneaker",
    8: "Bag",
    9: "Ankle Boot",
}
figure = plt.figure(figsize=(8, 8))
cols, rows = 3, 3
for i in range(1, cols * rows + 1):
    sample_idx = torch.randint(len(training_data), size=(1,)).item()
    img, label = training_data[sample_idx]
    figure.add_subplot(rows, cols, i)
    plt.title(labels_map[label])
    plt.axis("off")
    plt.imshow(img.squeeze(), cmap="gray")
plt.show()
```

通过调用`print(training_data[sample_idx])`可以发现每一个训练数据都是由多维维元组构成的，前面一部分浮点数list描述了每一行每一列单通道的像素明暗度，表示了一幅图案，后一个整型数字这就是ce测试数据的label

### 规范化数据

通过Dataloader导入数据，其中batch_size表示了每一次批次中的数量，shuffle表示随机下标

```python
from torch.utils.data import DataLoader

train_dataloader = DataLoader(training_data, batch_size=64, shuffle=True)
test_dataloader = DataLoader(test_data, batch_size=64, shuffle=True)
```

通过Dataloader索引图像

```python
# Display image and label.
train_features, train_labels = next(iter(train_dataloader))
print(f"Feature batch shape: {train_features.size()}")
print(f"Labels batch shape: {train_labels.size()}")
img = train_features[0].squeeze()
label = train_labels[0]
plt.imshow(img, cmap="gray")
plt.show()
label_name = list(labels_map.values())[label]
print(f"Label: {label_name}")
```

### 对数据进行变形

并非所有数据都适合机器学习的final input，因此，需要对一些数据进行变形。

在此之前，我们需要了解datasets中的数据特征

1. 他们都是有两部分组成的features与labels的元组
2. 前一部分描述数据，后一部分描述标签（这里暂时只考虑图像，即单通道或多通道的明暗list与一个表示类别的整型数字）

可以使用`transform`来进行数据的整理，`target_transform`进行标签的整理。

下面是一个使用Lambda匿名函数的示例

```python
from torchvision import datasets
from torchvision.transforms import ToTensor, Lambda

ds = datasets.FashionMNIST(
    root="data",
    train=True,
    download=True,
    transform=ToTensor(),
    target_transform=Lambda(lambda y: torch.zeros(10, dtype=torch.float).scatter_(0, torch.tensor(y), value=1))
)
```

其中有个常见的函数，这里提一下，详细可以看源码里的注释

`scatter_(dim,index,value)`就是将对应index坐标中的值更改为value，dim则是指定的维度

具体到这个实例中就是弄成，比如T-shirt的标签是

`tensor[1,0,0,0,0,0,0,0,0,0]`

1. 

## 模型生成

### 模型相关调试

神经网络的工作原理我就不在这里再重复了，在我的MindSpore概念章中已经提到了有关知识。

但我仍要针对torch的部分说一下

首先是引入部分

```python
import os
import torch
from torch import nn
from torch.utils.data import DataLoader
from torchvision import datasets, transforms
```

这里引用了nn，transform这两个比较难以理解的东西，后面用到了再提

```python
device = 'cuda' if torch.cuda.is_available() else 'cpu'
print('Using {} device'.format(device))
```

这里并**没有设定实际的运行设备**而是输出信息！

### 模型引入

直到这里，可以开始写第一个pyTorch框架下的模型了

```python
class NeuralNetwork(nn.Module):
    def __init__(self):
        super(NeuralNetwork, self).__init__()
        self.flatten = nn.Flatten()
        self.linear_relu_stack = nn.Sequential(
            nn.Linear(28*28, 512),
            nn.ReLU(),
            nn.Linear(512, 512),
            nn.ReLU(),
            nn.Linear(512, 10),
            nn.ReLU()
        )

    def forward(self, x):
        x = self.flatten(x)
        logits = self.linear_relu_stack(x)
        return logits
```

吸引眼球的函数`nn.Flatten`,来看一下这个函数是如何描述的，首先，Flatten这个单词表示扁平化，可以联想到我们在运算过程中的降维

Shape:
        - Input: :$$(*, S_{\text{start}},..., S_{i}, ..., S_{\text{end}}, *)$$
          where :$S_{i}$ is the size at dimension :math:`i` and :math:`*` means any
          number of dimensions including none.
                - Output: :math:$(*, \prod_{i=\text{start}}^{\text{end}} S_{i}, *)$.

注意这里的连乘指的是大小连乘而并非是数值连乘，也就是说如下所示：

```python
Examples::
        >>> input = torch.randn(32, 1, 5, 5)
        >>> # With default parameters
        >>> m = nn.Flatten()
        >>> output = m(input)
        >>> output.size()
        torch.Size([32, 25])
        >>> # With non-default parameters
        >>> m = nn.Flatten(0, 2)
        >>> output = m(input)
        >>> output.size()
        torch.Size([160, 5])
```

然后了解一下nn.Linear函数，这个函数的__init\_\_是这样描述的

```python
def __init__(self,
             in_features: int,
             out_features: int,
             bias: bool = True,
             device: Any = None,
             dtype: Any = None) -> None
```

他有五个参数，输入feature，输出feature，bias（偏差），设备，数据类型

可见，这里使用nn.Module定义了一个神经网络，由于我们的数据是28*28的图像，这里我们设置：

第一个in_features28\*28的,out_feature则是512的

而后是一个512到512的中间层（hidden layer），然后是一个512到10的输出层。

每层都采用ReLU作为激活函数

然后我们注意力放到`forword`前向传播函数中，在这里我们设定了这个模型的Flatten函数以及数据如何通过神经网络层

### 实例化模型并使用

```python
model = NeuralNetwork().to(device)
print(model)
```

我们指定一下示例到哪里运行

这里device可以有很多取值，但在我们的示例中，仅仅只有‘cpu’,’gpu’两种取值

接下来就是使用这一个模型

首先，我们通过torch.rand随机出一个参数X（1，28，28）

在这之后，我们需要注意，不能直接使用model.forward()函数，而是需要以X为输入并返回一个十维的行预测值

然后我们来了解一个函数,`nn.Softmax`

Softmax is defined as:

$$
\text{Softmax}(x_{i}) = \frac{\exp(x_i)}{\sum_j \exp(x_j)}
$$
它对指定维度的数字完成归一化的操作，使得他们的和为1而一定程度上保留其数字特征

```python
X = torch.rand(1, 28, 28, device=device)
logits = model(X) 
pred_probab = nn.Softmax(dim=1)(logits)
y_pred = pred_probab.argmax(1)
print(f"Predicted class: {y_pred}")
```

通过这段代码，我们实际上使模型第一次*流过了*数据，但这显然不是神经网络，他没有学到任何东西，于是，我们需要聚焦权值和偏移量。

#### Weights and Bias

首先，让我们观察一下第一层的两个变量

```python
print(f"First Linear weights: {model.linear_relu_stack[0].weight} \n")

print(f"First Linear biases: {model.linear_relu_stack[0].bias} \n")
```

看到这些数据，心里的石头终于安心的似了。它也是存在的！

> 这里再说一下Flatten：
>
> 虽然前面解释了这个函数，但很容易注意到，为什么start_dim这个默认值是1呢？这是因为我们输入图像数据的时候，第一维装进去的是一个批次很多个图像数据。
>
> 是直接第一行放到第一个，第二行放到第二个吗？实践一下
>
> ```python
> tensor1 = torch.tensor([[0,1,1,0],[1,2,3,4]])
> print(tensor1.size())
> tensor2 = nn.Flatten()(tensor1)
> print(tensor2)
> 
> tensor1 = torch.tensor([[[0,1],[1,0]],[[1,2],[3,4]]])
> tensor2 = nn.Flatten()(tensor1)
> print(tensor2)
> ```
>
> 确实，他就是将不同行按序合并到了同一行，如果指定维度只有1维则不变

#### nn.Sequential

nn.Sequential 是模块的有序容器。数据按照定义的顺序传递到所有模块。您可以使用顺序容器来快速组合出类似 seq_modules 的网络。

```python
seq_modules = nn.Sequential(
    flatten,
    layer1,
    nn.ReLU(),
    nn.Linear(20, 10)
)
input_image = torch.rand(3,28,28)
logits = seq_modules(input_image)
```

#### 模型参数查看

```python
print("Model structure: ", model, "\n\n")

for name, param in model.named_parameters():
    print(f"Layer: {name} | Size: {param.size()} | Values : {param[:2]} \n")
```

#### 自动梯度下降

```python
# torch.autograd 自动梯度下降
import torch

x = torch.ones(5)  # input tensor
y = torch.zeros(3)  # expected output
w = torch.randn(5, 3, requires_grad=True)
b = torch.randn(3, requires_grad=True)
z = torch.matmul(x, w)+b
loss = torch.nn.functional.binary_cross_entropy_with_logits(z, y)
```

以上是一个简单的梯度下降示例，其中使用损失函数是二元交叉熵损失函数。

`randn`是一个产生正态分布的随机数的函数

#### 计算图,梯度下降函数

```python
print('Gradient function for z =',z.grad_fn)
print('Gradient function for loss =', loss.grad_fn)
```

这里就比较难以理解了,似乎z是一个数组而已，怎么根据它求导函数呢，事实上，我们要明白并牢记这里的z并非是一个数组，而是一个张量，并且它由w,b两个设定了requires_grad=True的张量计算而来，因此，这里的z就是关于w,b的因变量，也是根据他们两个求导

更要清楚，我们应用于张量以构建计算图的函数是 Function 类的对象。此对象知道如何在正向计算函数，以及如何在反向传播步骤中计算其导数。对反向传播函数的引用存储在张量的 grad_fn 属性中。

#### 计算导数

我们在特定的`x` 和  `y`下计算 $\frac{\partial loss}{\partial w}$ and$\frac{\partial loss}{\partial b}$  。

可以通过调用`loss.backward()`, 然后获取 `w.grad` 和 `b.grad`参数

```python
loss.backward()
print(w.grad)
print(b.grad)
```

这里就有点令人不解了，为什么在获得有关两个自变量的导数时需要先使用一下loss.backward()呢

> `loss.backward()` 是一个非常重要的函数，它用于自动计算梯度。
>
> 它实际上是由如下几个步骤组成的
>
> 1. **计算梯度**：`loss.backward()` 会计算损失函数关于网络参数（如权重和偏置）的梯度。这是通过反向传播算法完成的，该算法从输出层开始，逐层向后计算梯度。
> 2. **累积梯度**：在PyTorch中，梯度是累积的，这意味着如果你多次调用 `loss.backward()` 而不更新参数，梯度会累加。这在某些情况下是有用的，比如在RMSprop或Adam这样的优化器中，它们需要计算梯度的一阶和二阶矩。
> 3. **准备参数更新**：计算完梯度后，这些梯度会被用于参数的更新。通常，你会在调用 `loss.backward()` 之后，使用优化器（如 `optimizer.step()`）来更新参数。
> 4. **清除旧梯度**：在每次迭代开始之前，通常需要清除旧的梯度，以避免梯度累积。这可以通过调用 `optimizer.zero_grad()` 或 `model.zero_grad()` 来实现。
>
> **backward中的形参**
>
> 1. **gradient（可选）**：这是一个用来指示目标张量相对于该张量的梯度的张量。如果指定了 `gradient`，它的形状必须与目标张量相同。如果不指定，PyTorch 会默认使用 1 作为梯度。
>
>    ```python
>    import torch
>    
>    x = torch.tensor([1.0], requires_grad=True)
>    y = (x + 1) ** 2
>    
>    # 假设我们想要将梯度缩放为 2
>    loss = y
>    loss.backward(gradient=torch.tensor([2.0]))
>    
>    print(x.grad)  # 输出: tensor([4.])
>    ```
>
>    在这个例子中，`y = (x + 1)^2` 的导数是 `2 * (x + 1)`。如果我们不指定 `gradient` 参数，`x.grad` 将会是 `[4.]`（因为 `2 * (1 + 1)`）。但是，我们通过指定 `gradient=torch.tensor([2.0])`，实际上是将损失函数对 `x` 的影响放大了 2 倍，所以最终的梯度是 `[8.]` 而不是 `[4.]`。
>
> 2. **retain_graph（可选）**：这是一个布尔值，用于指定是否保留计算图。默认情况下，`retain_graph=False`，这意味着计算图会在 `backward()` 调用后被释放，以节省内存。如果你需要再次对同一个图进行反向传播（例如，在同一个网络中进行多次反向传播），你可以设置 `retain_graph=True`。
>
> 3. **retain_variables（可选）**：这是一个布尔值，用于指定是否保留用于计算梯度的变量。默认情况下，`retain_variables=False`。如果你需要在 `backward()` 调用后再次使用这些变量，可以设置 `retain_variables=True`。

#### 避免梯度计算

```python
with torch.no_grad():
    z = torch.matmul(x, w) + b
```

通过这样的代码就能**阻断**梯度计算了。

这种代码对java选手简直就是天书，即使c++选手也会两眼一黑，这里不得不提到python的特性之一，上下文管理。参考 [python补充.md](python补充.md) 

值得注意的是：梯度计算是一个**链式过程**，即他是在有向无环图DAGs上进行的反向传播。

**什么情况下会用到阻断梯度计算**

1. 希望冻结参数运行神经网络模型
2. 希望加速神经网络模型并只进行前向计算

#### 雅各比行列式

在多元函数的求导中存在着这么一种求导法则，即雅各比行列式。

对函数$\vec{y}=f(\vec{x})$，当$\vec{x}=\langle x_1,\dots,x_n\rangle$，$\vec{y}=\langle y_1,\dots,y_m\rangle$， $\vec{y}$ 对$\vec{x}$的导数是一个包含$\frac{\partial y_{i}}{\partial x_{j}}$雅阁比行列式 $J_{ij}$ 。

Pytorch允许以计算 $v^T\cdot J$替代$v=(v_1 \dots v_m)$。这是通过以v作为backward的参数实现的v的大小应该与原始张量的大小相同，要根据原始张量计算乘积。

```python
inp = torch.eye(5, requires_grad=True)
out = (inp+1).pow(2)
out.backward(torch.ones_like(inp), retain_graph=True)
print("First call\n", inp.grad)
out.backward(torch.ones_like(inp), retain_graph=True)
print("\nSecond call\n", inp.grad)
inp.grad.zero_()
out.backward(torch.ones_like(inp), retain_graph=True)
print("\nCall after zeroing gradients\n", inp.grad)
```

`torch.eye`返回一个指定维的单位矩阵

`retain_graph`形参表示累加梯度计算值，若没有此项，重复backward将会报错

`inp.grad.zero_`原地函数，设置梯度值为零

值得注意的是gradient形参，该参数是高级用法之一，他最直观的作用就是将求出来的导数乘以一个矩阵(对应位置的乘法)

> 1. **自定义梯度**：在某些高级用例中，你可能需要为特定的操作或自定的损失函数指定非标准的梯度。例如，在使用强化学习或者某些特殊的优化算法时，你可能需要根据自定义的规则来计算梯度。
> 2. **梯度裁剪**：在训练神经网络时，可能会出现梯度爆炸的问题。在这种情况下，你可能需要在执行反向传播之前对梯度进行裁剪，以防止梯度值过大。通过 `gradient` 参数，你可以在计算梯度时直接应用梯度裁剪，而不是在梯度计算完成后再进行。
> 3. **多任务学习**：在多任务学习中，不同的任务可能需要对同一个网络层的输出有不同的梯度贡献。通过为不同的任务指定不同的 `gradient` 参数，你可以精确控制每个任务对网络参数更新的影响。
> 4. **避免梯度覆盖**：在某些复杂的模型或者动态计算图中，你可能需要在不同的时间点对同一个张量计算不同的梯度。使用 `gradient` 参数可以在不干扰其他计算的情况下，为特定的计算路径指定梯度。
> 5. **效率**：在某些情况下，直接在 `backward()` 中指定 `gradient` 参数可能比在梯度计算完成后再进行操作更高效。这可以减少中间变量的创建和操作，从而优化内存使用和计算速度。

#### 构建优化参数循环

1. 设定超参数

   ```python
   learning_rate = 1e-3 # 学习率
   batch_size = 64 # 批大小
   epochs = 5 # 训练轮数
   ```

2. 设置损失函数

   ```python
   loss_fn = nn.CrossEntropyLoss()
   ```

3. 优化算法

   所有优化的逻辑都封装在`optimizer`对象中。在这里，我们使用SGD优化器；在PyTorch中，还有许多不同的优化器，如`ADAM`和`RMSProp`，它们适用于不同类型的模型和数据。

   ```python
   optimizer = torch.optim.SGD(model.parameters(), lr=learning_rate)
   ```

4. 完整实现优化循环

   ```python
   def train_loop(dataloader, model, loss_fn, optimizer):
       size = len(dataloader.dataset)
       for batch, (X, y) in enumerate(dataloader):        
           # Compute prediction and loss
           pred = model(X)
           loss = loss_fn(pred, y)
           
           # Backpropagation
           optimizer.zero_grad()
           loss.backward()
           optimizer.step()
   
           if batch % 100 == 0:
               loss, current = loss.item(), batch * len(X)
               print(f"loss: {loss:>7f}  [{current:>5d}/{size:>5d}]")
   
   
   def test_loop(dataloader, model, loss_fn):
       size = len(dataloader.dataset)
       test_loss, correct = 0, 0
   
       with torch.no_grad():
           for X, y in dataloader:
               pred = model(X)
               test_loss += loss_fn(pred, y).item()
               correct += (pred.argmax(1) == y).type(torch.float).sum().item()
               
       test_loss /= size
       correct /= size
       print(f"Test Error: \n Accuracy: {(100*correct):>0.1f}%, Avg loss: {test_loss:>8f} \n")
   ```

   这里有几个需要注意的函数

   `optimizer.step()`：该函数根据相应点导数值调用优化器优化参数（目前知道这一点即可）

   `with torch.no_grad():`该代码块中利用上下文机制暂时关闭对应的反向传播

5. 真正调用模型开始训练循环

   ```python
   for t in range(epochs):
       print(f"Epoch {t+1}\n-------------------------------")
       train_loop(train_dataloader, model, loss_fn, optimizer)
       test_loop(test_dataloader, model, loss_fn)
   print("Done!")
   ```

## 保存参数

```python
torch.save(model.state_dict(), "data/model.pth")
print("Saved PyTorch Model State to model.pth")
```

## 加载参数

想要加载参数，显然，我们首先要保证神经网络模型是严格相同的。

使用`load_state_dict()`方法加载.pth神经网络模型

```python
model = NeuralNetwork()
model.load_state_dict(torch.load('data/model.pth'))
model.eval()
```

其中`eval`函数用以设置模型维评估模式，这样的模式确保了关闭Dropout层以及Batch Normalization批量归一化层，确保了评估模式的准确性。

## 开放式神经网络（Open Neural Network Exchange,ONNE）

**开放神经网络交换格式（Open Neural Network Exchange, ONNX）** 运行时为此提供了一种解决方案，它允许你在任何硬件、云端或边缘设备上一次训练模型并加速推理过程。

ONNX是一种通用格式，许多厂商支持通过该格式来共享神经网络和其他机器学习模型。你可以使用ONNX格式在其他编程语言和框架（如Java、JavaScript、C#和ML.NET）中对模型进行推理。

### 在ONNP中导出模型

导出模型主要涉及一个函数`onnx.export`，他存在于torch库中

1. 一个神经网络模型
2. 一个输入层维数的零向量
3. 一个文件路径，用以保存.onnx的模型参数

```python
input_image = torch.zeros((1,28,28))
onnx_model = 'data/model.onnx'
onnx.export(model, input_image, onnx_model)
```

## 异常处理

针对执行中可能会出现一些问题

### 发现我是用CPU跑的，我应该如何换用cuda

应该首先确认是否支持cuda

1. `nvidia-smi`shell查询是否支持

   ![navidia-smi](./../../../AppData/Roaming/Typora/typora-user-images/image-20240830151101652.png)

   如上图，我的显卡是3060，因此支持（可以看到QQ正在用！：）QQ用3060跑虚幻吗）

2. 安装cuda：[CUDA Toolkit Archive | NVIDIA Developer](https://developer.nvidia.com/cuda-toolkit-archive)

   cuda很大，你忍一下，因为网络原因，这里推荐用下载器下载（如：IDM）

   **注意版本号，到[Start Locally | PyTorch](https://pytorch.org/get-started/locally/) 查询最新支持版本**

3. `nvcc -V` 查询是否安装成功

4. 添加环境变量

   ![cuda-path](https://s2.loli.net/2024/08/30/SuG7jTYBF2VZmri.png)

5. 安装cudnn

   [cuda和cudnn是什么 - 范仁义 - 博客园](https://www.cnblogs.com/Renyi-Fan/p/13458559.html#_label0_2)[cuda和cudnn是什么 - 范仁义 - 博客园](https://www.cnblogs.com/Renyi-Fan/p/13458559.html#_label0_2)[cuda和cudnn是什么 - 范仁义 - 博客园](https://www.cnblogs.com/Renyi-Fan/p/13458559.html#_label0_2)   

   cuDNN是基于CUDA的深度学习GPU加速库，有了它才能在GPU上完成深度学习的计算。

   [cuDNN Archive | NVIDIA Developer](https://developer.nvidia.com/rdp/cudnn-archive)

6. 下载包中对应路径的文件夹的文件粘贴到cuda安装路径下对应的文件夹下

7. 主要使用CUDA内置的deviceQuery.exe 和 bandwithTest.exe两个程序：

   首先启动终端，cd到安装目录下D:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v10.0\extras\demo_suite（这是我的安装路径，默认是在C盘），然后分别执行bandwidthTest.exe和deviceQuery.exe。

    如果以上两步都有Result=PASS，那么就表示安装成功。

8. 结果检查

   ```python
   >>> torch.cuda.is_available()
   True
   >>> torch.cuda.device_count() 
   1
   ```

### 为什么我明明安装了torchvisio包，但仍然无法调用

如果你单纯使用了官网的安装脚本就很有可能出现这个问题。

一个很常见的原因是torch、torchvisio、python、cuda之间的版本并不匹配，如果安装torch时是直接pip install torch torchvisio很大概率会出这个问题（未知原因，可能是网络问题？），按照对应的版本在官网重装torch\torchvision即可

在该网站安装pyTorch[Start Locally | PyTorch](https://pytorch.org/get-started/locally/)

在该网站检查版本依赖并安装torchvision[pytorch/vision: Datasets, Transforms and Models specific to Computer Vision (github.com)](https://github.com/pytorch/vision)

### 报错[RuntimeError]: Expected all tensors to be on the same device

字面含义，参与的运算有多个变量，有的在GPU，有的在CPU上


## 参考文献

1. [简介 - Training | Microsoft Learn](https://learn.microsoft.com/zh-cn/training/modules/intro-machine-learning-pytorch/1-introduction)
2. [cuda的安装，及pytorch调用GPU步骤_gpu cuda使用-CSDN博客](https://blog.csdn.net/qq_58832911/article/details/120567345)
3. [RuntimeError: No such operator torchvision::nms问题解决方法_runtimeerror: operator torchvision::nms does not e-CSDN博客](https://blog.csdn.net/qq_41590635/article/details/112384718)
4. [RuntimeError: No such operator torchvision::nms | 兰秋廿柒的博客 (l-fay.github.io)](https://l-fay.github.io/2020/09/09/pytorchError00/)
5. [【python】使用pip安装指定版本的模块，卸载、查看、更新包_pip install version-CSDN博客](https://blog.csdn.net/weixin_43289135/article/details/120097579)