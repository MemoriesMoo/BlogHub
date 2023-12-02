---
title: 移动零
categories:
  - Leetcode
tags:
  - 算法题-双指针
cover: >-
  https://memoo-blog-1314753088.cos.ap-beijing.myqcloud.com/Blogpost%2FpostImage-12.webp
description: "\U0001F522将数组中的零移至末尾，保持非零元素相对顺序。"
mathjax: true
abbrlink: 1920b0ec
date: 2023-11-27 00:00:00
updated: 2023-11-27 00:00:00
swiper_index:
---
# [移动零](https://leetcode.cn/problems/move-zeroes/)

## 题目描述

给定一个数组 `nums`，编写一个函数将所有 `0` 移动到数组的末尾，同时保持非零元素的相对顺序。

**请注意** ，必须在**不复制数组**的情况下**原地**对数组进行操作。

**示例 1:**

```
输入: nums = [0,1,0,3,12]
输出: [1,3,12,0,0]
```

**示例 2:**

```
输入: nums = [0]
输出: [0]
```

**提示**

- `1 <= nums.length <= 10^4`
- `-2^31 <= nums[i] <= 2^31 - 1`

进阶：你能尽量减少完成的操作次数吗？

## 实现思路

1. **初始化两个指针：** 我们定义两个指针 `cur` 和 `nonZeroIndex`。`cur` 指针用于遍历整个数组，而 `nonZeroIndex` 则用于标记非零元素应该放置的位置。一开始，两个指针都指向数组的起始位置。

    ```c++
    int cur = 0;           // 用于遍历数组的指针
    int nonZeroIndex = 0;  // 标记非零元素的位置
    ```

2. **遍历数组：** 我们使用 `cur` 指针遍历整个数组。

    - 如果 `nums[cur]` 是非零元素，我们将其交换到 `nonZeroIndex` 的位置，并递增 `nonZeroIndex`。这样，非零元素就被放置到数组的前部。

    - 如果 `nums[cur]` 是零元素，我们继续遍历，因为零元素不需要移动。

    ```c++
    for (int cur = 0; cur < nums.length; ++cur) {
        if (nums[cur] != 0) {
            // 将非零元素交换到 nonZeroIndex 的位置
            int temp = nums[nonZeroIndex];
            nums[nonZeroIndex++] = nums[cur];
            nums[cur] = temp;
        }
    }
    ```

    通过这个过程，非零元素会被依次移到数组的前部。

3. **填充剩余位置：** 遍历完成后，`nonZeroIndex` 表示了非零元素的个数。接下来，我们从 `nonZeroIndex` 开始，将数组的剩余位置都填充为零。

    ```c++
    while (nonZeroIndex < nums.length) {
        nums[nonZeroIndex++] = 0;
    }
    ```

    这个步骤确保了剩余的位置都被零填充，最终实现了将所有零移动到数组末尾并保持非零元素相对顺序的目标。
    
    > 整个过程中，非零元素会被**依次**移到数组的前部，而剩余的位置都会被填充为零。由于只进行了一次遍历，因此**时间复杂度为 O(n)**。此外，这个方法是原地操作的，没有使用额外的数组，满足了题目中的要求。

## 代码实现

#### C++版本

```cpp
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        int nonZeroIndex = 0;

        // 遍历数组，将非零元素移到前面
        for (int cur = 0; cur < nums.size(); ++cur) {
            if (nums[cur] != 0) {
                swap(nums[nonZeroIndex++], nums[cur]);
            }
        }

        // 填充剩余位置为零
        while (nonZeroIndex < nums.size()) {
            nums[nonZeroIndex++] = 0;
        }
    }
};
```

#### C版本

```c
void moveZeroes(int* nums, int numsSize) {
    int nonZeroIndex = 0;

    // 遍历数组，将非零元素移到前面
    for (int cur = 0; cur < numsSize; ++cur) {
        if (nums[cur] != 0) {
            int temp = nums[nonZeroIndex];
            nums[nonZeroIndex++] = nums[cur];
            nums[cur] = temp;
        }
    }

    // 填充剩余位置为零
    while (nonZeroIndex < numsSize) {
        nums[nonZeroIndex++] = 0;
    }
}
```

#### java版本

```java
public class Solution {
    public void moveZeroes(int[] nums) {
        int nonZeroIndex = 0;

        // 遍历数组，将非零元素移到前面
        for (int cur = 0; cur < nums.length; ++cur) {
            if (nums[cur] != 0) {
                int temp = nums[nonZeroIndex];
                nums[nonZeroIndex++] = nums[cur];
                nums[cur] = temp;
            }
        }

        // 填充剩余位置为零
        while (nonZeroIndex < nums.length) {
            nums[nonZeroIndex++] = 0;
        }
    }

}
```

