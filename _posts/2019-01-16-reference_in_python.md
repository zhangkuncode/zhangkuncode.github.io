---
layout: post
tags: python
title: python中的变量引用
---

~~~python
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
~~~
2185035158112		
2185035158112		
[  1 100   3]		
[  1 100   3]		
~~~

但是我在使用np.array.reshape()却遇到了问题
~~~python
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
output is:		
~~~
[[1 2 3 1 2 3 1 2 3]]
[[1 2 3]
 [1 2 3]
 [1 2 3]]
[[100   2   3   1   2   3   1   2   3]]
[[100   2   3]
 [  1   2   3]
 [  1   2   3]]
1609965671088
1609965670928
~~~

aa 与 bb 的id不一样，为什么bb的值改变，aa的值也会改变呢？

后面又发现，打印aa[0] bb[0]的id是一样的。



