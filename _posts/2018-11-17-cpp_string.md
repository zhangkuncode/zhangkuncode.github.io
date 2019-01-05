---
layout: post
tags: c++
title: C++实现String
---

听老师说，这个在一些公司面试关于基础cpp知识时经常会被问到。

如果是刚开始学习cpp，实现String的时候可能会遇到一些问题

1. 对于char内存管理，需要了解下面几个函数:
1.1.strlen()  
	​	size_t strlen( const char *str );  
	​	要求str是以null结尾的字符串,if not, that is undefined behavior!  
	​	return str这个字符串的长度，不包括最后一个null字节  
	​	(strlen与sizeof()的区别： 计算的是相应数据类型所占的空间大小，如何传入的基本类型，这个大小由编译器本身决定的。如果传入的是char*,int*,long*,double*这些指针，那么大小就是他们所占的字节数，是4)

1.2.strcpy  
​	​	char *strcpy( char *dest, const char *src );  
​	​	包括src的null结束符复制到dest中。其中一下几种情况,会导致 undefined behavior  
​	​	1.src没有null结束符  
​	​	2.dest与src重叠  
​	​	3.dest数组不够大  
​	​	4.dest不是指向字符数组src的指针 或者 不是指向以null结尾的字符串的指针


2. 关于c语言类型的字符串：
结尾有'\0'，是结束标识符，又是转义字符，对应于ASCII码的00000000, 意义是空字符(NULL)，字符串常量，编译器会自动添加'\0'
~~~c
#include <stdio.h>
int main(){
	char a[6] = "hello";
	printf("%s\n", a);
	a[3] = 0;
	printf("%s", a);
	return 0;
}
~~~
输出是下面2行：
~~~
hello
hel
~~~
为什么第二行只打印了前3个字符？因为以字符串形式打印到第四个字符的时候，发现它是空字符，编译器理解为结束符，停止打印。

3. ASCLL码又怎么理解？
	​	关于编码：计算机内部，所有信息最终都是一个二进制值(0/1), 所以我们的看到的非0/1的字符，最终在计算机怎么表示呢？这就需要一套编码原则，对这些字符进行编码。
	​	世界上存在着多种编码方式，同一个二进制数字可以被解释成不同的符号(根据不同的编码原则)
	​	早期美国制定了ASCII码表，一共规定了128个字符的编码，比如空格SPACE是32（二进制00100000），大写的字母A是65（二进制01000001）。这128个符号（包括32个不能打印出来的控制符号），只占用了一个字节的后面7位，最前面的一位统一规定为0。

4. 关于运算符重载：
	4.1motivation：	
为什么要使用运算符重载？使代码显的清晰：
~~~cpp
Object a;
Object b;
mprint(myadd(a, b));
~~~
使用运算符重载后
~~~cpp
cout << a + b << endl;
~~~

4.2 使用原则
4.2.1 运算符函数，至少含有一个类类型参数
4.2.2 无权发明新的元算符号（operator* *， 来执行幂操作是不允许的）


4.3 运算符函数是作为成员函数还是非成员函数呢？

4.3.1 输入输出函数必须是非成员函数，因为它的左侧运算对象是输入输出流，而不是类类型对象；第二种说法, 如果把它定义成了成员函数，使用的时候就要这样使用
~~~cpp
MyClass object;
object << cout;
// equal to: 
object.operator<<(cout); // 调用MyClass类的成员输出函数
~~~
但是这样就不符合日常使用习惯，跟重载运算符的初衷违背了。第三种解决方法：在标准库里面修改ostream类，添加一个传入类型是Myclass的成员输出函数，但是我们有不能修改标准库，所以这种解决方法行不通。综上，只能选择第一种方法


MyString实现源码
~~~cpp
#include <iostream>
#include <cstring>

using namespace std;

// statement 
class MyString {
public:
	MyString(const char *str = NULL);
	MyString(const MyString &other);
	~MyString(void);

