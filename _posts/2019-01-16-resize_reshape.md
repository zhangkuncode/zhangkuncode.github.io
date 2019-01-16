---
layout: post
tags: python
title: numpy中的resize与reshape函数
---

numpy.reshape函数			
作用：Gives a new shape to an array without changing its data.	
​	 注意，不能改变data,减少或增加都会报错，报错信息如下
~~~
ValueError: cannot reshape array of size XXX into shape (x,x)
~~~
返回值：This will be a new view object if possible; otherwise, it will be a copy. 	
​	   大多数情况下都是副本，但是他们的id却不一样，目前还不知道怎么解释
使用方法如下：
~~~python
import numpy as np

a = np.array([[1,2,3,4],[1,2,3,4]])
#　方法一：
b = a.reshape(4, 2)

#　方法二：
c = np.reshape(a, (1,8))
~~~


ndarray.resize(new_shape, refcheck=True)	
作用：Change shape and size of array in-place.		
​	注意，默认会检查对象是否与其他变量共享内存，if yes, ValueError	
​	报错信息如下：
~~~
ValueError: cannot resize an array that references or is referenced
by another array in this way.  Use the resize function
~~~
返回值：none	
使用方法如下：
~~~python
# 以下新定义的变量b不与a共享内存，与reshape不同！！！
>>> import numpy as np
>>> a = np.array([[1,2,3,4],[1,2,3,4]])
>>> a.resize(1,2)
>>> a
array([[1, 2]])
~~~

resize还有其他函数形式，np.resize	
这种方法，即使a与其他变量共享内存，也没关系	
同样，新定义的变量b不与a共享内存，与reshape不同
~~~python 
>>> a = np.array([[1,2,3,4],[1,2,3,4]])
>>> b = np.resize(a, (1,2))
>>> b
array([[1, 2]])
~~~








