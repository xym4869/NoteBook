# 基础
## Tensorflow基础
```
import tensorflow as tf;
```

### 张量
```python
import tensorflow as tf
random_float = tf.random.uniform(shape=())#定义一个随机数（标量）
zero_vector = tf.zeros(shape = (2)) # 定义一个有2个元素的零向量
A = tf.constant([[1,2],[3,4]]) # 定义两个2×2的常量矩阵
B = tf.constant([[5,6],[7,8]])
print(A.shape) # 矩形的长和宽
print(A.dtype) # 元素类型，浮点数一般是float32
print(A.numpy()) # 转换成numpy

C = tf.add(A, B)    # 计算矩阵A和B的和
D = tf.matmul(A, B) # 计算矩阵A和B的乘积，矩阵乘法
E = tf.multiply(A,B) # 矩阵点乘
```

    (2, 2)
    <dtype: 'int32'>
    [[1 2]
     [3 4]]


### 自动求导机制
tf.GradientTape()：**求导记录器**， 来实现自动求导。

> 关于with的用法：  
with expression \[as target\]:  
    with_body  
参数说明：  
expression：是一个需要执行的表达式；  
target：是一个变量或者元组，存储的是expression表达式执行返回的结果，可选参数。  

变量与普通张量的一个重要区别是其默认能够被 TensorFlow 的自动求导机制所求导，因此往往被用于定义机器学习模型的参数。  

举例1：计算$y(x) = x^2$在$x=3$时的导数  
举例2：计算$L(w,b) = ||Xw+b-y||^2$在$w=(1,2)^T,b=1$时分别对w，b的偏导数，其中$X=\begin{bmatrix} 1 &2 \\ 3 &4 \end{bmatrix},y = \begin{bmatrix} 1 \\ 2 \end{bmatrix}$


```python
# 1
# 初始化为 3 的 变量，通过在 tf.Variable() 中指定 initial_value 参数来指定初始值
x = tf.Variable(initial_value = 3.) 
# 在 tf.GradientTape() 的上下文内，所有计算步骤都会被记录以用于求导
with tf.GradientTape() as tape:
    y = tf.square(x)
y_grad = tape.gradient(y,x) # 计算y关于x的导数
print(y , y_grad)

# 2
X = tf.constant([[1.,2.],[3.,4,]])
y = tf.constant([[1.],[2.]])
w = tf.Variable(initial_value = [[1.],[2.]])
b = tf.Variable(initial_value = 1.)
with tf.GradientTape() as tape:
    # tf.square() 操作代表对输入张量的每一个元素求平方，不改变张量形状
    # tf.reduce_sum() 操作代表对输入张量的所有元素求和，输出一个形状为空的纯量张量（可以通过 axis 参数来指定求和的维度，不指定则默认对所有元素求和）
    L = tf.reduce_sum(tf.square(tf.matmul(X, w) + b - y))
w_grad, b_grad = tape.gradient(L,[w,b])
print(L, w_grad, b_grad)
```

    tf.Tensor(9.0, shape=(), dtype=float32) tf.Tensor(6.0, shape=(), dtype=float32)
    tf.Tensor(125.0, shape=(), dtype=float32) tf.Tensor(
    [[ 70.]
     [100.]], shape=(2, 1), dtype=float32) tf.Tensor(30.0, shape=(), dtype=float32)


### 线性回归
对多元函数$f(x)$求局部极小值，梯度下降过程如下：
- 初始化自变量为$x_0,k=0$
- 迭代进行下列步骤直到满足收敛条件：
    - 求函数$f(x)$关于自变量的梯度$\nabla f(x_k)$
    - 更新自变量：$x_{k+1}=x_k+\gamma \nabla f(x_k)$，这里的$\gamma$是学习率（也就是梯度下降一次迈出的 “步子” 大小）
    - $k \leftarrow k+1$
    

接下来，我们考虑如何使用程序来实现梯度下降方法，求得线性回归的解
- $min_{a,b}L(a,b)=\sum_{i=1}^{n}(ax_i+b-y_i)^2$


```python
# 归一化
import numpy as np

X_raw = np.array([2013, 2014, 2015, 2016, 2017], dtype=np.float32)
y_raw = np.array([12000, 14000, 15000, 16500, 17500], dtype=np.float32)

X = (X_raw - X_raw.min()) / (X_raw.max() - X_raw.min())
y = (y_raw - y_raw.min()) / (y_raw.max() - y_raw.min())

# NumPy 下的线性回归
a, b = 0, 0
num_epoch = 10000
learning_rate = 5e-4
for e in range(num_epoch):
    # 手动计算损失函数关于自变量（模型参数）的梯度
    y_pred = a * X + b
    grad_a, grad_b = 2 * (y_pred - y).dot(X), 2 * (y_pred - y).sum()
    
    # 更新参数
    a, b = a - learning_rate * grad_a, b - learning_rate * grad_b
    
print(a,b)

# TensorFlow 下的线性回归 
X = tf.constant(X)
y = tf.constant(y)

a = tf.Variable(initial_value = 0.)
b = tf.Variable(initial_value = 0.)
variables = [a,b]

num_epoch = 10000
# 声明了一个梯度下降优化器
optimizer = tf.keras.optimizers.SGD(learning_rate=5e-4)
for e in range(num_epoch):
    # 使用tf.GradientTape()记录损失函数的梯度信息
    with tf.GradientTape() as tape:
        y_pred = a * X + b
        loss = tf.reduce_sum(tf.square(y_pred - y))
    
    # TensorFlow自动计算损失函数关于自变量（模型参数）的梯度
    grads = tape.gradient(loss, variables)
    # TensorFlow自动根据梯度更新参数
    optimizer.apply_gradients(grads_and_vars=zip(grads, variables))
print(a,b)
```

    0.9763702027872221 0.057564988311377796
    <tf.Variable 'Variable:0' shape=() dtype=float32, numpy=0.97637> <tf.Variable 'Variable:0' shape=() dtype=float32, numpy=0.057565063>


P.S. 更新模型参数的方法 optimizer.apply_gradients() 需要提供参数 grads_and_vars，即待更新的变量（如上述代码中的 variables）及损失函数关于这些变量的偏导数（如上述代码中的 grads）。  
具体而言，这里需要传入一个 Python 列表（List），列表中的每个元素是一个（变量的偏导数，变量）对。比如上例中需要传入的参数是 \[(grad_a, a), (grad_b, b)\] 。我们通过 grads = tape.gradient(loss, variables) 求出 tape 中记录的 loss 关于 variables = \[a, b\] 中每个变量的偏导数，也就是 grads = \[grad_a, grad_b\]，再使用 Python 的 zip() 函数将 grads = \[grad_a, grad_b\] 和 variables = \[a, b\] 拼装在一起，就可以组合出所需的参数了。