	size_t size();
	MyString& operator=(const MyString &other);
	bool operator==(const MyString& other)const;
	MyString operator+(const MyString& other);
	friend ostream& operator<<(ostream& os, const MyString& other);
private:
	char * m_data;
};

// definition
// 赋值操作符
MyString& MyString::operator=(const MyString &other) {
	// 关键要将原来m_data置空后重新分配内存
	if (nullptr != m_data) {
		delete[] m_data;
		m_data = nullptr;
	}

	m_data = new char[strlen(other.m_data) + 1];
	strcpy(m_data, other.m_data);
	return *this;
}

bool MyString::operator==(const MyString& other)const {
	if (0 == strcmp(m_data, other.m_data)) {
		return true;
	} else {
		return false;
	}
}

size_t MyString::size() {
	// 不包括null结尾的空字符
	return strlen(m_data); 
}

ostream& operator<<(ostream& os, const MyString& other) {
	return os << other.m_data;
}

// 不能是MyString& 返回的是临时对象函数结束时被析构，引用不到
MyString MyString::operator+(const MyString& other) {
	if(nullptr == other.m_data){
		cout << "opertor+() parameter: other.m_data is nullptr" << endl;
		return *this;
	}

	/*
		eg:
		this->m_data is "hello"
		`	in fact is {'h','e','l','l',o','\0'}
		other.m_data is "world"
			in fact is {'w','o','r','l','d','\0'}
	*/
	
	/* we hope the result is
       {'h','e','l','l','o','w','o','r','l','d','\n'}
    */
	char *s = new char[strlen(m_data) + strlen(other.m_data) + 1];
	// 申请内可能失败，由操作系统决定
	if (nullptr == s) {
		cout << "memory allocate failed!" << endl;
	}
	
	strcpy(s, m_data);
	/* not strcpy(s+strlen(m_data) + 1, other.m_data);
	   as for the this->m_data's '\0', we ignore it，noncopying it
	*/
	strcpy(s+strlen(m_data), other.m_data);
	return MyString(s);
}

// construct function
// 定义不能再重复加缺省值
MyString::MyString(const char *str) {
	if(m_data != nullptr){
		delete[] m_data;
		m_data = nullptr;
	}
	m_data = new char[strlen(str) + 1];
	if (nullptr == m_data) {
		cout << "memory allocate failed!" << endl;
	}
	strcpy(m_data, str);
}

// copy function
MyString::MyString(const MyString &other) {
	if (nullptr != m_data) {
		delete[] m_data;
		m_data = nullptr;
	}
	m_data = new char[strlen(other.m_data) + 1];
	if (nullptr == m_data) {
		cout << "memery allocate failed" << endl;
	}
	strcpy(m_data, other.m_data);
}

// delete function
MyString::~MyString(void) {
	if (m_data != nullptr) {
		delete[] m_data;
		m_data = nullptr;
	}
}

int main(){
	MyString s1("hello");
	cout << "--------test operator<<() , Expectation is <hello> " << endl;
	cout << s1 << endl;

	MyString s2(" world");
	cout << "--------test operator+() , Expectation is <hello world> " << endl;
	MyString s3 = s1 + s2;
	cout << s3 << endl;

	cout << "--------test operator==() , Expectation is <equal> " << endl;
	MyString s4("hello");
	if (s4 == s1) {
		cout << "equal" << endl;
	} else {
		cout << "inequal" << endl;
	}
		
	cout << "--------test operator=() , Expectation is < world> " << endl;
	MyString s5 = s2;
	cout << s5 << endl;

	cout << "--------test size() , Expectation is <6> " << endl;
	MyString s6 = s2;
	cout << s6 << endl;
	cout << s6.size() << endl;
}
~~~

reference：
1. http://www.ruanyifeng.com/blog/2007/10/ascii_unicode_and_utf-8.html
2. http://www.voidcn.com/article/p-kebfwave-go.html

