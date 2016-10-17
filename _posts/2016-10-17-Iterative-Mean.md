---
layout: post
title: 迭代求数列平均值
categories: [blog ]
tags: [数学, ]
description: 迭代求取数列平均值
---

有数列\$\\{x_i\\}\$，数列前\$ t\$个的均值为\$M_t\$，则
\$$M_t=\frac{\sum_{i=1}^{t}x_i}{t}\$$
\$M_t\$还有一种迭代的求取方法，
\$$M_t=\frac{t-1}{t}M_{t-1}+\frac{1}{t}x_t\$$

## 推导过程

有
\$$M_t={(\sum_{i=1}^{t}x_i)}/{t}\$$
将\$\sum_{i=1}^{t}x_i\$拆成\$\sum_{i=1}^{t-1}x_i\$和\$x_t\$的和，即
\$$\begin{aligned} &M_t &= & (\sum_{i=1}^{t}x_i)/{t} \\\\ \\\\ & &=& (\sum_{i=1}^{t-1}x_i+x_t)/t \\\\ \\\\ & &=& \frac{1}{t}\sum_{i=1}^{t-1}x_i+\frac{1}{t}x_t\end{aligned}\$$
由\$M_{t-1}=(\sum_{i=1}^{t-1}x_i)/(t-1)\$可得\$\sum_{i=1}^{t-1}x_i=M_{t-1}(t-1)\$，带入上式，
\$$\begin{aligned} &M_t &= & \frac{1}{t}\sum_{i=1}^{t-1}x_i+\frac{1}{t}x_t \\\\ \\\\ & &=& \frac{t-1}{t}M_{t-1}+\frac{1}{t}x_t\end{aligned}\$$

## 代码实现
迭代函数

```cpp
void iterative_mean(float& average, const float num, const int index)
{
    float index_1 = static_cast<float>(index + 1);
    average = average * static_cast<float>(index) / index_1 + num / index_1;
}
```
示例

```cpp
void iterative_mean(float& average, const float num, const int index)
{
    float index_1 = static_cast<float>(index + 1);
    average = average * static_cast<float>(index) / index_1 + num / index_1;
}

int main()
{
    float a[10] = {0};
    const int count = 10;
    for (int i = 0; i < count; ++i)
    {
        a[i] = i+1;
        std::cout << a[i] << " ";
    }
    std::cout << std::endl;
    float iter_mean = 0;
    float acc_mean = 0;
    for (int i = 0; i < count; ++i)
    {
        // 迭代求平均值
        iterative_mean(iter_mean, a[i], i);

        // 累加
        acc_mean += a[i];
    }
    // 累加计算平均值
    acc_mean /= count;
    std::cout << "accumulative mean: " << acc_mean << std::endl;
    std::cout << "iterative mean: " << iter_mean << std::endl;
    return 0;
}
```
输出：

```
1 2 3 4 5 6 7 8 9 10
accumulative mean: 5.5
iterative mean: 5.5
```
