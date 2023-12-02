---
title: 有效三角形个数
categories:
  - Leetcode
tags:
  - 算法题-双指针
cover: >-
  https://memoo-blog-1314753088.cos.ap-beijing.myqcloud.com/Blogpost%2FpostImage-22.webp
description: "\U0001F4C8统计给定数组中构成三角形的三元组数量。"
mathjax: true
abbrlink: 2a1c7787
date: 2023-11-28 00:00:00
updated: 2023-11-28 00:00:00
swiper_index:
---
# [ 有效三角形的个数](https://leetcode.cn/problems/valid-triangle-number/)

## 题目描述

给定一个包含非负整数的数组 `nums` ，返回其中可以组成三角形三条边的三元组个数。

**示例 1:**

```
输入: nums = [2,2,3,4]
输出: 3
解释:有效的组合是: 
2,3,4 (使用第一个 2)
2,3,4 (使用第二个 2)
2,2,3
```

**示例 2:**

```
输入: nums = [4,2,3,4]
输出: 4
```

**提示:**

- `1 <= nums.length <= 1000`
- `0 <= nums[i] <= 1000`

## 题解一（暴力求解）

> ps：此种解法会导致超时。

**三层 for 循环枚举出所有的三元组，并且判断是否能构成三⻆形。**

判断三⻆形的**优化**：

 ▪ 如果能构成三⻆形，需要满⾜任意两边之和要⼤于第三边。**但是实际上只需让较⼩的两条边之和⼤于第三边即可。**

 ▪ 因此我们可以先将原数组**排序**，然后从⼩到⼤枚举三元组，⼀⽅⾯省去枚举的数量，另⼀⽅⾯⽅便判断是否能构成三⻆形。

### C++版本

```cpp
class Solution {
public:
    int triangleNumber(vector<int>& nums) {
        // 1. 排序
        sort(nums.begin(), nums.end());
        int n = nums.size(), ret = 0;
        
        // 2. 从⼩到⼤枚举所有的三元组
        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                for (int k = j + 1; k < n; k++) {
                    // 当最⼩的两个边之和⼤于第三边的时候，统计答案
                    if (nums[i] + nums[j] > nums[k])
                        ret++;
                }
            }
        }
        
        return ret;
    }
};
```

### C版本

```c
int compare(const void *a, const void *b) {
    return (*(int*)a - *(int*)b);
}

int triangleNumber(int* nums, int numsSize) {
    // 1. 排序
    qsort(nums, numsSize, sizeof(int), compare);
    int ret = 0;

    // 2. 从小到大枚举所有的三元组
    for (int i = 0; i < numsSize; i++) {
        for (int j = i + 1; j < numsSize; j++) {
            for (int k = j + 1; k < numsSize; k++) {
                // 当最小的两个边之和大于第三边的时候，统计答案
                if (nums[i] + nums[j] > nums[k])
                    ret++;
            }
        }
    }

    return ret;
}
```

## 题解二（排序 + 双指针）

### 算法思路

先将数组排序。

根据「解法⼀」中的优化思想，我们可以固定⼀个「最⻓边」，然后在⽐这条边⼩的**有序数组中找出⼀个⼆元组**，使这个⼆元组之和⼤于这个最⻓边。由于数组是有序的，我们可以利⽤「对撞指针」来优化。

设最⻓边枚举到`index`位置，区间 `[left, right]` 是`index`位置左边的区间（也就是⽐它（最长边）⼩的区 间）：

如果 `nums[left] + nums[right] > nums[index]` ：

>  ▪ 说明 `[left, right - 1]` 区间上的**所有**元素均可以与 `nums[right]` 构成⽐ `nums[index]` ⼤的⼆元组 ，因为区间是有序的，`left`向后移动获得的边比此时`left`的边要大，恒满足组成三角形。
>
> ▪ 满⾜条件的有 `right - left` 种 。
>
> ▪ 此时 `right` 位置的元素的所有情况相当于**全部考虑完毕**， `right--` ，进⼊下⼀轮判断 。

![](https://memoo-blog-1314753088.cos.ap-beijing.myqcloud.com/Leetcode%2F611-%E6%9C%89%E6%95%88%E4%B8%89%E8%A7%92%E5%BD%A2%E4%B8%AA%E6%95%B0%2F%E7%A4%BA%E4%BE%8B1.png)

如果 `nums[left] + nums[right] <= nums[index]` ：

>  ▪ 说明 `left` 位置的元素是不可能与 `[left + 1, right]` 位置上的元素构成满⾜条件的⼆元组此时
>
>  ▪ `left` 位置的元素可以舍去，`left++` 进⼊下轮循环，因为`left`向后移动可能获取更大的边，可能得到满足条件的二元组。

![](https://memoo-blog-1314753088.cos.ap-beijing.myqcloud.com/Leetcode%2F611-%E6%9C%89%E6%95%88%E4%B8%89%E8%A7%92%E5%BD%A2%E4%B8%AA%E6%95%B0%2F%E7%A4%BA%E4%BE%8B2.png)

### C++版本

```cpp
class Solution {
public:
    int triangleNumber(vector<int>& nums) {
        // 1. 优化：对输入数组进行排序
        sort(nums.begin(), nums.end());
        
        // 2. 利用双指针解决问题
        int count = 0; // 记录符合条件的三元组个数
        int n = nums.size();
        
        // 3. 从数组末尾开始，固定最大的数
        for (int index = n - 1; index >= 2; i--) {
            // 4. 使用双指针从数组开头和当前最大数的前一个位置开始向中间移动
            int left = 0, right = index - 1;
            
            // 5. 在双指针移动的过程中，判断是否满足三角形的条件
            while (left < right) {
                // 如果两较小边之和大于当前最大边，则说明符合条件
                if (nums[left] + nums[right] > nums[i]) {
                    // 6. 在此情况下，右指针左移，统计符合条件的个数
                    count += right - left;
                    right--;
                } else {
                    // 7. 如果不满足条件，左指针右移
                    left++;
                }
            }
        }
        // 8. 返回符合条件的三元组个数
        return count;
    }
};
```

