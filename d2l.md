# d2l 的一些配置

pip install scrapy -i https://pypi.tuna.tsinghua.edu.cn/simple
http://mirrors.aliyun.com/pypi/simple/
http://pypi.mirrors.ustc.edu.cn/simple/

# conda环境

conda create --name d2l python=3.8 -y

conda activate d2l

# data

"""TF的输出"""

矩阵范数在torch和tf中都是Frobenius norm。

$$\|\mathbf{X}\|_{F}=\sqrt{\sum_{i=1}^{m} \sum_{j=1}^{n} x_{i j}^{2}}$$

tf中，向量的变化
```python
tic = time.time()
C_tf = tf.Variable(tf.zeros(shape=[4096, 4096]))
for i in range(4096):
    C_tf[:, i].assign(tf.linalg.matvec(A_tf, B_tf[:, i]))
toc = time.time()
print(toc - tic)
```

TF GPU 占用内存报错
```
Blas GEMM launch failed 
```
解决方案
```python
gpus = tf.config.experimental.list_physical_devices('GPU')
tf.config.experimental.set_memory_growth(gpus[0], True)
```