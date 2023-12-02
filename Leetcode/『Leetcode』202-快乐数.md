---
title: 快乐数
categories:
  - Leetcode
tags:
  - 算法题-双指针
cover: >-
  https://memoo-blog-1314753088.cos.ap-beijing.myqcloud.com/Blogpost%2FpostImage-16.webp
description: "\U0001F916判断数是否是快乐数，用快慢指针检测循环。"
mathjax: true
abbrlink: 9ba82001
date: 2023-11-28 00:00:00
updated: 2023-11-28 00:00:00
swiper_index:
---
# [快乐数](https://leetcode.cn/problems/happy-number/)

## 题目描述

编写一个算法来判断一个数 `n` 是不是快乐数。

**「快乐数」** 定义为：

- 对于一个正整数，每一次将该数替换为它每个位置上的数字的平方和。
- 然后重复这个过程直到这个数变为 1，也可能是 **无限循环** 但始终变不到 1。
- 如果这个过程 **结果为** 1，那么这个数就是快乐数。

如果 `n` 是 *快乐数* 就返回 `true` ；不是，则返回 `false` 。

**示例 1：**

```
输入：n = 19
输出：true
解释：
12 + 92 = 82
82 + 22 = 68
62 + 82 = 100
12 + 02 + 02 = 1
```

