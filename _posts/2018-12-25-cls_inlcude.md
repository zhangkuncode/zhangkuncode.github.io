---
layout: post
tags: c++
title: 类模版的分离式写法
---

cls.h
~~~cpp
#pragma once

template<typename T>
class Cls{
private:
	T m;
public:
	Cls(T a);
	void show();
};

#include "cls.cpp"
~~~

cls.cpp
~~~cpp
#include <iostream>
using namespace std;

template<typename T>
Cls<T>::Cls(T a){
	m = a;
}

template<typename T>
void Cls<T>::show(){
	cout << m << endl;
}
~~~

main.cpp
~~~cpp
#include "cls.h"

int main(){
	CLS<int> a(10);
	a.show();

	return 0;
}
~~~

与传统普通类的分离式写法不同在于：
实现文件(cls..cpp)里面不包含声明文件(cls.h), 但是在声明文件的末尾包含实现文件	
并且编译的时候，只编译main.cpp

上面所说的就是逆包含，在STL的实现中，采用的就是这种策略
