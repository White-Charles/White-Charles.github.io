---
title: AttributeError:'KerasTensor' object has no attribute' 神经网络，问题解决
date: 2024-02-20 17:01:31
tags: [Python, Machine_Learning, Neural_Network]
---

# AttributeError: 'KerasTensor' object has no attribute' 在神经网络中遇到，问题解决。
这里我遇到的问题不仅仅是版本的问题，可能是在图神经网络的构建中，某些操作将不被允许。这些操作 x.numpy() 在构建模型之外是允许的，在模型中不行。
原因比较复杂，参考了github上的一些文章，最后找到了解决的办法:
禁用TF2中某些操作的函数
强制开启了TF2中某些操作的函数 `tf.config.run_functions_eagerly(True)`

``` python
import tensorflow as tf
tf.config.run_functions_eagerly(True)
tf.compat.v1.disable_v2_behavior()
tf.compat.v1.disable_eager_execution()
if tf.executing_eagerly():
	x.numpy() # type(x) >> 'KerasTensor'
```

我实际执行的环境： (TF=2.14.0, python=3.9.18, keras=2.14.0).
我参考的网页
[https://www.tensorflow.org/api_docs/python/tf/config/run_functions_eagerly](https://www.tensorflow.org/api_docs/python/tf/config/run_functions_eagerly)
[https://github.com/onnx/keras-onnx/issues/651#issuecomment-872061539](https://github.com/onnx/keras-onnx/issues/651#issuecomment-872061539)
