---
layout: post
tags: python
title: python中的变量引用
---

~~~
import numpy as np

a = np.array([1,2,3])
b = a
print(id(a))
print(id(b))
b[1] = 100
print(b)
print(a)
~~~
output :	
2185035158112	
2185035158112	
[  1 100   3]	
[  1 100   3]	

但是我在使用np.reszie()却遇到了问题
~~~
import numpy as np

aa = np.array([[1,2,3],[1,2,3],[1,2,3]])
bb = aa.reshape(1,9)
print(bb)
print(aa)
bb[0][0] = 100
print(bb)
print(aa)
print(id(bb))
print(id(aa))
~~~


