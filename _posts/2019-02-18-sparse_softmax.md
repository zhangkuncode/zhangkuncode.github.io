---
layout: post
tags: tensorflow
title: sparse_softmax_cross_entropy_with_logits()函数
---

这个logits什么意思呢？就是W*x + b的值，不必做scalar

sparse_softmax_cross_entropy_with_logits函数：
​    使用的是实数来表示类别，数据类型为int16，int32，或者 int64，
​    标签大小范围为[0，num_classes-1]，
​    标签的维度为[batch_size]大小。
​    
这个函数比softmax_cross_entropy_with_logits()的优势和区别是在：	
​	[1] 对于样本的label,不必自己转成1-N encoding形式，然后利用这个形式调cross_entropy函数。这样做会导致输入的维度可能很大。样本label直接是一个数
​	[2] 同时有看到有人这样说
​	     it is more efficient:[it properly takes care of corner cases, like logits equal to 0]
​	     这种说法暂时还没理解，可能对交叉熵理解还不够，之后明白了再补充

下面这个代码运行结果，loss1 = loss2，是不是说明sparse版本的这个函数在内部，把y转成了[[0,0,1,0]]，再调用softmax版那个函数？ 我暂时理解是这样的
~~~python
import tensorflow as tf
import os

os.environ['TF_CPP_MIN_LOG_LEVEL'] = '3'

y1 = tf.constant([[0,0,1,0]], dtype=tf.float32, name='y1')
y_1 = tf.constant([[4.6, 1.7, 3.2, 0.1]], dtype=tf.float32, name='y_1')

#返回张量轴上具有最大值的索引
#即是返回从0开始的类别标签
y = tf.argmax(y1, 1) # [2]

loss1 = tf.nn.softmax_cross_entropy_with_logits(labels=y1, logits=y_1)
loss2 = tf.nn.sparse_softmax_cross_entropy_with_logits(labels=y, logits=y_1)

with tf.Session() as sess:
    print("loss1: ", sess.run(loss1))
    print("loss2: ", sess.run(loss1))
~~~