![](https://memoo-blog-1314753088.cos.ap-beijing.myqcloud.com/Leetcode%2F202-%E5%BF%AB%E4%B9%90%E6%95%B0%2F%E7%A4%BA%E4%BE%8B%E4%B8%80.png)

**示例 2：**

```
输入：n = 2
输出：false
解释：
2 -> 4 -> 16 -> 37 -> 58 -> 89 -> 145 -> 42 -> 20 -> 4 -> 16
```

![](https://memoo-blog-1314753088.cos.ap-beijing.myqcloud.com/Leetcode%2F202-%E5%BF%AB%E4%B9%90%E6%95%B0%2F%E7%A4%BA%E4%BE%8B%E4%BA%8C.png)

**提示：**

- `1 <= n <= 2^31 - 1`

## 解题思路

为了⽅便叙述，将「对于⼀个正整数，每⼀次将该数**替换**为它每个位置上的数字的平⽅和」这⼀个 操作记为`Re`操作。

题⽬告诉我们，当我们不断重复`Re`操作的时候，计算⼀定会「**死循环**」，循环的⽅式有两种： 

▪ **情况⼀**：⼀直在 1 中死循环，即 1 -> 1 -> 1 -> 1...... 。（如示例一图片）

▪ **情况⼆**：在历史的数据中死循环，但始终变不到 1 。（如示例二图片）

由于上述两种情况只会出现⼀种，因此，只要我们能确定循环是在「情况⼀」中进⾏，还是在「情 况⼆」中进⾏，就能得到结果。

**ps: 此时可能会有读者疑问，为什么不可能是一直无规律没有循环呢？**

**证明一下**

- 根据题目提示，`1 <= n <= 2^31 - 1`，即`n`的最大值为`2147483648`，是十位数，不妨假设最大值为`9999999999`，这样我们可以确定此范围内所有数经过Re操作能得到的最大值为`9^2 * 10 = 810`，说明在此范围内，经过`Re`操作最多可产生810种数字，也就是变化的区间在 [1, 810] 之间。
- 根据「`鸽巢原理`」，⼀个数变化 811 次之后，必然会形成⼀个循环。
- 因此不存在无限不循环的情况，变化的过程最终会⾛到⼀个圈⾥⾯，归结于上面提到的两种情况，可以⽤「快慢双指针」来解决。

## 鸽巢原理

> 鸽巢原理是一种基本的数学原理，用于解决一类问题，其中涉及到将一组对象放入一组容器的情况。原理的核心思想是，如果我们有更多的对象要放入容器，而容器的数量又不足以容纳所有对象，那么至少会有一个容器里包含两个或两个以上的对象。
>

具体来说，如果有n个对象要放入m个容器中，而n大于m，那么至少会有一个容器包含两个或两个以上的对象。

**例子：生日问题**

考虑一个房间里有n个人，问至少要多少人，才能确保至少有两个人生日相同？假设一年有365天。

解法：使用鸽巢原理。我们将每个人的生日看作一个“鸽子”，将365天的可能生日看作“鸽巢”。当我们开始增加人数时，就是将鸽子放入鸽巢的过程。如果我们有超过365个人，根据鸽巢原理，至少会有两个人出生在同一天，因为鸽子的数量大于鸽巢的数量。因此，至少需要366人才能确保至少有两个人生日相同。

## 代码实现

> 根据上述的题⽬分析，我们可以知道，当重复执⾏ `Re` 的时候，数据会陷⼊到⼀个「循环」之中。 ⽽「快慢指针」有⼀个特性，就是在⼀个圆圈中，快指针总是会追上慢指针的，也就是说他们总会 相遇在⼀个位置上。如果相遇位置的值是 1 ，那么这个数⼀定是快乐数；如果相遇位置不是 1 的话，那么就不是快乐数。

**解题步骤**

> 1. **定义`Re`操作：** 定义一个函数 `digitSquareSum`，用于计算一个数的每个位置上的数字的平方和。
> 2. **快慢双指针判断循环：** 使用快慢指针，初始化两个指针 `slow` 和 `fast`，初始值都为输入的数 `n`。在循环中，`slow`每次移动一步，`fast`每次移动两步。如果存在循环，它们会相遇。
> 3. **判断相遇点是否为1：** 当快慢指针相遇时，判断相遇点是否为1。如果是1，则说明是快乐数；如果不是1，则说明存在循环但不是快乐数。

### C++版本

```cpp
class Solution {
public:
    int digitSquareSum(int num) {
        // 计算一个数的每个位置上的数字的平方和
        int result = 0;
        while (num > 0) {
            int digit = num % 10;
            result += digit * digit;
            num /= 10;
        }
        return result;
    }

    bool isHappy(int n) {
        // 初始化快慢指针
        int slow = n;
        int fast = n;

        do {
            slow = digitSquareSum(slow);      // 慢指针移动一步
            fast = digitSquareSum(digitSquareSum(fast));  // 快指针移动两步

            if (slow == fast) {
                // 如果快慢指针相遇，说明存在循环
                break;
            }
        } while (fast != 1);

        // 判断相遇点是否为1
        return fast == 1;
    }
};
```

### Java版本

```java
public class Solution {

    public int digitSquareSum(int num) {
        // 计算一个数的每个位置上的数字的平方和
        int result = 0;
        while (num > 0) {
            int digit = num % 10;
            result += digit * digit;
            num /= 10;
        }
        return result;
    }

    public boolean isHappy(int n) {
        // 初始化快慢指针
        int slow = n;
        int fast = n;

        do {
            slow = digitSquareSum(slow);      // 慢指针移动一步
            fast = digitSquareSum(digitSquareSum(fast));  // 快指针移动两步

            if (slow == fast) {
                // 如果快慢指针相遇，说明存在循环
                break;
            }
        } while (fast != 1);

        // 判断相遇点是否为1
        return fast == 1;
    }
}
```

### python版本

```python
class Solution:
    def digitSquareSum(self, num: int) -> int:
        # 计算一个数的每个位置上的数字的平方和
        result = 0
        while num > 0:
            digit = num % 10
            result += digit * digit
            num //= 10
        return result

    def isHappy(self, n: int) -> bool:
        # 初始化快慢指针
        slow = n
        fast = n

        while True:
            slow = self.digitSquareSum(slow)      # 慢指针移动一步
            fast = self.digitSquareSum(self.digitSquareSum(fast))  # 快指针移动两步

            if slow == fast:
                # 如果快慢指针相遇，说明存在循环
                break

        # 判断相遇点是否为1
        return fast == 1
```

