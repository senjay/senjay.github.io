---
title: Template类模板
date:  2020-05-21 21:03:27
categories:
- C++
tags:
- C++
---
# Template

## 1.函数模板

编译期间编译器自动分析参数类型，`template `函数模板是支持默认参数的，`T1 、T2` 顺序在默认情况下是可以任意的，不用严格按照从右到左的顺序，例如：`template <typename T1 ,typename T2 = int>`

```cpp
//函数模板化    
template<typename T>
T add(const T lval,const T rval)
{
    T ans = lval + rval;
    return ans; 
}
```



```cpp
cout << add(1, 2) << endl;
cout << add(2.1, 1.2) << endl;//add(2, 1.2)会报错,因为变成了add(int,double)
输出：
    3
    3.3
```



## 2.类模板

将模板思想应用于类,类模板也支持默认参数，但是类模板必须严格从右往左默认化。

```cpp
//类模板
template<class T>
class Myclass
{
    T a;
    public:
        T add(const T lval, const T rval);
};
template <class T>
T Myclass <T>::add(const T lval, const T rval)
{
    a = lval + rval;
    return a;
}
```



```cpp
Myclass<double> myclass;
cout << myclass.add(1.2, 3.3) << endl;
输出：
    4.5
```



## 3.成员模板

在模板类内使用模板函数。

```cpp
//成员模板
template<class T>
class Mylove
{
    public:
        T a;
        template <typename type_1, typename type_2>
        type_1 add(const type_1 lval, const type_2 rval);
};
template <class T>
template <typename type_1, typename type_2>
type_1 Mylove<T>::add(const type_1 lval, const type_2 rval)
{
    a = lval + rval;
    return a;
}
```

```cpp
Mylove<double> mylove;
cout << mylove.add(0, 20.2) << endl; //答案为20，因为该函数返回为第一个参数相同,该过程为int+double=double,但最终返回int
输出:
	20
```



## 4.嵌套类模板

模板类内嵌套模板类。

```cpp
//嵌套类模板
template<class T>
class Mylife
{
    public:
        T a;
        template <class type_3>
        class Mystory;//先声明，在外部定义
        Mystory<T> mystory;//用T实例化
};
template<class T>
    template<class type_3>
        class Mylife<T>::Mystory
        {
            public:
                type_3 c;
                type_3 add(const type_3 lval, const type_3 rval) 
                {
                    c = lval + rval;
                    return c;
                };
        };
```

```cpp
Mylife<double> mylife;
cout << mylife.mystory.add(1, 1.1) << endl;
输出：
    2.1
```



## 5.总结

使用`template`,以不变应万变，平常使用的STL正是基于此。