---
layout: post
tags: tensorflow
title: 如何使用TensorBoard
---

1.对于使用tensorflow构建的图，我们在最后加上
~~~python
writer = tf.summary.FileWriter("./log", tf.get_default_graph())
writer.close()
~~~
完整demo如下
~~~python
'''
计算a = (b + c) * (c + 2)
Tensorflow是基于graph的并行计算模型
圆形或方形的节点被称为node，在node中流动的数据流被称为张量(tensor)
'''

import tensorflow as tf

# 使用tf.constant()定义常量，使用tf.Variable()定义变量
const = tf.constant(2.0, name='const')
# Tensorflow可以自动进行数据类型检测，
# 比如：赋值2.0就默认为tf.float32，但最好还是显式地定义
b = tf.Variable(2.0, name='b')
c = tf.Variable(1.0, dtype=tf.float32, name='c')
# 创建operation
d = tf.add(b, c, name='d')
e = tf.add(c, const, name='e')
a = tf.multiply(d, e, name='a')

# TensorFlow中所有的变量必须经过初始化才能使用
# 1. 定义init operation
init_op = tf.global_variables_initializer()

# 以上已经完成TensorFlow graph的搭建，下一步即计算并输出

# 运行graph需要先调用tf.Session()函数创建一个会话(session)。
# session就是我们与graph交互的handle
with tf.Session() as sess:
    # 2. 运行init operation
    sess.run(init_op)
    # 计算
    a_out = sess.run(a)
    print("Variable a is {}".format(a_out))

writer = tf.summary.FileWriter("./log", tf.get_default_graph())
writer.close()
~~~
这个文件跑过一遍后，会产生日志保存在./log下	
tensorBoard的启动命令是    tensorboard --logdir='这里填写log文件夹所在的绝对路径或相对路径'	
然后打开产生的链接即可			
如果你是在google chome里面打开，可能会失败，原因可能是google chome的版本过低，换一个浏览器或者升级chome即可

如果是对于keras模型怎么启动TenorBoard呢？	
在fit函数里面添加一项，callbacks=[TensorBoard(log_dir='./log_dir')]
~~~python
import os, cv2, keras
import tensorflow as tf
import numpy as np
from keras.callbacks import TensorBoard
from keras.models import Sequential
from keras.layers import Dense, Flatten, Dropout
from keras.datasets import mnist

os.environ["TF_CPP_MIN_LOG_LEVEL"]='2'

(x_train, y_train),(x_test, y_test) = mnist.load_data()

x_train, x_test = x_train / 255.0, x_test / 255.0
x_test = np.random.normal(x_test)
y_train = keras.utils.to_categorical(y_train, 10)
y_test = keras.utils.to_categorical(y_test, 10)

print(x_test.shape)
print(y_test.shape)

model = Sequential()
# 用add更加形象展示构造network的过程
model.add(Flatten())
model.add(Dense(100, activation='relu'))
model.add(Dropout(0.7))
model.add(Dense(80, activation='relu'))
model.add(Dropout(0.7))
model.add(Dense(40, activation='relu'))
model.add(Dropout(0.7))
model.add(Dense(10, activation = 'softmax'))

model.compile(optimizer="adam",
              loss = 'categorical_crossentropy',
              metrics = ['accuracy'])

model.fit(x_train,y_train,batch_size=100,epochs=5,validation_split=0.2,callbacks=[TensorBoard(log_dir='./log_dir')])

result2 = model.evaluate(x_test, y_test, batch_size = 100)
print('Test Acc: ', result2[1])
~~~
