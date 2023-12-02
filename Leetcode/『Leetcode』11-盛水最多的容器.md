---
title: 盛水最多的容器
categories:
  - Leetcode
tags:
  - 算法题-双指针
cover: >-
  https://memoo-blog-1314753088.cos.ap-beijing.myqcloud.com/Blogpost%2FpostImage-19.webp
description: "\U0001FAA3 x 轴构成的容器能容纳最多水。"
mathjax: true
abbrlink: fae52317
date: 2023-11-28 00:00:00
updated: 2023-11-28 00:00:00
swiper_index:
---
# [ 盛最多水的容器](https://leetcode.cn/problems/container-with-most-water/)

## 题目描述

给定一个长度为 `n` 的整数数组 `height` 。有 `n` 条垂线，第 `i` 条线的两个端点是 `(i, 0)` 和 `(i, height[i])` 。

找出其中的两条线，使得它们与 `x` 轴共同构成的容器可以容纳最多的水。

返回容器可以储存的最大水量。

说明：你不能倾斜容器。

**示例 1：**

![image-20231128130801113](https://memoo-blog-1314753088.cos.ap-beijing.myqcloud.com/Leetcode%2F11-%E7%9B%9B%E6%B0%B4%E6%9C%80%E5%A4%9A%E7%9A%84%E5%AE%B9%E5%99%A8%2F%E7%A4%BA%E4%BE%8B.png)

```
输入：[1,8,6,2,5,4,8,3,7]
输出：49 
解释：图中垂直线代表输入数组 [1,8,6,2,5,4,8,3,7]。在此情况下，容器能够容纳水（表示为蓝色部分）的最大值为 49。
```

**示例 2：**

```
输入：height = [1,1]
输出：1
```

**提示：**

- `n == height.length`
- `2 <= n <= 10^5`
- `0 <= height[i] <= 10^4`

## 题解一（暴力求解）

**ps：此题解会超时**

### **算法思路：**

> 枚举出能构成的所有容器，找出其中容积最⼤的值。

- 容器容积的计算⽅式： 

> 设两指针 `i` , `j` ，分别指向⽔槽板的最左端以及最右端，此时容器的宽度为 `j - i` 。由于容器的⾼度由两板中的**短板决定**，因此可得容积公式 ： `v = (j - i) * min( height[i], height[j]) ）`

### **C++版本**

```cpp
class Solution {
public:
    int maxArea(vector<int>& height) {
        int n = height.size();
        int ret = 0;

        // 两层 for 枚举出所有可能出现的情况
        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                // 计算容积，找出最⼤的那⼀个
                ret = max(ret, min(height[i], height[j]) * (j - i));
            }
        }

        return ret;
    }
};
```

## 题解二（对撞指针）

### **算法思路：**

设两个指针`left`，`right`分别指向容器的左右两个端点，此时容器的容积 : `v = (right - left) * min( height[right], height[left])` 

容器的左边界为 `height[left]` ，右边界为 `height[right]` 。

**为了⽅便叙述，我们假设「左边边界」⼩于「右边边界」。**

> ps: 实际情况中两种情况都有可能，甚至可能是相等，如果是相反的情况，直接进行调换即可，若相等，两种做法均可。

如果此时我们固定⼀个边界，改变另⼀个边界，⽔的容积会有如下变化形式：

- 容器的**宽度⼀定变⼩**。 
- 由于左边界较⼩，短板决定了⽔的⾼度。如果改变左边界，容器的宽度减小，但是短板的变化不确定，甚至如果超过右边的水柱高度，因此容器的容积可能会增⼤，可能会减小，**情况并不确定**。
-  如果改变右边界，⽆论右边界移动到哪⾥，新的⽔⾯的⾼度⼀定不会超过左边界，也就是不会超过现在的⽔⾯⾼度，因为现在的水面高度是短板，移动之后的最好情况是和现在水面相平，但是**由于容器的宽度减⼩**，因此容器的容积**⼀定会变⼩**的。 

> 由此可⻅，左边界和其余边界的组合情况都可以舍去，因为**此时只有移动左边界才有可能找到更大的容积**。所以我们可以 left++ 跳过这个边界，继续去判断下⼀个左右边界。

### **C++版本**

```cpp
class Solution {
public:
    int maxArea(vector<int>& height) {
        // 初始化左右指针和最大面积
        int left = 0, right = height.size() - 1, ret = 0;

        // 循环直到左右指针相遇
        while (left < right) {
            // 计算当前两个指针所指向的线段构成的容器的水量
            int v = min(height[left], height[right]) * (right - left);
            // 更新最大水量
            ret = max(ret, v);

            // 移动指针
            // 选择较短的线段移动，以期望找到更高的线段
            if (height[left] < height[right]) {
                left++;
            } else {
                right--;
            }
        }

        // 返回最大水量
        return ret;
    }
};
```

### python版本

```python
from typing import List

class Solution:
    def maxArea(self, height: List[int]) -> int:
        # 初始化左右指针和最大面积
        left, right, ret = 0, len(height) - 1, 0

        # 循环直到左右指针相遇
        while left < right:
            # 计算当前两个指针所指向的线段构成的容器的水量
            v = min(height[left], height[right]) * (right - left)
            # 更新最大水量
            ret = max(ret, v)

            # 移动指针
            # 选择较短的线段移动，以期望找到更高的线段
            if height[left] < height[right]:
                left += 1
            else:
                right -= 1

        # 返回最大水量
        return ret
```

