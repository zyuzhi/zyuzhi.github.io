---
layout: post
title: C#异常：throw，throw ex，throw new Exception(..)
categories: [blog ]
tags: [C#, ]
description: C#异常抛出的方式：throw，throw ex，throw new Exception(..)
---

最近看其他人写的C#代码，异常抛出的方式格式各样。总结了三种方式及其优劣：

1. throw  
代码
```c#
try
{
        do_something();
}
catch
{
        throw;
}
```
这种方式是将原先的异常再次抛出（rethrow），且不会重置异常的堆栈信息（在原先的堆栈信息上增加了当前的异常信息）。是最为推荐的异常抛出方式。  
1. throw new Exception(...)  
代码
```c#
try
{
        do_something();
}
catch (Exception ex)
{
        throw Exception("exception", ex);
}
```
这种异常抛出方式是将原先的异常经过包装后再抛出，将原先的异常放在新异常的内部异常堆栈中（```ex.InnerException```）  
PS: 下面异常抛出方式**不要使用！！**，没有任何异常信息。
```c#
try
{
        do_something();
}
catch (Exception ex)
{
        throw Exception("exception");
}
```
1. throw ex  
代码
```c#
try
{
        do_something();
}
catch (Exception ex)
{
        throw ex;
}
```
这种异常抛出方式重置了堆栈信息，**不要使用！**


## 测试
代码

```c#
using System;
using System.Collections.Generic;
using System.Diagnostics;
using System.Linq;
using System.Text;
using System.Threading;

namespace ExceptionDemo
{
    class Program
    {
        static void ThrowException1()
        {
            try
            {
                Console.WriteLine("throw");
                ProduceException();
            }
            catch (Exception)
            {
                throw;
            }
        }

        static void ThrowException2()
        {
            try
            {
                Console.WriteLine("throw new Exception(\"ProduceException\", ex)");
                ProduceException();
            }
            catch (Exception ex)
            {
                throw new Exception("ProduceException", ex);
            }
        }

        static void ThrowException3()
        {
            try
            {
                Console.WriteLine("throw new Exception(\"ProduceException\")");
                ProduceException();
            }
            catch (Exception ex)
            {
                throw new Exception("ProduceException");
            }
        }

        static void ThrowException4()
        {
            try
            {
                Console.WriteLine("throw ex");
                ProduceException();
            }
            catch (Exception ex)
            {
                throw ex;
            }
        }

        static int ProduceException()
        {
            int a = 0;
            int b = 1;
            return b/a;
        }
        static void Main(string[] args)
        {
            var funcList = new List<Action>
            {
                ThrowException1,
                ThrowException2,
                ThrowException3,
                ThrowException4,
            };
            foreach (var action in funcList)
            {
                try
                {
                    action();
                }
                catch (Exception ex)
                {
                    Console.WriteLine(ex);
                }
                Console.WriteLine();
                Console.WriteLine();
            }
        }
    }
}
```

结果

```
throw
System.DivideByZeroException: 尝试除以零。
   在 ExceptionDemo.Program.ProduceException() 位置 E:\ExceptionDemo\Program.cs:行号 68
   在 ExceptionDemo.Program.ThrowException1() 位置 E:\ExceptionDemo\Program.cs:行号 21
   在 ExceptionDemo.Program.Main(String[] args) 位置 E:\ExceptionDemo\Program.cs:行号 83


throw new Exception("ProduceException", ex)
System.Exception: ProduceException ---> System.DivideByZeroException: 尝试除以零。
   在 ExceptionDemo.Program.ProduceException() 位置 E:\ExceptionDemo\Program.cs:行号 68
   在 ExceptionDemo.Program.ThrowException2() 位置 E:\ExceptionDemo\Program.cs:行号 30
   --- 内部异常堆栈跟踪的结尾 ---
   在 ExceptionDemo.Program.ThrowException2() 位置 E:\ExceptionDemo\Program.cs:行号 34
   在 ExceptionDemo.Program.Main(String[] args) 位置 E:\ExceptionDemo\Program.cs:行号 83


throw new Exception("ProduceException")
System.Exception: ProduceException
   在 ExceptionDemo.Program.ThrowException3() 位置 E:\ExceptionDemo\Program.cs:行号 47
   在 ExceptionDemo.Program.Main(String[] args) 位置 E:\ExceptionDemo\Program.cs:行号 83


throw ex
System.DivideByZeroException: 尝试除以零。
   在 ExceptionDemo.Program.ThrowException4() 位置 E:\ExceptionDemo\Program.cs:行号 60
   在 ExceptionDemo.Program.Main(String[] args) 位置 E:\ExceptionDemo\Program.cs:行号 83
```

## 结论
1. ```throw;``` good
1. ```throw new Exception("msg", ex);``` good
1. ```throw new Exception("msg");``` bad
1. ```throw ex;``` bad