> zip()函数：返回的是一个 zip 对象，本质上是一个生成器，需要调用 list() 来将生成器转换成列表。  
 ![../../_images/zip.jpg](https://tf.wiki/_images/zip.jpg) 

在实际应用中，我们往往会编写并实例化一个模型类 model = Model() ，然后使用 y_pred = model(X) 调用模型，使用 model.variables 获取模型参数。

## TensorFlow 模型建立与训练
- 模型的构建： tf.keras.Model 和 tf.keras.layers
- 模型的损失函数： tf.keras.losses
- 模型的优化器： tf.keras.optimizer
- 模型的评估： tf.keras.metrics

### 模型（Model）与层（Layer）
层将各种计算流程和变量进行了封装（例如基本的全连接层，CNN 的卷积层、池化层等），而模型则将各种层进行组织和连接，并封装成一个整体，描述了如何将输入数据通过各种层以及运算而得到输出。  
Keras 模型以类的形式呈现，我们可以通过继承 tf.keras.Model 这个 Python 类来定义自己的模型。  
继承 tf.keras.Model 后，我们同时可以使用父类的若干方法和属性，例如在实例化类 model = Model() 后，可以通过 model.variables 这一属性直接获得模型中的所有变量
```python
class MyModel(tf.keras.Model):
    def __init__(self):
        super().__init__()     # Python 2 下使用 super(MyModel, self).__init__()
        # 此处添加初始化代码（包含 call 方法中会用到的层），例如
        # layer1 = tf.keras.layers.BuiltInLayer(...)
        # layer2 = MyCustomLayer(...)

    def call(self, input):
        # 此处添加模型调用的代码（处理输入并返回输出，模型构建），例如
        # x = layer1(input)
        # output = layer2(x)
        return output

    # 还可以添加自定义的方法
```
 ![../../_images/model.png](https://tf.wiki/_images/model.png) 

### 全连接层
全连接层：对输入矩阵$A$进行$f(AW+b)$的线性变换 + 激活函数操作。如果不指定激活函数，即是纯粹的线性变换$AW+b$。  
给定输入张量 input = [batch_size, input_dim] ，该层对输入张量首先进行 tf.matmul(input, kernel) + bias 的线性变换（ kernel 和 bias 是层中可训练的变量），然后对线性变换后张量的每个元素通过激活函数 activation ，从而输出形状为 [batch_size, units] 的二维张量。  
 ![../../_images/dense.png](https://tf.wiki/_images/dense.png) 
其包含的主要参数如下：

- units ：输出张量的维度；
- activation ：激活函数，对应于$f(AW+b)$中的$f$，默认为无激活函数（$a(x) = x$）。常用的激活函数包括 tf.nn.relu 、 tf.nn.tanh 和 tf.nn.sigmoid ；
- use_bias ：是否加入偏置向量 bias ，即$f(AW+b)$中的$b$。默认为 True ；
- kernel_initializer 、 bias_initializer ：权重矩阵 kernel 和偏置向量 bias 两个变量的初始化器。默认为 **tf.glorot_uniform_initializer**。设置为 tf.zeros_initializer 表示将两个变量均初始化为全 0；

该层包含权重矩阵 kernel = [input_dim, units] 和偏置向量 bias = [units] 两个可训练变量，对应于$f(AW+b)$中的$W,b$。
> tf.glorot_uniform_initializer()也称之为Xavier uniform initializer，由一个均匀分布（uniform distribution)来初始化数据。假设均匀分布的区间是[-limit, limit],则$limit=sqrt(6 / (fan\_in + fan_out))$,其中的fan_in和fan_out分别表示输入单元的结点数和输出单元的结点数。  
你可能会注意到， tf.matmul(input, kernel) 的结果是一个形状为 [batch_size, units] 的二维矩阵，这个二维矩阵要如何与形状为 [units] 的一维偏置向量 bias 相加呢？事实上，这里是 TensorFlow 的 **Broadcasting 机制**在起作用，该加法运算相当于**将二维矩阵的每一行加上了 Bias** 。

## Keras Pipeline
以上示例均使用了 Keras 的 **Subclassing API** 建立模型，即对 tf.keras.Model 类进行扩展以定义自己的新模型，同时手工编写了训练和评估模型的流程。  
这种方式灵活度高，且与其他流行的深度学习框架（如 PyTorch、Chainer）共通，是本手册所推荐的方法。  
不过在很多时候，我们只需要建立一个结构相对简单和典型的神经网络（比如上文中的 MLP 和 CNN），并使用常规的手段进行训练。这时，Keras 也给我们提供了另一套更为简单高效的内置方法来建立、训练和评估模型。


```python
# Keras Sequential API 模式建立模型 
# 提供一个层的列表，就能由 Keras 将它们自动首尾相连，形成模型
model = tf.keras.models.Sequential([
    tf.keras.layers.Flatten(),
    tf.keras.layers.Dense(100, activation=tf.nn.relu),
    tf.keras.layers.Dense(10),
    tf.keras.layers.Softmax()
])

# Keras Functional API 模式建立模型
# 将层作为可调用的对象并返回张量,并将输入向量和输出向量提供给 tf.keras.Model 的 inputs 和 outputs 参数
inputs = tf.keras.Input(shape=(28, 28, 1))
x = tf.keras.layers.Flatten()(inputs)
x = tf.keras.layers.Dense(units=100, activation=tf.nn.relu)(x)
x = tf.keras.layers.Dense(units=10)(x)
outputs = tf.keras.layers.Softmax()(x)
model = tf.keras.Model(inputs=inputs, outputs=outputs)
```


```python
# 当模型建立完成后，通过 tf.keras.Model 的 compile 方法配置训练过程
model.compile(
    optimizer=tf.keras.optimizers.Adam(learning_rate=0.001), # 优化器
    loss=tf.keras.losses.sparse_categorical_crossentropy, # 损失函数
    metrics=[tf.keras.metrics.sparse_categorical_accuracy] # 评估指标
)

# 使用 tf.keras.Model 的 fit 方法训练模型
model.fit(data_loader.train_data, data_loader.train_label, epochs=num_epochs, batch_size=batch_size) # 训练数据，目标数据，迭代次数，批次大小，验证数据

# 使用 tf.keras.Model.evaluate 评估训练效果，提供测试数据及标签即可
print(model.evaluate(data_loader.test_data, data_loader.test_label))
```

事实上，我们不仅可以继承 tf.keras.Model 编写自己的模型类，也可以继承 tf.keras.layers.Layer 编写自己的层。


```python
# 自定义层
# 自定义层需要继承 tf.keras.layers.Layer 类，并重写 __init__ 、 build 和 call 三个方法
class MyLayer(tf.keras.layers.Layer):
    def __init__(self):
        super().__init__()
        # 初始化代码

    def build(self, input_shape):     # input_shape 是一个 TensorShape 类型对象，提供输入的形状
        # 在第一次使用该层的时候调用该部分代码，在这里创建变量可以使得变量的形状自适应输入的形状
        # 而不需要使用者额外指定变量形状。
        # 如果已经可以完全确定变量的形状，也可以在__init__部分创建变量
        self.variable_0 = self.add_weight(...)
        self.variable_1 = self.add_weight(...)

    def call(self, inputs):
        # 模型调用的代码（处理输入并返回输出）
        return output
```


```python
# 自定义损失函数需要继承 tf.keras.losses.Loss 类，重写 call 方法即可
class MeanSquaredError(tf.keras.losses.Loss):
    def call(self, y_true, y_pred):
        # 均方差损失函数
        return tf.reduce_mean(tf.square(y_pred - y_true)) # 输出模型预测值和真实值之间通过自定义的损失函数计算出的损失值
```


```python
# 自定义评估指标需要继承 tf.keras.metrics.Metric 类，并重写 __init__ 、 update_state 和 result 三个方法
class SparseCategoricalAccuracy(tf.keras.metrics.Metric):
    def __init__(self):
        super().__init__()
        self.total = self.add_weight(name='total', dtype=tf.int32, initializer=tf.zeros_initializer())
        self.count = self.add_weight(name='count', dtype=tf.int32, initializer=tf.zeros_initializer())

    def update_state(self, y_true, y_pred, sample_weight=None):
        values = tf.cast(tf.equal(y_true, tf.argmax(y_pred, axis=-1, output_type=tf.int32)), tf.int32)
        self.total.assign_add(tf.shape(y_true)[0])
        self.count.assign_add(tf.reduce_sum(values))

    def result(self):
        return self.count / self.total
```

#### 举例：线性连接


```python
# 举例：y_pred = a * X + b
import tensorflow as tf

X = tf.constant([[1.0, 2.0, 3.0], [4.0, 5.0, 6.0]])
y = tf.constant([[10.0], [20.0]])

class Linear(tf.keras.Model):
    def __init__(self):
        super().__init__()
        self.dense = tf.keras.layers.Dense(
            units=1,
            activation=None,
            kernel_initializer=tf.zeros_initializer(),
            bias_initializer=tf.zeros_initializer()
        )
        
    def call(self, input):
        output = self.dense(input)
        return output
    
model = Linear()
optimizer = tf.keras.optimizers.SGD(learning_rate=0.01)
for i in range(100):
    with tf.GradientTape() as tape:
        y_pred = model(X) # 调用模型 y_pred = model(X) 而不是显式写出 y_pred = a * X + b
        # tf.reduce_mean 函数用于计算张量tensor沿着指定的数轴（tensor的某一维度）上的的平均值
        loss = tf.reduce_mean(tf.square(y_pred - y))
    grads = tape.gradient(loss, model.variables)
    optimizer.apply_gradients(grads_and_vars=zip(grads, model.variables)) # 使用 model.variables 直接获得模型中的所有变量
print(model.variables)
```

    [<tf.Variable 'linear_2/dense_1/kernel:0' shape=(3, 1) dtype=float32, numpy=
    array([[0.40784496],
           [1.191065  ],
           [1.9742855 ]], dtype=float32)>, <tf.Variable 'linear_2/dense_1/bias:0' shape=(1,) dtype=float32, numpy=array([0.78322077], dtype=float32)>]

上述例子中，没有显式地声明 a 和 b 两个变量并写出 $y\_pred = a * X + b$ 这一线性变换，而是建立了一个继承了 $tf.keras.Model$ 的模型类 $Linear$ 。  
这个类在初始化部分实例化了一个 **全连接层** （ $tf.keras.layers.Dense$ ），并在 call 方法中对这个层进行调用，实现了线性变换的计算。  


```python
# 使用自定义层方式
class LinearLayer(tf.keras.layers.Layer):
    def __init__(self, units):
        super().__init__()
        self.units = units

    def build(self, input_shape):     # 这里 input_shape 是第一次运行call()时参数inputs的形状
        self.w = self.add_variable(name='w',
            shape=[input_shape[-1], self.units], initializer=tf.zeros_initializer())
        self.b = self.add_variable(name='b',
            shape=[self.units], initializer=tf.zeros_initializer())

    def call(self, inputs):
        y_pred = tf.matmul(inputs, self.w) + self.b
        return y_pred
    
class LinearModel(tf.keras.Model):
    def __init__(self):
        super().__init__()
        self.layer = LinearLayer(units=1)

    def call(self, inputs):
        output = self.layer(inputs)
        return output
```

### 多层感知机（MLP）
步骤：
1. 使用 tf.keras.datasets 获得数据集并预处理
2. 使用 tf.keras.Model 和 tf.keras.layers 构建模型
3. 构建模型训练流程，使用 tf.keras.losses 计算损失函数，并使用 tf.keras.optimizer 优化模型
4. 构建模型评估流程，使用 tf.keras.metrics 计算评估指标

 ![../../_images/mlp.png](https://tf.wiki/_images/mlp.png) 


```python
# 举例：使用多层感知机完成 MNIST 手写体数字图片数据集的分类任务。
# 在 TensorFlow 中，图像数据集的一种典型表示是 [图像数目，长，宽，色彩通道数] 的四维张量

# 使用了 tf.keras.datasets 快速载入 MNIST 数据集。
class MNISTLoader():
    def __init__(self):
        mnist = tf.keras.datasets.mnist # 将从网络上自动下载 MNIST 数据集并加载
        (self.train_data, self.train_label), (self.test_data, self.test_label) = mnist.load_data()
        # MNIST中的图像默认为uint8（0-255的数字）。以下代码将其归一化到0-1之间的浮点数，并在最后增加一维作为颜色通道
        # 灰度图片，色彩通道数为 1; 彩色 RGB 图像色彩通道数为 3
        self.train_data = np.expand_dims(self.train_data.astype(np.float32)/255.0, axis = -1) # 使用 np.expand_dims() 函数为图像数据手动在最后添加一维通道。
        self.test_data = np.expand_dims(self.test_data.astype(np.float32)/255.0, axis = -1)
        self.train_label = self.train_label.astype(np.int32)
        self.test_label = self.test_label.astype(np.int32)
        self.num_train_data, self.num_test_data = self.train_data.shape[0], self.test_data.shape[0]
        
    def get_batch(self, batch_size):
        # 从数据集中随机取出batch_size个元素并返回
        index = np.random.randint(0, np.shape(self.train_data)[0], batch_size)
        return self.train_data[index, :], self.train_label[index]
```

    test accuracy: 0.978600



```python
# 构造模型
# 该模型输入一个向量（比如这里是拉直的 1×784 手写体数字图片），输出 10 维的向量，分别代表这张图片属于 0 到 9 的概率。
class MLP(tf.keras.Model):
    def __init__(self):
        super().__init__()
        # Flatten层将除第一维（batch_size）以外的维度展平
        self.flatten = tf.keras.layers.Flatten()
        self.dense1 = tf.keras.layers.Dense(units=100, activation=tf.nn.relu)
        self.dense2 = tf.keras.layers.Dense(units=10)
        
    def call(self, inputs): # [batch_size, 28, 28, 1]
        x = self.flatten(inputs) # [batch_size, 784]
        x = self.dense1(x) # [batch_size, 100]
        x = self.dense2(x) # [batch_size, 10]
        # 为了满足，该向量中的每个元素均在 [0, 1] 之间且所有元素之和为 1，使用softmax函数
        output = tf.nn.softmax(x) # 归一化指数函数, softmax 函数能够凸显原始向量中最大的值，并抑制远低于最大值的其他分量
        return output
```


```python
# 模型训练
num_epochs = 5
batch_size = 50
learning_rate = 0.001

model = MLP()
data_loader = MNISTLoader()
optimizer = tf.keras.optimizers.Adam(learning_rate=learning_rate)

for i in range(num_epochs):
    num_batches = int(data_loader.num_train_data // batch_size * num_epochs)
    for batch_index in range(num_batches):
        # 从 DataLoader 中随机取一批训练数据
        X, y = data_loader.get_batch(batch_size)
        with tf.GradientTape() as tape:
            # 将这批数据送入模型，计算出模型的预测值
            y_pred = model(X)
            # 将模型预测值与真实值进行比较，计算损失函数（loss）
            loss = tf.keras.losses.sparse_categorical_crossentropy(y_true=y, y_pred=y_pred) # 交叉熵损失函数
            # 同 loss = tf.keras.losses.categorical_crossentropy(
            #                  y_true=tf.one_hot(y, depth=tf.shape(y_pred)[-1]),
            #                  y_pred=y_pred
            #              )
            #  sparse 的含义是，真实的标签值 y_true 可以直接传入 int 类型的标签类别
            loss = tf.reduce_mean(loss)
        # 计算损失函数关于模型变量的导数
        grads = tape.gradient(loss, model.variables)
        # 求出的导数值传入优化器,更新模型参数以最小化损失函数
        optimizer.apply_gradients(grads_and_vars=zip(grads, model.variables))
```


```python
# 模型评估
sparse_categorical_accuracy = tf.keras.metrics.SparseCategoricalAccuracy() # SparseCategoricalAccuracy 评估器输出预测正确的样本数占总样本数的比例
num_batches = int(data_loader.num_test_data // batch_size)
for batch_index in range(num_batches):
    start_index, end_index = batch_index * batch_size, (batch_index + 1) * batch_size
    y_pred = model.predict(data_loader.test_data[start_index:end_index])
    # 每次通过 update_state() 方法向评估器输入两个参数： y_pred 和 y_true ，即模型预测出的结果和真实结果。
    # 评估器具有内部变量来保存当前评估指标相关的参数数值
    sparse_categorical_accuracy.update_state(y_true=data_loader.test_label[start_index:end_index], y_pred=y_pred)
print("test accuracy: %f" % sparse_categorical_accuracy.result()) # result() 方法输出最终的评估指标值
```

### CNN
卷积神经网络 （Convolutional Neural Network, CNN）是一种结构类似于人类或动物的 视觉系统 的人工神经网络，包含一个或多个卷积层（Convolutional Layer）、池化层（Pooling Layer）和全连接层（Fully-connected Layer）。  
池化层（Pooling Layer）可以理解为对图像进行降采样的过程，对于每一次滑动窗口中的所有值，输出其中的最大值（MaxPooling）、均值或其他方法产生的值。


```python
# 使用 Keras 实现卷积神经网络
class CNN(tf.keras.Model):
    def __init__(self):
        super().__init__()
        self.conv1 = tf.keras.layers.Conv2D(
            filters=32, # 卷积层神经元（卷积核）数目
            kernel_size=[5, 5], # 感受野大小
            padding='same', # padding策略（vaild 或 same），当我们将 padding 参数设为 same 时，会将周围缺少的部分使用 0 补齐，使得输出的矩阵大小和输入一致。
            activation=tf.nn.relu # 激活函数
            )
        self.pool1 = tf.keras.layers.MaxPool2D(pool_size=[2, 2], strides=2) # strides 参数即可设置步长
        self.conv2 = tf.keras.layers.Conv2D(
            filters=64,
            kernel_size=[5, 5],
            padding='same',
            activation=tf.nn.relu
            )
        self.pool2 = tf.keras.layers.MaxPool2D(pool_size=[2, 2], strides=2)
        self.flatten = tf.keras.layers.Reshape(target_shape=(7 * 7 * 64,))
        self.dense1 = tf.keras.layers.Dense(units=1024, activation=tf.nn.relu)
        self.dense2 = tf.keras.layers.Dense(units=10)
        
    def call(self, inputs):
        x = self.conv1(inputs) # [batch_size, 28, 28, 32]
        x = self.pool1(x) # [batch_size, 14, 14, 32]
        x = self.conv2(x) # [batch_size, 14, 14, 64]
        x = self.pool2(x) # [batch_size, 7, 7, 64]
        x = self.flatten(x) # [batch_size, 7 * 7 * 64]
        x = self.dense1(x) # [batch_size, 1024]
        x = self.dense2(x) # [batch_size, 10]
        output = tf.nn.softmax(x)
        return output
```

 ![../../_images/cnn.png](https://tf.wiki/_images/cnn.png) 

tf.keras.applications 中有一些预定义好的经典卷积神经网络结构，如 VGG16 、 VGG19 、 ResNet 、 MobileNet 等。我们可以直接调用这些经典的卷积神经网络结构（甚至载入预训练的参数），而无需手动定义网络结构。  
各网络模型参数的详细介绍可参考[Keras 文档 ](https://keras.io/api/applications/)

MobileNetV2 网络结构：
- input_shape ：输入张量的形状（不含第一维的 Batch），大多默认为 224 × 224 × 3 。一般而言，模型对输入张量的大小有下限，长和宽至少为 32 × 32 或 75 × 75 ；
- include_top ：在网络的最后是否包含全连接层，默认为 True ；
- weights ：预训练权值，默认为 'imagenet' ，即为当前模型载入在 ImageNet 数据集上预训练的权值。如需随机初始化变量可设为 None ；
- classes ：分类数，默认为 1000。修改该参数需要 include_top 参数为 True 且 weights 参数为 None 。

WARNING:absl:Dataset tf_flowers is hosted on GCS. It will automatically be downloaded to your
local data directory. If you'd instead prefer to read directly from our public
GCS bucket (recommended if you're running on GCP), you can instead pass
`try_gcs=True` to `tfds.load` or set `data_dir=gs://tfds-data/datasets`.


```python
# 使用 MobileNetV2 网络在 tf_flowers 五分类数据集上进行训练
import tensorflow as tf
import tensorflow_datasets as tfds

num_batches = 1000
batch_size = 50
learning_rate = 0.001

dataset = tfds.load("tf_flowers", split=tfds.Split.TRAIN, as_supervised=True)
dataset = dataset.map(lambda img, label: (tf.image.resize(img, [224, 224]) / 255.0, label)).shuffle(1024).batch(32)
model = tf.keras.applications.MobileNetV2(weights=None, classes=5)
optimizer = tf.keras.optimizers.Adam(learning_rate=learning_rate)
for images, labels in dataset:
    with tf.GradientTape() as tape:
        labels_pred = model(images)
        loss = tf.keras.losses.sparse_categorical_crossentropy(y_true=labels, y_pred=labels_pred)
        loss = tf.reduce_mean(loss)
        print("loss %f" % loss.numpy())
    grads = tape.gradient(loss, model.trainable_variables)
    optimizer.apply_gradients(grads_and_vars=zip(grads, model.trainable_variables))
```

### RNN
循环神经网络（Recurrent Neural Network, RNN）是一种适宜于处理序列数据的神经网络，被广泛用于语言模型、文本生成、机器翻译等。    
循环神经网络是一个处理时间序列数据的神经网络结构。循环神经网络的核心是状态s，是一个特定维数的向量，类似于神经网络的“记忆”。在t=0的初始时刻，$s_0$被赋予一个初始值（常用的为全0向量），然后，用类似于递归的方法来描述循环神经的工作过程，即在t时刻，我们假设$s_{t-1}$已经求出，关注如何在此基础上求出$s_t$：
- 我们假设输入向量$x_t$，状态$s$和输出相关$o_t$的维度分别为$m,n,p$,则$U \in \mathbb{R}^{m \times n},W \in \mathbb{R}^{n \times n},V \in \mathbb{R}^{n \times p}$
- 对输入向量$x_t$通过矩阵$U$进行线性变换
- 对$s_{t-1}$通过矩阵$W$进行线性变换
- 将上述得到的两个向量相加并通过激活函数，作为当前状态$s_t$的值，即$s_t = f(Ux_t+Ws_{t-1})$。即，当前状态的值是上一状态的值和当前输入进行某种信息整合而产生的
- 对当前状态$s_t$通过矩阵$V$进行线性变换，得到当前状态的输出$o_t$

 ![../../_images/rnn_cell.jpg](https://tf.wiki/_images/rnn_cell.jpg) 

RNN有很多改进版本，如 LSTM（长短期记忆神经网络，**解决了长序列的梯度消失问题**，适用于较长的序列）、GRU 等。


```python
# 使用 RNN 来进行尼采风格文本的自动生成，这个任务的本质其实预测一段英文文本的接续字母的概率分布

# 读取文本，并以字符为单位进行编码
# 设字符种类数为 num_chars ，则每种字符赋予一个 0 到 num_chars - 1 之间的唯一整数编号 i
class DataLoader():
    def __init__(self):
        path = tf.keras.utils.get_file('nietzsche.txt', origin='https://s3.amazonaws.com/text-datasets/nietzsche.txt')
        with open(path, encoding='utf-8') as f:
            self.raw_text = f.read().lower()
        self.chars = sorted(list(set(self.raw_text)))
        self.char_indices = dict((c, i) for i, c in enumerate(self.chars))
        self.indices_char = dict((i, c) for i, c in enumerate(self.chars))
        self.text = [self.char_indices[c] for c in self.raw_text]
        
    def get_batch(self, seq_length, batch_size):
        seq = []
        next_char = []
        for i in range(batch_size):
            index = np.random.randint(0, len(self.text) - seq_length)
            seq.append(self.text[index : index + seq_length])
            next_char.append(self.text[index + seq_length])
        return np.array(seq), np.array(next_char) # [batch_size, seq_length], [num_batch]
```

我们首先对序列进行 “One Hot” 操作，即将序列中的每个字符的编码 i 均变换为一个 num_char 维向量，其第 i 位为 1，其余均为 0。变换后的序列张量形状为 [seq_length, num_chars] 。  
然后，我们初始化 RNN 单元的状态，存入变量 state 中。接下来，将序列从头到尾依次送入 RNN 单元，即在 t 时刻，将上一个时刻 t-1 的 RNN 单元状态 state 和序列的第 t 个元素 inputs[t, :] 送入 RNN 单元，得到当前时刻的输出 output 和 RNN 单元状态。取 RNN 单元最后一次的输出，通过全连接层变换到 num_chars 维，即作为模型的输出。  
 ![../../_images/rnn_single.jpg](https://tf.wiki/_images/rnn_single.jpg)    ![../../_images/rnn.jpg](https://tf.wiki/_images/rnn.jpg) 


```python
# 模型构建
class RNN(tf.keras.Model):
    def __init__(self, num_chars, batch_size, seq_length):
        super().__init__()
        self.num_chars = num_chars
        self.seq_length = seq_length
        self.batch_size = batch_size
        self.cell = tf.keras.layers.LSTMCell(units=256)
        self.dense = tf.keras.layers.Dense(units=self.num_chars)
        
    def call(self, inputs, from_logits=False):
        inputs = tf.one_hot(inputs, depth=self.num_chars)
        state = self.cell.get_initial_state(batch_size=self.batch_size, dtype=tf.float32)
        for t in range(self.seq_length):
            output, state = self.cell(inputs[:,t,:], state)
        logits = self.dense(output)
        if from_logits:
            return logits
        else:
            return tf.nn.softmax(logits)
```


```python
num_batches = 1000
seq_length = 40
batch_size = 50
learning_rate = 1e-3

data_loader = DataLoader()
model = RNN(num_chars=len(data_loader.chars), batch_size=batch_size, seq_length=seq_length)
optimizer = tf.keras.optimizers.Adam(learning_rate=learning_rate)
for batch_index in range(num_batches):
    X, y = data_loader.get_batch(seq_length, batch_size)
    with tf.GradientTape() as tape:
        y_pred = model(X)
        loss = tf.keras.losses.sparse_categorical_crossentropy(y_true=y, y_pred=y_pred)
        loss = tf.reduce_mean(loss)
#         print("batch %d: loss %f" % (batch_index, loss.numpy()))
    grads = tape.gradient(loss, model.variables)
    optimizer.apply_gradients(grads_and_vars=zip(grads, model.variables))
```


```python
# 模型测试，但我没跑通，报错了
def predict(self, inputs, temperature=1.):
    batch_size, _ = tf.shape(inputs)
    logits = self(inputs, from_logits=True)
    prob = tf.nn.softmax(logits / temperature).numpy()
    return np.array([np.random.choice(self.num_chars, p=prob[i, :])
                         for i in range(batch_size.numpy())])

X_, _ = data_loader.get_batch(seq_length, 1)
for diversity in [0.2, 0.5, 1.0, 1.2]:
    X = X_
    print("diversity %f:" % diversity)
    for t in range(400):
        y_pred = model.predict(X, diversity)
        print(data_loader.indices_char[y_pred[0]], end='', flush=True)
        X = np.concatenate([X[:, 1:], np.expand_dims(y_pred, axis=1)], axis=-1)
    print("\n")
```

### 强化学习
我们所熟知的有监督学习是在带标签的已知训练数据上进行学习，得到一个从数据特征到标签的映射（预测模型），进而预测新的数据实例所具有的标签。而强化学习中则出现了两个新的概念，“智能体” 和 “环境”。  
在强化学习中，**智能体通过与环境的交互来学习策略，从而最大化自己在环境中所获得的奖励**。
例如，在下棋的过程中，你（智能体）可以通过与棋盘及对手（环境）进行交互来学习下棋的策略，从而最大化自己在下棋过程中获得的奖励（赢棋的次数）。    
如果说有监督学习关注的是 “预测”，是与统计理论关联密切的学习类型的话，那么强化学习关注的则是 “决策”，与计算机算法（尤其是动态规划和搜索）有着深入关联。

#### 动态规划  
 ![../../_images/value_iteration_case_0.png](https://tf.wiki/_images/value_iteration_case_0.png) 

#### 加入随机性和概率的动态规划  
 ![../../_images/policy_iteration_case_1.png](https://tf.wiki/_images/policy_iteration_case_1.png)  
算法流程：

- 初始化环境
- for i = N-1 downto 0 do
    - （策略评估）计算第 i 层中每个坐标 (i, j) 选择 $\pi(i,j) = \downarrow$和$\pi(i,j) = \searrow$的未来期望$q_1$和$q_2$
    - （策略改进）对第 i 层中每个坐标 (i, j)，取未来期望较大的动作作为$\pi(i,j)$的取值
    - (值更新）根据本轮迭代确定的$\pi(i,j)$的值更新$g(i,j)=max(q_1,q_2)+r(i,j)$
    
#### 环境信息无法直接获得的情况
回到前节的 “策略评估 - 策略改进” 框架，我们现在遇到的最大困难是无法在 “策略评估” 中通过前一阶段的$g(i+1,j),g(i+1,j+1)$和概率参数$p_1,p_2$直接计算每个动作的未来期望$p_1g(i+1,j)+p_2g(i+1,j+1)$（因为概率参数未知）。  
不过，期望的妙处在于：就算我们无法直接计算期望，我们也是**可以通过大量试验估计出期望**的。如果我们用$q(i,j,a)$表示智能体在坐标 (i, j) 选择动作 a 时的未来期望，则我们可以观察智能体在 (i, j) 处选择动作 a 后的 K 次试验结果，取这 K 次结果的平均值作为估计值。  
于是，我们只需将前节 “策略评估” 中的未来期望计算，更换为使用试验估计$a=\downarrow$和$a=\searrow$时的未来期望$q(i,j,a)$，即可在环境概率参数未知的情况下进行 “策略评估” 步骤。值得一提的是，由于我们不需要显式计算期望$p_1g(i+1,j)+p_2g(i+1,j+1)$,所以我们也无须关心$g(i,j)$的值了，前节值更新的步骤也随之省略.  
还有一点值得注意的是，由于试验是一个从上而下的步骤，需要算法为整个路径均提供动作，那么对于那些尚未确定动作$\pi(i,j)$的坐标应该如何是好呢？我们可以对这些坐标使用 “随机动作”，即 50% 的概率选择$\downarrow$，50% 的概率选择$\searrow$，以在试验过程中对两种动作均进行充分的 “探索”。  

算法流程： ![../../_images/q_iteration_case_2.png](https://tf.wiki/_images/q_iteration_case_2.png) 

- 初始化q值
- for i = N-1 downto 0 do
    - （策略评估）试验估计第 i 层中每个坐标 (i, j) 选择$a=\downarrow$和$a=\searrow$的未来期望$q(i,j,\downarrow)$和$q(i,j,\searrow)$
    - （策略改进）对第 i 层中每个坐标 (i, j)，取未来期望较大的动作作为 $\pi(i, j)$ 的取值
    
#### 从直接算法到迭代算法
到目前为止，我们都非常严格地遵循了动态规划中 “划分阶段” 的思想，即按照问题的时间特征将问题分成若干个阶段并依次求解。不过在实际场景中，算法的计算时间往往是有限的，因此我们可能需要算法具有较好的 “渐进特性”，即并不要求算法输出精确的理论最优解，只需能够输出**近似的较优解**，且**解的质量随着迭代次数的增加而提升**。我们往往称这种算法为 “**迭代算法**”。  
为了解决这个问题，我们不妨从更高的层次来审视我们目前的算法做了什么。其实算法的主体是交替进行 “策略评估” 和 “策略改进” 两个步骤。其中，
- “策略评估” 根据智能体在坐标 $(i, j)$ 的动作 $\pi(i, j)$ ，评估在这套动作组合下，智能体在坐标 $(i, j)$ 选择动作 a 的未来期望 $q(i, j, a)$ 。
- “策略改进” 根据上一步计算出的 $q(i, j, a)$ ，选择未来期望最大的动作来更新动作 $\pi(i, j)$ 。

事实上，这一 “策略评估” 和 “策略改进” 的交替步骤并不一定需要按照层的顺序自下而上进行。我们只要确保算法能根据有限的试验结果 “尽量” 反复进行策略评估和策略改进，就能让算法输出的结果 “渐进” 地越变越好。于是，我们考虑以下算法流程
- 初始化 $q(i, j, a)$ 和 $\pi(i, j)$
- repeat
    - 固定智能体的动作 $\pi(i, j)$ 的取值，进行 k 次试验（试验时加入一些随机扰动，使得能 “探索” 更多动作组合，上节也有类似操作）。
    - （策略评估）根据当前 k 次试验的结果，调整智能体的未来期望 $q(i, j, a)$ 的取值，使得 $q(i, j, a)$ 的取值 “尽量” 能够真实反映智能体在当前动作 $\pi(i, j)$ 下的未来期望（上节是精确调整至等于未来期望）。
    - （策略改进）根据当前 $q(i, j, a)$ 的值，选择未来期望较大的动作作为 $\pi(i, j)$ 的取值。
- until 所有坐标的 q 值都不再变化，或总试验次数大于 K  

考虑一种极端情况：假设每轮迭代的试验次数 k 的值足够大，则策略评估步骤中可以将 $q(i, j, a)$ 精确调整为完全等于智能体在当前动作 $\pi(i, j)$ 下的未来期望，事实上就变成了上节算法的 “粗放版”（上节的算法每次只更新一层的 $q(i, j, a)$ 值为精确的未来期望，这里每次都更新了所有的 $q(i, j, a)$ 值。在结果上没有差别，只是多了一些冗余计算）。

q 值的渐进性更新：  
1. 当每轮迭代的试验次数 k 足够大、覆盖的情形足够广，q 值的更新很简单：根据试验结果为每个 (i, j, a) 重新计算一个新的 $\bar{q}(i, j, a)$ ，并替换原有数值即可。$$q_{new}(i,j,a) \leftarrow \bar{q}(i, j, a)$$
2. 当每轮迭代的试验次数 k 有限时，“渐进” 地更新 q 值。也就是说，我们把旧的 q 值向当前试验的结果 \bar{q}(i, j, a) 稍微 “牵引” 一点，作为新的 q 值，从而让新的 q 值更贴近当前试验的结果 \bar{q}(i, j, a) ，即$$q_{\text{new}}(i, j, a) \leftarrow q_{\text{old}}(i, j, a) + \alpha(\underbrace{\bar{q}(i, j, a)}_{\text{target}} - q_{\text{old}}(i, j, a))$$
其中参数 \alpha 控制牵引的 “力度”，我们一般会设一个小一点的数字，比如 0.1 或 0.01。  
通过这种方式，我们既加入了新的试验所带来的信息，又保留了部分旧的知识。其实很多迭代算法都有类似的特点。  
不过， $\bar{q}(i, j, a)$ 的值只有当一次试验完全做完的时候才能获得。在实际更新时往往使用另一种方法，使得我们每走一步都可以更新一次 q 值。  
具体地说，假设某一次试验中我们在数字三角形的坐标 $(i, j)$ 处，通过执行动作 $a = \pi(i, j) + \epsilon$ （ $+ \epsilon$ 代表加上一些探索扰动）而跳到了坐标 $(i’,j’)$（即 “走一步”，可能是 $(i+1, j)$，也可能是 $(i+1, j+1)$），然后又在坐标 $(i’,j’)$ 执行了动作 $a' = \pi(i', j') + \epsilon$ 。这时我们可以用 $r(i', j') + q(i', j', a')$ 来近似替代之前的 $\bar{q}(i, j, a)$ ，如下式所示：
$$q_{\text{new}}(i, j, a) \leftarrow q_{\text{old}}(i, j, a) + \alpha\big(\underbrace{r(i', j') + q(i', j', a')}_{\text{target}} - q_{\text{old}}(i, j, a)\big)$$  
我们甚至可以不需要试验结果中的 $a'$ ，而使用在坐标 $(i’, j’)$ 时两个动作对应的 q 值的较大者 $\max[q(i', j', \downarrow), q(i', j', \searrow)]$ 来代替 $q(i', j', a')$ ，如下式：
$$q_{\text{new}}(i, j, a) \leftarrow q_{\text{old}}(i, j, a) + \alpha\big(\underbrace{r(i', j') + \max[q(i', j', \downarrow), q(i', j', \searrow)]}_{\text{target}} - q_{\text{old}}(i, j, a)\big)$$  

探索策略:  
对于我们前面介绍的，基于试验的算法而言，由于环境里的概率参数是未知的（类似于将环境看做黑盒），所以我们在试验时一般都需要加入一些**随机的 “探索策略”**，以保证试验的结果能覆盖到比较多的情况。否则的话，由于智能体在每个坐标都具有固定的动作 \pi(i, j) ，所以试验的结果会受到极大的限制，导致**陷入局部最优**的情况。  
探索的策略有很多种，在此我们介绍一种较为简单的方法：设定一个概率比例 $\epsilon$ ，以 $\epsilon$ 的概率随机生成动作（ $\downarrow$ 或 $\searrow$ ），以 $1 - \epsilon$ 的概率选择动作 $\pi(i, j)$ 。  
我们可以看到，当 $\epsilon = 1$ 时，相当于完全随机地选取动作。当 $\epsilon = 0$ 时，则相当于没有加入任何随机扰动，直接选择动作 $\pi(i, j)$ 。  
一般而言，在**迭代初始的时候 $\epsilon$ 的取值较大**，以扩大探索的范围。随着迭代次数的增加， $\pi(i, j)$ 的值逐渐变优， $\epsilon$ 的取值会逐渐减小。

大规模问题的求解:  
算法设计有两个永恒的指标：时间和空间。通过将直接算法改造为迭代算法，我们初步解决了算法在时间消耗上的问题。于是我们的下一个挑战就是空间消耗，这主要体现在 q 值的存储上。  
我们来思考一下，当我们具体实现 $q(i, j, a)$ 时，我们需要其能够实现的功能有二：
- q 值映射：给定坐标 $(i, j)$ 和动作 a（ $\downarrow$ 或 $\searrow$ ），可以输出一个 $q(i, j, a)$ 值。
- q 值更新：给定坐标 $(i, j)$、动作 a 和目标值 target，可以更新 q 值映射，使得更新后输出的 $q(i, j, a)$ 距离目标值 target 更近。

事实上，我们有不少近似方法，可以让我们在不使用太多内存的情况下实现一个满足以上两个功能的 $q(i, j, a)$ 。这里介绍一种最流行的方法，即**使用深度神经网络近似实现 $q(i, j, a)$** ：
- q 值映射：将坐标 $(i, j)$ 输入深度神经网络，网络输出在坐标 $(i, j)$ 下的所有动作的 q 值（即 $q(i, j, \downarrow)$ 和 $q(i, j, \searrow)$ ）。
- q 值更新：给定坐标 $(i, j)$、动作 a 和目标值 target，将坐标 $(i, j)$ 输入深度神经网络，网络输出在坐标 $(i, j)$ 下的所有动作的 q 值，取其中动作为 a 的 q 值为 $q(i, j, a)$ ，并定义损失函数 $\text{loss} = (\text{target} - q(i, j, a))^2$ ，使用优化器（例如梯度下降）对该损失函数进行一步优化。

 ![../../_images/q_network.png](https://tf.wiki/_images/q_network.png) 

### 深度强化学习（DRL）
使用深度强化学习玩 CartPole（倒立摆）游戏。
> 倒立摆是控制论中的经典问题，在这个游戏中，一根杆的底部与一个小车通过轴相连，而杆的重心在轴之上，因此是一个不稳定的系统。在重力的作用下，杆很容易倒下。而我们则需要控制小车在水平的轨道上进行左右运动，以使得杆一直保持竖直平衡状态。  
和 Gym 的交互过程很像是一个回合制游戏，我们首先获得游戏的初始状态（比如杆的初始角度和小车位置），然后在每个回合 t，我们都需要在当前可行的动作中选择一个并交由 Gym 执行（比如向左或者向右推动小车，每个回合中二者只能择一），Gym 在执行动作后，会返回动作执行后的下一个状态和当前回合所获得的奖励值（比如我们选择向左推动小车并执行后，小车位置更加偏左，而杆的角度更加偏右，Gym 将新的角度和位置返回给我们。而如果杆在这一回合仍没有倒下，Gym 同时返回给我们一个小的正奖励）。这个过程可以一直迭代下去，直到游戏终止（比如杆倒下了）。

![](https://tf.wiki/_images/cartpole.gif)


```python
# Gym 的基本调用方法，可使用 pip install gym 进行安装
import gym

env = gym.make('CartPole-v1') # 实例化一个游戏环境，参数为游戏名称
state = env.reset() # 初始化环境，获得初始状态
while True:
    env.render() # 对当前帧进行渲染，绘图到屏幕
    action = model.predict(state) # 通过当前状态预测出这时应该进行的动作
    next_state, reward, done, info = env.step(action) # 让环境执行动作，获得执行完动作的下一个状态，动作的奖励，游戏是否已结束以及额外信息
    if done: # 如果游戏结束则退出循环
        break
```

我们的任务就是训练出一个模型，能够根据当前的状态预测出应该进行的一个好的动作。粗略地说，**一个好的动作应当能够最大化整个游戏过程中获得的奖励之和，这也是强化学习的目标**。  
我们使用 tf.keras.Model 建立一个 Q 函数网络（Q-network），用于拟合 Q Learning 中的 Q 函数。这里我们使用较简单的多层全连接神经网络进行拟合。该网络输入当前状态，输出各个动作下的 Q-value（CartPole 下为 2 维，即向左和向右推动小车）。  
对于不同的任务（或者说环境），我们需要根据任务的特点，设计不同的状态以及采取合适的网络来拟合 Q 函数。例如，如果我们考虑经典的打砖块游戏（Gym 环境库中的 Breakout-v0 ），每一次执行动作（挡板向左、向右或不动），都会返回一个 210 * 160 * 3 的 RGB 图片，表示当前屏幕画面。为了给打砖块游戏这个任务设计合适的状态表示，我们有以下分析：
- 砖块的颜色信息并不是很重要，画面转换成灰度也不影响操作，因此可以去除状态中的颜色信息（即将图片转为灰度表示）；
- 小球移动的信息很重要，如果只知道单帧画面而不知道小球往哪边运动，即使是人也很难判断挡板应当移动的方向。因此，必须在**状态中加入表征小球运动方向的信息**。一个简单的方式是将*当前帧与前面几帧的画面进行叠加*，得到一个 210 * 160 * X （X 为叠加帧数）的状态表示；
- 每帧的分辨率不需要特别高，只要能大致表征方块、小球和挡板的位置以做出决策即可，因此对于*每帧的长宽可做适当压缩*。
- 考虑到我们需要从图像信息中提取特征，使用 CNN 作为拟合 Q 函数的网络将更为适合。

## TensorFlow 常用模块
### tf.train.Checkpoint ：变量的保存与恢复

可以使用其 save() 和 restore() 方法将 TensorFlow 中所有包含 Checkpointable State 的对象进行保存和恢复。**Checkpoint 只保存模型的参数，不保存模型的计算过程，因此一般用于在具有模型源代码的时候恢复之前训练好的模型参数。**
```python
# train.py 模型训练阶段
model = MyModel()
checkpoint = tf.train.Checkpoint(myModel=model) # myAwesomeOptimizer=optimizer
checkpoint.save('./save/model.ckpt')
# 例如，在源代码目录建立一个名为 save 的文件夹并调用一次 checkpoint.save('./save/model.ckpt') ，我们就可以在可以在 save 目录下发现名为 checkpoint 、 model.ckpt-1.index 、 model.ckpt-1.data-00000-of-00001 的三个文件，这些文件就记录了变量信息

# test.py 模型使用阶段
model = MyModel()
checkpoint = tf.train.Checkpoint(myModel=model)
checkpoint.restore(tf.train.latest_checkpoint('./save')) # 载入最近的一个
```
> tf.train.Checkpoint 与以前版本常用的 tf.train.Saver 相比，强大之处在于其支持在**即时执行模式下 “延迟” 恢复变量**。具体而言，==当调用了 checkpoint.restore() ，但模型中的变量还没有被建立的时候，Checkpoint 可以等到变量被建立的时候再进行数值的恢复==。即时执行模式下，模型中各个层的初始化和变量的建立是在模型第一次被调用的时候才进行的（好处在于可以根据输入的张量形状而自动确定变量形状，无需手动指定）。这意味着当模型刚刚被实例化的时候，其实里面还一个变量都没有，这时候使用以往的方式去恢复变量数值是一定会报错的。比如，你可以试试在 train.py 调用 tf.keras.Model 的 save_weight() 方法保存 model 的参数，并在 test.py 中实例化 model 后立即调用 load_weight() 方法，就会出错，只有当调用了一遍 model 之后再运行 load_weight() 方法才能得到正确的结果。可见， tf.train.Checkpoint 在这种情况下可以给我们带来相当大的便利。

```python
# 实例：多层感知机模型
import numpy as np
import tensorflow as tf
import argparse

from dataset import MNISTLoader
from model.mlp import MLP

# 存args
parser = argparse.ArgumentParser(description='Process some integers.')
parser.add_argument('--mode', default='train', help='train or test')
parser.add_argument('--num_epochs', default=1)
parser.add_argument('--batch_size', default=50)
parser.add_argument('--learning_rate', default=0.001)
args = parser.parse_args()

data_loader = MNISTLoader()


def train():
    model = MLP()
    optimizer = tf.keras.optimizers.Adam(learning_rate=args.learning_rate)
    num_batches = int(data_loader.num_train_data // args.batch_size * args.num_epochs)
    # 存model的args
    checkpoint = tf.train.Checkpoint(myModel=model)
    for batch_index in range(1, num_batches + 1):
        X, y = data_loader.get_batch(args.batch_size)
        with tf.GradientTape() as tape:
            y_pred = model(X)
            loss = tf.keras.losses.sparse_categorical_crossentropy(y_true=y, y_pred=y_pred)
            loss = tf.reduce_mean(loss)
            print("batch %d: loss %f" % (batch_index, loss.numpy()))
        grads = tape.gradient(loss, model.variables)
        optimizer.apply_gradients(grads_and_vars=zip(grads, model.variables))
        # 每隔100个Batch保存一次，保存模型参数到文件
        if batch_index % 100 == 0:
            path = checkpoint.save('./save/mlpmodel.ckpt')
            print("model saved to %s" % path)

def test():
    model_to_be_restored = MLP()
    # 实例化Checkpoint，设置恢复对象为新建立的模型model_to_be_restored
    checkpoint = tf.train.Checkpoint(myModel=model_to_be_restored)
    checkpoint.restore(tf.train.latest_checkpoint('./save'))
    y_pred = np.argmax(model_to_be_restored.predict(data_loader.test_data), axis=-1)
    print("test accuracy: %f" % (sum(y_pred == data_loader.test_label) / data_loader.num_test_data))


if __name__ == '__main__':
    if args.mode == 'train':
        train()
    if args.mode == 'test':
        test()

```
可以使用 TensorFlow 的 tf.train.CheckpointManager 来满足：
- 在长时间的训练后，程序会保存大量的 Checkpoint，但我们只想保留最后的几个 Checkpoint；
- Checkpoint 默认从 1 开始编号，每次累加 1，但我们可能希望使用别的编号方式（例如使用当前 Batch 的编号作为文件编号）。
```python
checkpoint = tf.train.Checkpoint(myAwesomeModel=model)
# 使用tf.train.CheckpointManager管理Checkpoint
manager = tf.train.CheckpointManager(checkpoint, directory='./save', max_to_keep=3)

# 使用CheckpointManager保存模型参数到文件并自定义编号
path = manager.save(checkpoint_number=batch_index)
```
### TensorBoard：训练过程可视化

```python
# 建立一个文件夹存放 TensorBoard 的记录文件
summary_writer = tf.summary.create_file_writer('./tensorboard')
for batch_index in range(num_batches):
	# ...（训练代码，当前batch的损失值放入变量loss中）
	with summary_wirter.as_default(): # 希望使用的记录器
		tf.summary.scalar("loss", loss, step=batch_index)
		tf.summary.scalar("MyScalar", my_scalar, step=batch_index) # 还可以添加其他自定义的变量
```
每运行一次 tf.summary.scalar() ，记录器就会向记录文件中写入一条记录。除了最简单的标量（scalar）以外，TensorBoard 还可以对其他类型的数据（如图像，音频等）进行可视化.
当我们要对训练过程可视化时，在代码目录打开终端（如需要的话进入 TensorFlow 的 conda 环境），运行:
```
tensorboard --logdir=./tensorboard
```
然后使用浏览器访问命令行程序所输出的网址（一般是 http://name-of-your-computer:6006） ，即可访问 TensorBoard 的可视界面，如下图所示： 
![../../_images/tensorboard.png](https://tf.wiki/_images/tensorboard.png) 
默认情况下，TensorBoard 每 30 秒更新一次数据。不过也可以点击右上角的刷新按钮手动刷新。
注意事项：
- 如果需要重新训练，需要删除掉记录文件夹内的信息并重启 TensorBoard（或者建立一个新的记录文件夹并开启 TensorBoard， --logdir 参数设置为新建立的文件夹）
- 记录文件夹目录保持全英文

### 查看 Graph 和 Profile 信息
我们可以在训练时使用 tf.summary.trace_on 开启 Trace，此时 TensorFlow 会将训练时的大量信息（如计算图的结构，每个操作所耗费的时间等）记录下来。在训练完成后，使用 tf.summary.trace_export 将记录结果输出到文件。
```python
# 开启Trace，可以记录图结构和profile信息
tf.summary.trace_on(graph=True, profile=True)
with summary_writer.as_default():
	# 保存Trace信息到文件
	tf.summary.trace_export(name="model_trace", step=0, profiler_outdir=log_dir)
```
之后，我们就可以在 TensorBoard 中选择 “Profile”，以时间轴的方式查看各操作的耗时情况。 
![../../_images/profiling.png](https://tf.wiki/_images/profiling.png) 
![../../_images/graph.png](https://tf.wiki/_images/graph.png) 

### tf.data ：数据集的构建与预处理
#### 数据集对象的建立

tf.data 的核心是 tf.data.Dataset 类，提供了对数据集的高层封装。tf.data.Dataset 由一系列的可迭代访问的元素（element）组成，每个元素包含一个或多个张量。
最基础的建立 tf.data.Dataset 的方法是使用 tf.data.Dataset.from_tensor_slices() ，适用于数据量较小（能够整个装进内存）的情况。他的所用是切分传入的 Tensor 的第一个维度，生成相应的 dataset 。

1. 对传入的（5,2）进行切分，最终产生的dataset有5个元素，每个元素的形状都是（2，）
```python
import tensorflow as tf

data = tf.data.Dataset.from_tensor_slices(np.random.uniform(size=(5,2)))
print(data)
# output:<TensorSliceDataset shapes: (2,), types: tf.float64>
```
2. 在图像识别中可能出现的字典或者元组的矩阵情况，因为将图像数字化之后，会产生矩阵和对应的标签。
```python
import tensorflow as tf
import numpy as np

dataset = tf.data.Dataset.from_tensor_slices({'a':np.array([1.0,2.0,3.0,4.0,5.0]),'b':np.random.uniform(size=(5,2))})
print(dataset)
# output:<TensorSliceDataset shapes: {a: (), b: (2,)}, types: {a: tf.float64, b: tf.float64}>
# 函数会分别切分”a”中的数值以及”b”中的数值，最后总dataset中的一个元素就是类似于{ “a”:1.0, “b”:[0.9,0.1] }的形式。
```
类似地，我们可以载入前章的 MNIST 数据集：
```python
import matplotlib.pyplot as plt 

(train_data, train_label), (_, _) = tf.keras.datasets.mnist.load_data()
train_data = np.expand_dims(train_data.astype(np.float32) / 255.0, axis=-1)      # [60000, 28, 28, 1]
mnist_dataset = tf.data.Dataset.from_tensor_slices((train_data, train_label))

for image, label in mnist_dataset:
    plt.title(label.numpy())
    plt.imshow(image.numpy()[:, :, 0])
    plt.show()
```
输出：

 ![../../_images/mnist_1.png](https://tf.wiki/_images/mnist_1.png) 
 对于特别巨大而无法完整载入内存的数据集，我们可以先将数据集处理为 TFRecord 格式，然后使用 `tf.data.TFRocrdDataset()` 进行载入。 ( 详情请参考  TFRecord 部分)

#### 数据集对象的预处理
常用：
Dataset.map(f) ：对数据集中的每个元素应用函数 f ，得到一个新的数据集（往往结合 tf.io 进行读写和解码文件， tf.image 进行图像处理）；
```python
# 使用 Dataset.map() 将所有图片旋转 90 度
def rot90(image, label):
    image = tf.image.rot90(image)
    return image, label

mnist_dataset = mnist_dataset.map(rot90)
```
输出：
 ![../../_images/mnist_1_rot90.png](https://tf.wiki/_images/mnist_1_rot90.png) 

Dataset.shuffle(buffer_size) ：
- 设定一个固定大小为 buffer_size 的缓冲区（Buffer）；
- 初始化时，取出数据集中的前 buffer_size 个元素放入缓冲区；
- 每次需要从数据集中取元素时，即从缓冲区中随机采样一个元素并取出，然后从后续的元素中取出一个放回到之前被取出的位置，以维持缓冲区的大小。

```python
mnist_dataset = mnist_dataset.shuffle(buffer_size=10000).batch(4)

for images, labels in mnist_dataset:
    fig, axs = plt.subplots(1, 4)
    for i in range(4):
        axs[i].set_title(labels.numpy()[i])
        axs[i].imshow(images.numpy()[i, :, :, 0])
    plt.show()
```
输出：
 ![第一次运行](https://tf.wiki/_images/mnist_shuffle_1.png) 
第一次运行结果
 ![第二次运行](https://tf.wiki/_images/mnist_shuffle_2.png) 
第二次运行结果

Dataset.batch(batch_size) ：将数据集分成批次，即对每 batch_size 个元素，使用 tf.stack() 在第 0 维合并，成为一个元素。每次的数据都会被随机打散。
```python
mnist_dataset = mnist_dataset.batch(4)

# image: [4, 28, 28, 1], labels: [4]
for images, labels in mnist_dataset:    
    fig, axs = plt.subplots(1, 4)
    for i in range(4):
        axs[i].set_title(labels.numpy()[i])
        axs[i].imshow(images.numpy()[i, :, :, 0])
    plt.show()
```
输出：
 ![../../_images/mnist_batch.png](https://tf.wiki/_images/mnist_batch.png) 

#### 并行化策略
常规训练流程，在准备数据时，GPU 只能空载：
 ![../../_images/datasets_without_pipelining.png](https://tf.wiki/_images/datasets_without_pipelining.png) 

使用 `Dataset.prefetch()` 方法进行数据预加载后的训练流程，在 GPU 进行训练的同时 CPU 进行数据预加载，提高了训练效率：
 ![../../_images/datasets_with_pipelining.png](https://tf.wiki/_images/datasets_with_pipelining.png) 
```python
# AUTOTUNE:tensorflow自动选择合适的数值，也可以手工设置
mnist_dataset = mnist_dataset.prefetch(buffer_size=tf.data.experimental.AUTOTUNE)

# Dataset.map() 也可以利用多 GPU 资源，并行化地对数据项进行变换
mnist_dataset = mnist_dataset.map(map_func=rot90, num_parallel_calls=2)
```
运行过程如下图：
 ![../../_images/datasets_parallel_map.png](https://tf.wiki/_images/datasets_parallel_map.png) 

 #### 数据集元素的获取与使用
 获取：
 ```python
 # For 循环迭代获取数据
dataset = tf.data.Dataset.from_tensor_slices((A, B, C, ...))
for a, b, c, ... in dataset:
    # 对张量a, b, c等进行操作，例如送入模型进行训练
    
# 使用 iter() 显式创建一个 Python 迭代器并使用 next() 获取下一个元素
dataset = tf.data.Dataset.from_tensor_slices((A, B, C, ...))
it = iter(dataset)
a_0, b_0, c_0, ... = next(it)
a_1, b_1, c_1, ... = next(it)
 ```
 Keras 支持使用 tf.data.Dataset 直接作为输入：
 当调用 tf.keras.Model 的 fit() 和 evaluate() 方法时，可以将参数中的输入数据 x 指定为一个元素格式为 (输入数据, 标签数据) 的 Dataset ，并忽略掉参数中的标签数据 y 。
 ```python
 # 常规用法
 model.fit(x=train_data, y=train_label, epochs=num_epochs, batch_size=batch_size)
 
 # 使用 tf.data.Dataset 后
 model.fit(mnist_dataset, epochs=num_epochs)
 ```
#### 实例：cats_vs_dogs 图像分类 
数据集：[下载](https://www.floydhub.com/fastai/datasets/cats-vs-dogs)
以下代码以猫狗图片二分类任务为示例，展示了使用 tf.data 结合 tf.io 和 tf.image 建立 tf.data.Dataset 数据集，并进行训练和测试的完整过程。
```python
import tensorflow as tf
import os

num_epochs = 10
batch_size = 12
learning_rate = 0.001
data_dir = 'keras/dataset/cats-vs-dogs'
train_cats_dir = data_dir + '/train/cats/'
train_dogs_dir = data_dir + '/train/dogs/'
test_cats_dir = data_dir + '/test/cats/'
test_dogs_dir = data_dir + '/test/dogs/'

def _decode_and_resize(filename, label):
    image_string = tf.io.read_file(filename) # 读取原始文件
    image_decoded = tf.image.decode_jpeg(image_string) # 解码JPEG图片
    image_resized = tf.image.resize(image_decoded, [256, 256]) / 255.0
    return image_resized, label

if __name__ == '__main__':
	# 构建训练数据集
    train_cat_filenames = tf.constant([train_cats_dir + filename for filename in os.listdir(train_cats_dir)])
    train_dog_filenames = tf.constant([train_dogs_dir + filename for filename in os.listdir(train_dogs_dir)])
    train_filenames = tf.concat([train_cat_filenames, train_dog_filenames], axis=-1)
    train_labels = tf.concat([
        tf.zeros(train_cat_filenames.shape, dtype=tf.int32),
        tf.ones(train_dog_filenames.shape, dtype=tf.int32)],
        axis=-1)
    
    train_dataset = tf.data.Dataset.from_tensor_slices((train_filenames, train_labels))
    train_dataset = train_dataset.map(
        map_func=_decode_and_resize,
        num_parallel_calls=tf.data.experimental.AUTOTUNE
    )
    train_dataset = train_dataset.shuffle(buffer_size=23000)
    train_dataset = train_dataset.batch(batch_size)
    train_dataset = train_dataset.prefetch(tf.data.experimental.AUTOTUNE)
    
    model = tf.keras.Sequential([
        tf.keras.layers.Conv2D(32, 3, activation='relu', input_shape=(256, 256, 3)),
        tf.keras.layers.MaxPooling2D(),
        tf.keras.layers.Conv2D(32, 5, activation='relu'),
        tf.keras.layers.MaxPooling2D(),
        tf.keras.layers.Flatten(),
        tf.keras.layers.Dense(64, activation='relu'),
        tf.keras.layers.Dense(2, activation='softmax')
    ])
    
    model.compile(
        optimizer=tf.keras.optimizers.Adam(learning_rate=learning_rate),
        loss=tf.keras.losses.sparse_categorical_crossentropy,
        metrics=[tf.keras.metrics.sparse_categorical_accuracy]
    )
    
    model.fit(train_dataset, epochs=num_epochs)
    
    # 构建测试数据集
    test_cat_filenames = tf.constant([test_cats_dir + filename for filename in os.listdir(test_cats_dir)]) # tensor类型，内部为string
    test_dog_filenames = tf.constant([test_dogs_dir + filename for filename in os.listdir(test_dogs_dir)])
    test_filenames = tf.concat([test_cat_filenames, test_dog_filenames], axis=-1)
    test_labels = tf.concat([
        tf.zeros(test_cat_filenames.shape, dtype=tf.int32), 
        tf.ones(test_dog_filenames.shape, dtype=tf.int32)], 
        axis=-1)

    test_dataset = tf.data.Dataset.from_tensor_slices((test_filenames, test_labels))
    test_dataset = test_dataset.map(_decode_and_resize)
    test_dataset = test_dataset.batch(batch_size)

    print(model.metrics_names)
    print(model.evaluate(test_dataset))
```
通过对以上示例进行性能测试，我们可以感受到 tf.data 的强大并行化性能。通过 prefetch() 的使用和在 map() 过程中加入 num_parallel_calls 参数，模型训练的时间可缩减至原来的一半甚至更低。测试结果如下：
 ![../../_images/tfdata_performance.jpg](https://tf.wiki/_images/tfdata_performance.jpg) 

### TFRecord ：TensorFlow 数据集存储格式

 TFRecord 可以理解为一系列序列化的 tf.train.Example 元素所组成的列表文件，而每一个 tf.train.Example 又由若干个 tf.train.Feature 的字典组成。形式如下：
 ```
 # dataset.tfrecords
[
    {   # example 1 (tf.train.Example)
        'feature_1': tf.train.Feature,
        ...
        'feature_k': tf.train.Feature
    },
    ...
    {   # example N (tf.train.Example)
        'feature_1': tf.train.Feature,
        ...
        'feature_k': tf.train.Feature
    }
]
 ```
 为了将形式各样的数据集整理为 TFRecord 格式，我们可以对数据集中的每个元素进行以下步骤：
 - 读取该数据元素到内存；
 - 将该元素转换为 tf.train.Example 对象（每一个 tf.train.Example 由若干个 tf.train.Feature 的字典组成，因此需要先建立 Feature 的字典）；
 - 将该 tf.train.Example 对象序列化为字符串，并通过一个预先定义的 tf.io.TFRecordWriter 写入 TFRecord 文件

读取 TFRecord 数据则可按照以下步骤：
- 通过 tf.data.TFRecordDataset 读入原始的 TFRecord 文件（此时文件中的 tf.train.Example 对象尚未被反序列化），获得一个 tf.data.Dataset 数据集对象；
- 通过 Dataset.map 方法，对该数据集对象中的每一个序列化的 tf.train.Example 字符串执行 tf.io.parse_single_example 函数，从而实现反序列化。

使用的 cats_vs_dogs 二分类数据集的训练集部分转换为 TFRecord 文件，并读取该文件的过程：
```python
import tensorflow as tf
import os

data_dir = 'keras/dataset/cats-vs-dogs'
train_cats_dir = data_dir + '/train/cats/'
train_dogs_dir = data_dir + '/train/dogs/'
tfrecord_file = data_dir + '/train/train.tfrecords'

train_cat_filenames = [train_cats_dir + filename for filename in os.listdir(train_cats_dir)] # list
train_dog_filenames = [train_dogs_dir + filename for filename in os.listdir(train_dogs_dir)]
train_filenames = train_cat_filenames + train_dog_filenames
train_labels = [0] * len(train_cat_filenames) + [1] * len(train_dog_filenames) # 将 cat 类的标签设为0，dog 类的标签设为1

# 迭代读取每张图片，建立 tf.train.Feature 字典和 tf.train.Example 对象，序列化并写入 TFRecord 文件
with tf.io.TFRecordWriter(tfrecord_file) as writer:
    for filename, label in zip(train_filenames, train_labels):
        image = open(filename, 'rb').read() # 读取数据集图片到内存，image 为一个 Byte 类型的字符串
        feature = { # 建立 tf.train.Feature 字典
            'image': tf.train.Feature(bytes_list=tf.train.BytesList(value=[image])), # 图片是一个 Bytes 对象
            'label': tf.train.Feature(int64_list=tf.train.Int64List(value=[label])) # 标签是一个 Int 对象
        }
        example = tf.train.Example(features=tf.train.Features(feature=feature)) # 通过字典建立 Example
        writer.write(example.SerializeToString()) # 将Example序列化并写入 TFRecord 文件
# 即可在 tfrecord_file 所指向的文件地址获得一个 500MB 左右的 train.tfrecords 文件
        
# 读取之前建立的 train.tfrecords 文件，并通过 Dataset.map 方法，使用 tf.io.parse_single_example 函数对数据集中的每一个序列化的 tf.train.Example 对象解码
raw_dataset = tf.data.TFRecordDataset(tfrecord_file) # 读取 TFRecord 文件
feature_description = { # 定义Feature结构，告诉解码器每个Feature的类型是什么
    'image': tf.io.FixedLenFeature([], tf.string),
    'label': tf.io.FixedLenFeature([], tf.int64)
}

def _parse_example(example_string): # 将 TFRecord 文件中的每一个序列化的 tf.train.Example 解码
    feature_dict = tf.io.parse_single_example(example_string, feature_description)
    feature_dict['image'] = tf.io.decode_jpeg(feature_dict['image']) # 解码JPEG图片
    return feature_dict['image'], feature_dict['label']

dataset = raw_dataset.map(_parse_example)

# 获得一个数据集对象 dataset ，这已经是一个可以用于训练的 tf.data.Dataset 对象了！我们从该数据集中读取元素并输出验证
import matplotlib.pyplot as plt

for image, label in dataset:
    plt.title('cat' if label == 0 else 'dog')
    plt.imshow(image.numpy())
    plt.show()
```
显示：
 ![../../_images/tfrecord_cat.png](https://tf.wiki/_images/tfrecord_cat.png) 

tf.train.Feature 支持三种数据格式：
- tf.train.BytesList ：字符串或原始 Byte 文件（如图片），通过 bytes_list 参数传入一个由字符串数组初始化的 tf.train.BytesList 对象;
- tf.train.FloatList ：浮点数，通过 float_list 参数传入一个由浮点数数组初始化的 tf.train.FloatList 对象；
- tf.train.Int64List ：整数，通过 int64_list 参数传入一个由整数数组初始化的 tf.train.Int64List 对象

feature_description 类似于一个数据集的 “描述文件”，通过一个由键值对组成的字典，告知 tf.io.parse_single_example 函数每个 tf.train.Example 数据项有哪些 Feature，以及这些 Feature 的类型、形状等属性。 
tf.io.FixedLenFeature 的三个输入参数 shape 、 dtype 和 default_value （可省略）为每个 Feature 的形状、类型和默认值。这里我们的数据项都是单个的数值或者字符串，所以 shape 为空数组。

### tf.function ：图执行模式*
虽然默认的即时执行模式（Eager Execution）为我们带来了灵活及易调试的特性，但在特定的场合，例如追求高性能或部署模型时，我们希望将模型转换成高效的TensorFlow图模型。
TensorFlow 2 为我们提供了 **tf.function 模块**，结合 **AutoGraph 机制**，使得我们仅需加入一个简单的 **@tf.function** 修饰符，就能轻松将模型以图执行模式运行。
#### 基础使用方法
在 TensorFlow 2 中，推荐使用 tf.function 实现图执行模式，从而将模型转换为易于部署且高性能的 TensorFlow 图模型。只需要将我们希望以图执行模式运行的代码封装在一个函数内，并在函数前加上 @tf.function 即可。
**并不是任何函数都可以被 @tf.function 修饰！！！**建议在函数内**只使用 TensorFlow 的原生操作**，不要使用过于复杂的 Python 语句，**函数参数只包括 TensorFlow 张量或 NumPy 数组**，并最好是能够按照计算图的思想去构建函数
```python
import tensorflow as tf
import time
from zh.model.mnist.cnn import CNN
from zh.model.utils import MNISTLoader

num_batches = 1000
batch_size = 50
learning_rate = 0.001
data_loader = MNISTLoader()
model = CNN()
optimizer = tf.keras.optimizers.Adam(learning_rate=learning_rate)

@tf.function
def train_one_step(X, y):    
    with tf.GradientTape() as tape:
        y_pred = model(X)
        loss = tf.keras.losses.sparse_categorical_crossentropy(y_true=y, y_pred=y_pred)
        loss = tf.reduce_mean(loss)
        tf.print("loss", loss) # 注意这里使用了TensorFlow内置的tf.print()。@tf.function不支持Python内置的print方法
    grads = tape.gradient(loss, model.variables)    
    optimizer.apply_gradients(grads_and_vars=zip(grads, model.variables))

# 测试耗时，结果显示有一定的特性提升
start_time = time.time()
for batch_index in range(num_batches):
    X, y = data_loader.get_batch(batch_size)
    train_one_step(X, y)
end_time = time.time()
print(end_time - start_time)
```
一般而言，当模型由较多小的操作组成的时候， @tf.function 带来的提升效果较大。而当模型的操作数量较少，但单一操作均很耗时的时候，则 @tf.function 带来的性能提升不会太大。
详细内容：[AutoGraph Capabilities and Limitations](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/python/autograph/g3doc/reference/limitations.md)

### tf.TensorArray ：TensorFlow 动态数组*
在部分网络结构，尤其是涉及到时间序列的结构中，我们可能需要将一系列张量以数组的方式依次存放起来，以供进一步处理。如果你需要基于计算图的特性（例如使用 @tf.function 加速模型运行或者使用 SavedModel 导出模型），TensorFlow 提供了 tf.TensorArray ，一种支持计算图特性的 TensorFlow 动态数组。
声明方法：
- arr = tf.TensorArray(dtype, size, dynamic_size=False) ：声明一个大小为 size ，类型为 dtype 的 TensorArray arr 。如果将 dynamic_size 参数设置为 True ，则该数组会自动增长空间。

读取和写入的方法为：
- write(index, value) ：将 value 写入数组的第 index 个位置；
- read(index) ：读取数组的第 index 个值；

请注意，由于需要支持计算图， tf.TensorArray 的 **write() 方法是不可以忽略左值的**！也就是说，在图执行模式下，必须按照以下的形式写入数组：
```python
arr = arr.write(index, value)
# 这样才可以正常生成一个计算图操作，并将该操作返回给 arr

# 而不是
arr.write(index, value)     # 生成的计算图操作没有左值接收，从而丢失
```
简单的示例：
```python
import tensorflow as tf

@tf.function
def array_write_and_read():
    arr = tf.TensorArray(dtype=tf.float32, size=3)
    arr = arr.write(0, tf.constant(0.0))
    arr = arr.write(1, tf.constant(1.0))
    arr = arr.write(2, tf.constant(2.0))
    arr_0 = arr.read(0)
    arr_1 = arr.read(1)
    arr_2 = arr.read(2)
    return arr_0, arr_1, arr_2

a, b, c = array_write_and_read()
print(a, b, c)

# 输出
tf.Tensor(0.0, shape=(), dtype=float32) tf.Tensor(1.0, shape=(), dtype=float32) tf.Tensor(2.0, shape=(), dtype=float32)
```

### tf.config：GPU 的使用与分配
#### 指定当前程序使用的 GPU
通过 tf.config.list_physical_devices ，我们可以获得当前主机上某种特定运算设备类型（如 GPU 或 CPU ）的列表：
```python
gpus = tf.config.list_physical_devices(device_type='GPU')
cpus = tf.config.list_physical_devices(device_type='CPU')
print(gpus, cpus)

# 输出
[PhysicalDevice(name='/physical_device:GPU:0', device_type='GPU'),
 PhysicalDevice(name='/physical_device:GPU:1', device_type='GPU'),
 PhysicalDevice(name='/physical_device:GPU:2', device_type='GPU'),
 PhysicalDevice(name='/physical_device:GPU:3', device_type='GPU')]
[PhysicalDevice(name='/physical_device:CPU:0', device_type='CPU')]
# 可见，该工作站具有 4 块 GPU：GPU:0 、 GPU:1 、 GPU:2 、 GPU:3 ，以及一个 CPU CPU:0 
```
通过 tf.config.set_visible_devices ，可以设置当前程序可见的设备范围（当前程序只会使用自己可见的设备，不可见的设备不会被当前程序使用）。
> 例如，如果在上述 4 卡的机器中我们需要限定当前程序只使用下标为 0、1 的两块显卡（GPU:0 和 GPU:1）
```python
gpus = tf.config.list_physical_devices(device_type='GPU')
tf.config.set_visible_devices(devices=gpus[0:2], device_type='GPU')
```
使用环境变量 CUDA_VISIBLE_DEVICES 也可以控制程序所使用的 GPU.
假设发现四卡的机器上显卡 0,1 使用中，显卡 2,3 空闲，Linux 终端输入:
```shell
export CUDA_VISIBLE_DEVICES=2,3
```
或在代码中加入
```python
import os
os.environ['CUDA_VISIBLE_DEVICES'] = "2,3"
```
#### 设置显存使用策略
默认情况下，TensorFlow 将使用几乎所有可用的显存。TensorFlow 提供两种显存使用策略，让我们能够更灵活地控制程序的显存使用方式：
- 仅在需要时申请显存空间（程序初始运行时消耗很少的显存，随着程序的运行而动态申请显存）；
- 限制消耗固定大小的显存（程序不会超出限定的显存大小，若超出的报错）。

通过 tf.config.experimental.set_memory_growth 将 GPU 的显存使用策略设置为 “仅在需要时申请显存空间”：
```python
gpus = tf.config.list_physical_devices(device_type='GPU')
for gpu in gpus:
    tf.config.experimental.set_memory_growth(device=gpu, enable=True)
```
通过 tf.config.set_logical_device_configuration 选项并传入 tf.config.LogicalDeviceConfiguration 实例，设置 TensorFlow 固定消耗 GPU:0 的 1GB 显存:
```python
gpus = tf.config.list_physical_devices(device_type='GPU')
tf.config.set_logical_device_configuration(
    gpus[0],
    [tf.config.LogicalDeviceConfiguration(memory_limit=1024)])
```
#### 单 GPU 模拟多 GPU 环境
当我们的本地开发环境只有一个 GPU，但却需要编写多 GPU 的程序在工作站上进行训练任务时，TensorFlow 为我们提供了一个方便的功能，可以让我们在本地开发环境中建立多个模拟 GPU，从而让多 GPU 的程序调试变得更加方便。
```python
# 在实体 GPU GPU:0 的基础上建立了两个显存均为 2GB 的虚拟 GPU
gpus = tf.config.list_physical_devices('GPU')
tf.config.set_logical_device_configuration(
    gpus[0],
    [tf.config.LogicalDeviceConfiguration(memory_limit=2048),
     tf.config.LogicalDeviceConfiguration(memory_limit=2048)])
# 输出设备数量
Number of devices: 2
```

# 部署
## TensorFlow 模型导出
为了将训练好的机器学习模型部署到各个目标平台（如服务器、移动端、嵌入式设备和浏览器等），我们的第一步往往是将训练好的整个模型完整导出（序列化）为一系列标准格式的文件。在此基础上，我们才可以在不同的平台上使用相对应的部署工具来部署模型文件。
TensorFlow 提供了**统一模型导出格式 SavedModel**，使得我们训练好的模型可以以这一格式为中介，在多种不同平台上部署，这是我们在 TensorFlow 2 中主要使用的导出格式。同时，基于历史原因，Keras 的 Sequential 和 Functional 模式也有自有的模型导出格式。
### 使用 SavedModel 完整导出模型
 SavedModel 包含了一个 TensorFlow 程序的完整信息：不仅包含参数的权值，还包含计算的流程（即计算图）。
 当模型导出为 SavedModel 文件时，无须模型的源代码即可再次运行模型，这使得 SavedModel 尤其适用于模型的分享和部署。
 Keras 模型均可方便地导出为 SavedModel 格式。不过需要注意的是，因为 SavedModel 基于计算图，所以对于<u>使用继承 tf.keras.Model 类建立的 Keras 模型，其需要导出到 SavedModel 格式的方法（比如 call ）都需要使用 @tf.function 修饰</u>
 ```python
 # 导出
 tf.saved_model.save(model, "保存的目标文件夹名称")
 
 # 载入
 model = tf.saved_model.load("保存的目标文件夹名称")
 ```
 举例：MNIST 手写体识别的模型
 ```python
 # 训练
import tensorflow as tf
from zh.model.utils import MNISTLoader

num_epochs = 1
batch_size = 50
learning_rate = 0.001

model = tf.keras.models.Sequential([
    tf.keras.layers.Flatten(),
    tf.keras.layers.Dense(100, activation=tf.nn.relu),
    tf.keras.layers.Dense(10),
    tf.keras.layers.Softmax()
])

data_loader = MNISTLoader()
model.compile(
    optimizer=tf.keras.optimizers.Adam(learning_rate=0.001),
    loss=tf.keras.losses.sparse_categorical_crossentropy,
    metrics=[tf.keras.metrics.sparse_categorical_accuracy]
)
model.fit(data_loader.train_data, data_loader.train_label, epochs=num_epochs, batch_size=batch_size)
tf.saved_model.save(model, "saved/1") # 导出模型到 saved/1 文件夹

# 测试
import tensorflow as tf
from zh.model.utils import MNISTLoader

batch_size = 50

model = tf.saved_model.load("saved/1")
data_loader = MNISTLoader()
sparse_categorical_accuracy = tf.keras.metrics.SparseCategoricalAccuracy()
num_batches = int(data_loader.num_test_data // batch_size)
for batch_index in range(num_batches):
    start_index, end_index = batch_index * batch_size, (batch_index + 1) * batch_size
    y_pred = model(data_loader.test_data[start_index: end_index])
    sparse_categorical_accuracy.update_state(y_true=data_loader.test_label[start_index: end_index], y_pred=y_pred)
print("test accuracy: %f" % sparse_categorical_accuracy.result())
 ```
 对于使用继承 tf.keras.Model 类建立的 Keras 模型 model ，使用 SavedModel 载入后将无法使用 model() 直接进行推断，而需要使用 model.call() 。call 方法需要以 @tf.function 修饰，以转化为 SavedModel 支持的计算图。
 同举例MLP：
 ```python
 class MLP(tf.keras.Model):
    def __init__(self):
        super().__init__()
        self.flatten = tf.keras.layers.Flatten()
        self.dense1 = tf.keras.layers.Dense(units=100, activation=tf.nn.relu)
        self.dense2 = tf.keras.layers.Dense(units=10)

    @tf.function
    def call(self, inputs):         # [batch_size, 28, 28, 1]
        x = self.flatten(inputs)    # [batch_size, 784]
        x = self.dense1(x)          # [batch_size, 100]
        x = self.dense2(x)          # [batch_size, 10]
        output = tf.nn.softmax(x)
        return output

model = MLP()

# 注意模型推断时需要显式调用 call 方法
y_pred = model.call(data_loader.test_data[start_index: end_index])
 ```
 ### Keras 自有的模型导出格式（Jinpeng）
 由于历史原因，我们在有些场景也会用到 Keras 的 Sequential 和 Functional 模式的自有模型导出格式（H5）。
 我们以 keras 模型训练和保存为例进行讲解，[keras 官方的 mnist 模型训练样例](https://github.com/keras-team/keras/blob/master/examples/mnist_cnn.py)，是基于 keras 的 Sequential 构建了多层的卷积神经网络，并进行训练。
 为了方便起见可使用如下命令拷贝到本地:
```shell
curl -LO https://raw.githubusercontent.com/keras-team/keras/master/examples/mnist_cnn.py
```
 然后，在最后加上如下一行代码（主要是对 keras 训练完毕的模型进行保存）:
 ```python
 model.save('mnist_cnn.h5')
 ```
 在终端执行mnist_cnn.py 文件：
 ```shell
 python mnist_cnn.py
 ```
 该过程需要连接网络获取 [mnist.npz 文件](https://s3.amazonaws.com/img-datasets/mnist.npz)，会被保存到 $HOME/.keras/datasets/ 。如果网络连接存在问题，可以通过其他方式获取 mnist.npz 后，直接保存到该目录即可。
 执行结束后，会在当前目录产生 mnist_cnn.h5 文件（HDF5 格式），就是 keras 训练后的模型，其中已经包含了训练后的模型结构和权重等信息。在服务器端，可以直接通过 keras.models.load_model("mnist_cnn.h5") 加载，然后进行推理；在移动设备需要将 HDF5 模型文件转换为 TensorFlow Lite 的格式，然后通过相应平台的 Interpreter 加载，然后进行推理。

 ## TensorFlow Serving
当我们将模型训练完毕后，往往需要将模型在生产环境中部署。最常见的方式，是在服务器上提供一个 API，即客户机向服务器的某个 API 发送特定格式的请求，服务器收到请求数据后通过模型进行计算，并返回结果。
TensorFlow 为我们提供了 TensorFlow Serving 这一组件，能够帮助我们在实际生产环境中灵活且高性能地部署机器学习模型。
### TensorFlow Serving 安装
- 推荐 [使用 Docker 部署 TensorFlow Serving](https://www.tensorflow.org/tfx/serving/docker)
- 依赖环境较少的 [apt-get 安装 ](https://www.tensorflow.org/tfx/serving/setup#installing_using_apt)

### TensorFlow Serving 模型部署 
- 直接读取 SavedModel 格式的模型进行部署
```shell
tensorflow_model_server \
    --rest_api_port=端口号（如8501） \
    --model_name=模型名 \
    --model_base_path="SavedModel格式模型的文件夹绝对地址（不含版本号）"
```
- Keras Sequential 模式模型的部署：由于 Sequential 模式的输入和输出都很固定，因此这种类型的模型很容易部署，无需其他额外操作
```shell
# 使用 Keras Sequential 模式建立 以 MLP 的模型名在 8501 端口进行部署
tensorflow_model_server \
    --rest_api_port=8501 \
    --model_name=MLP \
    --model_base_path="/home/.../.../saved"  # 文件夹绝对地址根据自身情况填写，无需加入版本号
```
- 自定义 Keras 模型的部署：
使用继承 tf.keras.Model 类建立的自定义 Keras 模型的自由度相对更高。因此当使用 TensorFlow Serving 部署模型时，对导出的 SavedModel 文件也有更多的要求：
	- 需要导出到 SavedModel 格式的方法（比如 call ）不仅需要使用 @tf.function 修饰，还要在修饰时指定 input_signature 参数，以显式说明输入的形状。该参数传入一个由 tf.TensorSpec 组成的列表，指定每个输入张量的形状和类型。
	```python
	# 如，对于 MNIST 手写体数字识别，我们的输入是一个 [None, 28, 28, 1] 的四维张量（ None 表示第一维即 Batch Size 的大小不固定）
	class MLP(tf.keras.Model):
    ...

    @tf.function(input_signature=[tf.TensorSpec([None, 28, 28, 1], tf.float32)])
    def call(self, inputs):
        ...
	```
	- 在将模型使用 tf.saved_model.save 导出时，需要通过 signature 参数提供待导出的函数的签名（Signature）。简单说来，由于自定义的模型类里可能有多个方法都需要导出，因此，需要告诉 TensorFlow Serving 每个方法在被客户端调用时分别叫做什么名字。
	```python
	# 例如，如果我们希望客户端在调用模型时使用 call 这一签名来调用 model.call 方法时，我们可以在导出时传入 signature 参数，以 dict 的键值对形式告知导出的方法对应的签名
	model = MLP()
	...
	tf.saved_model.save(model, "saved_with_signature/1", signatures={"call": model.call})
	```
以上两步均完成后，即可使用以下命令部署：
```shell
tensorflow_model_server \
    --rest_api_port=8501 \
    --model_name=MLP \
    --model_base_path="/home/.../.../saved_with_signature"  # 修改为自己模型的绝对地址
```
### 在客户端调用以 TensorFlow Serving 部署的模型
[链接](https://tf.wiki/zh_hans/deployment/serving.html#zh-hans-call-serving-api)
## TensorFlow Lite（Jinpeng）
TensorFlow Lite 是 TensorFlow 在移动和 IoT 等边缘设备端的解决方案，提供了 Java、Python 和 C++ API 库，可以运行在 Android、iOS 和 Raspberry Pi 等设备上。
[链接](https://tf.wiki/zh_hans/deployment/lite.html)
## TensorFlow in JavaScript（Huan）
TensorFlow.js 是 TensorFlow 的 JavaScript 版本，支持 GPU 硬件加速，可以运行在 Node.js 或浏览器环境中。它不但支持完全基于 JavaScript 从头开发、训练和部署模型，也可以用来运行已有的 Python 版 TensorFlow 模型，或者基于现有的模型进行继续训练。
[链接](https://tf.wiki/zh_hans/deployment/javascript.html)
