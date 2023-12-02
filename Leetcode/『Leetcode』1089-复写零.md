---
title: 复写零
categories:
  - Leetcode
tags:
  - 算法题-双指针
cover: >-
  https://memoo-blog-1314753088.cos.ap-beijing.myqcloud.com/Blogpost%2FpostImage-18.webp
description: "\U0001F4DD数组元素右移，复写零，空间复杂度O(1)。"
mathjax: true
abbrlink: ff8d7d24
date: 2023-11-27 00:00:00
updated: 2023-11-27 00:00:00
swiper_index:
---
# [复写零](https://leetcode.cn/problems/duplicate-zeros/)

## 题目描述

给你一个**长度固定**的整数数组 `arr` ，请你将该数组中出现的每个零都复写一遍，并将其余的元素向右平移。

注意：请不要在超过该数组长度的位置写入元素。请对输入的数组 **就地** 进行上述修改，不要从函数返回任何东西。

**示例 1：**

```
输入：arr = [1,0,2,3,0,4,5,0]
输出：[1,0,0,2,3,0,0,4]
解释：调用函数后，输入的数组将被修改为：[1,0,0,2,3,0,0,4]
```

**示例 2：**

```
输入：arr = [1,2,3]
输出：[1,2,3]
解释：调用函数后，输入的数组将被修改为：[1,2,3]
```

**提示：**

- `1 <= arr.length <= 10^4`
- `0 <= arr[i] <= 9`

## 代码实现

```cpp
class Solution {
public:
    void duplicateZeros(std::vector<int>& arr) {
        int cur = 0;      // 当前遍历的位置
        int dest = -1;    // 最后一个复写的位置
        int num = arr.size();  // 数组的长度

        // 第一次遍历，找到最后一个复写的位置
        while (cur < num) {
            // 如果当前元素是非零，直接将 dest 和 cur 都加1
            if (arr[cur] != 0) {
                dest++;
            } else {
                // 如果当前元素是零，因为我们要复写两次零，所以 dest 加2
                dest += 2;
            }

            // 如果 dest 超过数组长度减1，跳出循环
            if (dest >= num - 1) {
                break;
            }

            // 继续遍历下一个位置
            cur++;
        }

        // 处理特殊情况：最后一个复写的位置在数组末尾
        if (dest == num) {
            // 在数组末尾设置零，并调整 dest 和 cur
            arr[num - 1] = 0;
            dest -= 2;
            cur--;
        }

        // 第二次遍历，从数组末尾开始移动元素
        while (cur >= 0) {
            // 如果当前元素是非零，直接将其复写到 dest 的位置，然后将 dest 和 cur 分别减1
            if (arr[cur] != 0) {
                arr[dest--] = arr[cur--];
            } else {
                // 如果当前元素是零，需要复写两次零，所以将 dest 的位置和 dest-1 的位置都设置为零，并将 cur 减2
                arr[dest--] = 0;
                arr[dest--] = 0;
                cur--;
            }
        }
    }
};
```

## 解题思路

1. **找到最后一个复写的位置：** 从数组的开头开始遍历，统计数组中的零的个数，得到最后一个复写的位置。为什么要找最后一个复写的位置呢？因为在进行元素移动的时候，我们需要从后往前移动，防止覆盖未处理的元素。

    ```cpp
    int cur = 0;  // 当前遍历的位置
    int dest = -1;  // 最后一个复写的位置
    int num = arr.size();  // 数组的长度

    // 找到最后一个复写的位置
    while (cur < num) {
        if (arr[cur] != 0) {
            dest++;
        } else {
            dest += 2;
        }

        if (dest >= num - 1) {
            break;
        }

        cur++;
    }
    ```

2. **处理特殊情况：** 如果最后一个复写的位置 `dest` 恰好在数组末尾，说明最后一个元素也需要被复写，因此我们在数组的最后一个位置设置为零，否则会导致越界，并相应地调整 `dest` 和 `cur`。

    ```cpp
    if (dest == num) {
        arr[num - 1] = 0;
        dest -= 2;
        cur--;
    }
    ```

3. **从数组末尾开始移动元素：** 在这个阶段，我们需要从数组的末尾开始向前遍历，将非零元素移动到正确的位置，对于零元素，需要复写两次零。

    ```cpp
    while (cur >= 0) {
        if (arr[cur] != 0) {
            arr[dest--] = arr[cur--];
        } else {
            arr[dest--] = 0;
            arr[dest--] = 0;
            cur--;
        }
    }
    ```

这样，通过两次遍历，我们就能够在原地修改数组，实现题目要求的功能。这个思路的关键在于先确定最后一个复写的位置，然后从数组的末尾开始移动元素。