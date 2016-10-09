---
layout: post
title: 为C#枚举添加描述信息
categories: [blog ]
tags: [C#, ]
description: 为C#枚举添加描述信息
---

在C#中使用枚举的时候，经常会为某个Enum类型添加相应的描述信息，例如：

```c#
public enum DemoEnum
{
    Enum1,
    Enum2,
    Enum3
};

public void Foo()
{
    DemoEnum e = GetEnum();
    if (e == DemoEnum.Enum1)
    {
        Console.WriteLine("Enum1");
    }
    else if (e == DemoEnum.Enum2)
    {
        Console.WriteLine("Enum2");
    }
    else if (e == DemoEnum.Enum3)
    {
        Console.WriteLine("Enum3");
    }
}
```

函数```Foo```中针对每一个枚举项都输出了特定的字符串，使用```if```或是```switch```都会显得很繁琐。下面是使用**Attribute**和**扩展方法**实现的方法。

## 为C# Enum添加Description

```c#
public enum DemoEnum
{
    [Description("枚举1")]
    Enum1,
    [Description("枚举2")]
    Enum2,
    [Description("枚举3")]
    Enum3
};

public static class Extension
{
    public static string ToDescription(this DemoEnum myEnum)
    {
        var type = typeof(DemoEnum);
        var info = type.GetField(myEnum.ToString());
        var attrs = info.GetCustomAttributes(typeof(DescriptionAttribute), true);
        var defaultRes = type.ToString();
        if (attrs.Length == 0)
        {
            return defaultRes;
        }
        var descriptionAttribute = attrs[0] as DescriptionAttribute;
        return
            descriptionAttribute != null ? descriptionAttribute.Description : defaultRes;

    } 
}
```
那么最开始的```Foo```函数就可以变成

```c#
public void Foo()
{
    DemoEnum e = GetEnum();
    Console.WriteLine(e.ToDescription());
}
```

## 背后的原理
其实现由**Attribute**和**扩展方法**共同组成。
### Attribute
Attribute即为特性。所有的特性及自定义特性都继承自```Attribute```基类。以下信息摘自[MSDN](https://msdn.microsoft.com/zh-cn/library/system.attribute(v=vs.100).aspx)：
> Attribute 类将预定义的系统信息或用户定义的自定义信息与目标元素相关联。 目标元素可以是程序集、类、构造函数、委托、枚举、事件、字段、接口、方法、可移植可执行文件模块、参数、属性、返回值、结构或其他特性。  
>   
> 特性所提供的信息也称为元数据。 元数据可由应用程序在运行时进行检查以控制程序处理数据的方式，也可以由外部工具在运行前检查以控制应用程序处理或维护自身的方式。 例如，.NET Framework 预定义特性类型并使用特性类型控制运行时行为，某些编程语言使用特性类型表示 .NET Framework 常规类型系统不直接支持的语言功能。  
>   
> 所有特性类型都直接或间接地从 Attribute 类派生。 特性可应用于任何目标元素；多个特性可应用于同一目标元素；并且特性可由从目标元素派生的元素继承。 使用 AttributeTargets 类可以指定特性所应用到的目标元素。

简单来说表达了4条信息：
1. 所有特性都派生自```Attribute```基类
1. 特性可以修饰C#中任何元素（程序集、类、构造函数、委托、枚举、事件、字段、接口、方法、可移植可执行文件模块、参数、属性、返回值、结构或其他特性）
1. 特性可以存放数据（即元数据，可以在程序运行时进行读取和写入）
1. 同一元素可以有多个特性

上面程序使用的Description特性是C#內建的特性（```System.ComponentModel.DescriptionAttribute```）

以下是我添加了注释的原型代码

```c#
// AttributeUsage说明该特性可以应用的范围
// 此处为：可以应用到所有元素
[AttributeUsage(AttributeTargets.All)]
public class DescriptionAttribute : Attribute
{
    // 定义默认的静态属性
    public static readonly DescriptionAttribute Default = new DescriptionAttribute();

    // 存放描述信息的私有属性
    private string description;
    
    public DescriptionAttribute() : this(string.Empty)
    {
    }
    
    [TargetedPatchingOptOut("Performance critical to inline this type of method across NGen image boundaries")]
    public DescriptionAttribute(string description)
    {
        this.description = description;
    }
    
    // 重写object的Equals方法
    public override bool Equals(object obj)
    {
        // 同一变量或描述相同则返回true
        // 否则false
        if (obj == this)
        {
            return true;
        }
        DescriptionAttribute attribute = obj as DescriptionAttribute;
        return ((attribute != null) && (attribute.Description == this.Description));
    }
    
    // 重写GetHashCode方法
    public override int GetHashCode()
    {
        return this.Description.GetHashCode();
    }
    
    public override bool IsDefaultAttribute()
    {
        return this.Equals(Default);
    }
    
    // 返回其描述信息
    public virtual string Description
    {
        [TargetedPatchingOptOut("Performance critical to inline this type of method across NGen image boundaries")]
        get
        {
            return this.DescriptionValue;
        }
    }
    
    // 返回其描述信息
    protected string DescriptionValue
    {
        [TargetedPatchingOptOut("Performance critical to inline this type of method across NGen image boundaries")]
        get
        {
            return this.description;
        }
        [TargetedPatchingOptOut("Performance critical to inline this type of method across NGen image boundaries")]
        set
        {
            this.description = value;
        }
    }
}
```

### 扩展方法
扩展方法能够向现有类中增加新的方法，而不需要继承自该类。[扩展方法MSDN](https://msdn.microsoft.com/library/bb383977.aspx)

> LINQ是最常见的扩展方法

扩展方法有4个特点：
1. 扩展方法是**静态**的
1. 第一个参数指定要扩展的类
1. 第一个参数以```this```修饰符为前缀
1. 在非嵌套的、非泛型的**静态**类内定义

详见开头部分的代码
