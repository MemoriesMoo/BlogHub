---
title: 和为target的两个数
categories:
  - Leetcode
tags:
  - 算法题-双指针
cover: >-
  https://memoo-blog-1314753088.cos.ap-beijing.myqcloud.com/Blogpost%2FpostImage-23.webp
description: "\U0001F4A1通过双指针法寻找数组中两数之和等于目标值"
mathjax: true
abbrlink: 172c373f
date: 2023-11-27 00:00:00
updated: 2023-11-27 00:00:00
swiper_index:
---
# [查找总价格为目标值的两个商品](https://leetcode.cn/problems/he-wei-sde-liang-ge-shu-zi-lcof/)

## 题目描述

购物车内的商品价格按照升序记录于数组 `price`。请在购物车中找到两个商品的价格总和刚好是 `target`。若存在多种情况，返回任一结果即可。

**示例 1：**

```css
输入：price = [3, 9, 12, 15], target = 18
输出：[3,15] 或者 [15,3]
```

**示例 2：**

```css
输入：price = [8, 21, 27, 34, 52, 66], target = 61
输出：[27,34] 或者 [34,27]
```

**提示：**

- `1 <= price.length <= 10^5`
- `1 <= price[i] <= 10^6`
- `1 <= target <= 2*10^6`

## 题解一（暴⼒解法）

> **ps：此种算法超时。**

### 算法思路

两层 for 循环列出所有两个数字的组合，判断是否等于⽬标值。

### 算法流程

两层 for 循环： 

**外层 for 循环**：依次枚举第一个数 `nums1`

- **内层 for 循环**：依次枚举第二个数 `nums2`，让它与 `nums1` 匹配符合结果

> ps ：挑选第⼆个数的时候，可以不从第⼀个数开始选，因为 `nums1`前 ⾯的数我们都已经在之前验证过。因此，我们可以从 `nums1`往后的数开始列举。

### C++版本

```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        int n = nums.size();
        for (int i = 0; i < n; i++) { // 第一层循环从前往后列举第一个数
            for (int j = i + 1; j < n; j++) { // 第二层循环从 i 位置之后列举第二个数
                if (nums[i] + nums[j] == target) // 两个数的和等于目标值，说明我们已经找到结果了
                    return {nums[i], nums[j]};
            }
        }
        return {-1, -1};
    }
};
```

##  题解⼆（双指针 - 对撞指针）

### 算法思路

本题是升序的数组，因此可以⽤「对撞指针」优化时间复杂度。

### 算法流程

当 `left`、`right` 分别指向数组的左右两端时（这里指的是数组的下标）：

- 当 `left < right` 时，一直循环。
  
  i. 如果 `nums[left] + nums[right] == target`，说明找到结果，记录结果，并且返回。
  
  ii. 当 `nums[left] + nums[right] < target` 时：
  
    - 对于 `nums[left]` 而言，此时 `nums[right]` 相当于是 `nums[left]` 能够碰到的**最大值**（升序数组）。如果此时不符合要求，说明在这个数组里面没有其他的数符合 `nums[left]` 的要求。因此，我们可以舍去这个数，让 `left++`，去**比较下一组数据**。
    
    - 针对 `nums[right]`，由于此时两数之和小于目标值，`nums[right]` 仍然可以选择比 `nums[left]` 大的值继续努力达到目标值，因此 `right` 指针我们按兵不动。
  
  iii. 当 `nums[left] + nums[right] > target` 时，同理我们可以舍去 `nums[right]`（最小的数都无法满足）。让 `right--`，继续比较下一组数据，而 `left` 指针不变（因为它还可以去匹配比 `nums[right]` 更小的数）。

### C++版本

```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        int left = 0, right = nums.size() - 1;

        while (left < right) {
            // 计算当前两个指针指向的元素之和
            int sum = nums[left] + nums[right];

            if (sum > target) {
                // 如果和大于目标值，减小和的值，移动右指针
                right--;
            } else if (sum < target) {
                // 如果和小于目标值，增大和的值，移动左指针
                left++;
            } else {
                // 如果和等于目标值，找到结果，返回
                return {nums[left], nums[right]};
            }
        }

        // 循环结束仍未找到符合条件的两个数
        return {-1, -1};
    }
};
```

### Java版本

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        int left = 0, right = nums.length - 1;

        while (left < right) {
            // 计算当前两个指针指向的元素之和
            int sum = nums[left] + nums[right];

            if (sum > target) {
                // 如果和大于目标值，减小和的值，移动右指针
                right--;
            } else if (sum < target) {
                // 如果和小于目标值，增大和的值，移动左指针
                left++;
            } else {
                // 如果和等于目标值，找到结果，返回
                return new int[]{nums[left], nums[right]};
            }
        }

        // 循环结束仍未找到符合条件的两个数
        return new int[]{-1, -1};
    }
}
```

