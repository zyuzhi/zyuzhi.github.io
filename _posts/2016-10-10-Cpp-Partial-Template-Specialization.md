---
layout: post
title: C++特化和偏特化
categories: [blog ]
tags: [C++, ]
description: C++特化和偏特化
---

最新在看《STL源码剖析》，一直在使用C++、STL和BOOST，但却对其背后的原理和模板特性知之甚少，就从STL源码开始吧。

书中在`iterator`这一节提到了**全特化**和**偏特化**。**全特化**和**偏特化**是**特化(Template Specialization)**的子集。



1. 模板(Template)

   ```cpp
   // 模板
   template<typename T, typename U>
   class A
   {};
   ```
   模板类`A`有两个模板参数`T`和`U`。
1. 全特化(Full Template Specialization)

   ```cpp
   // 全特化
   template<>
   class A<char, int>
   {};
   ```
   对模板类`A`进行了全特化，指定了所有模板参数`T=char`和`U=int`。
1. 偏特化(Partial Template Specialization)

   偏特化有两种不尽相同的特化形式，“对部分模板参数进行特化”和“对模板参数进行限定”的两种偏特化形式。

   1. 对部分模板参数进行特化

      ```cpp
      // 偏特化(只特化部分模板参数)
      template<typename T>
      class A<T, char>
      {};

      // 偏特化(只特化部分模板参数)
      template<typename U>
      class A<int, U>
      {};
      ```
      只对部分模板参数进行特化，与特化位置和特化个数无关。
   1. 对模板参数进行限定

      ```cpp
      // 偏特化(只对模板参数做出部分限定)
      // 指针限定
      template<typename T, typename U>
      class A<T *, U>
      {};
      
      // 偏特化(只对模板参数做出部分限定)
      // 引用限定
      template<typename T, typename U>
      class A<T &, U>
      {};
      
      class B{};
      
      // 偏特化(只对模板参数做出部分限定)
      // 成员变量指针限定
      template<typename T, typename U>
      class A<T B::*, U>
      {};
      
      // 偏特化(只对模板参数做出部分限定)
      // 函数指针限定
      template<typename T, typename U>
      class A<void (*)(T), U>
      {};
      
      // 偏特化(只对模板参数做出部分限定)
      // 成员函数指针限定
      template<typename T, typename U>
      class A<void (B::*)(T), U>
      {};
      ```
      限定可以是指针(line 4)、引用(line 10)、成员变量指针(line 18)、函数指针(line 24)和成员函数指针(line 30)限定。表现形式不限于上述代码。
