# 0001 - Two Sum

## 一、题目链接

LeetCode：https://leetcode.cn/problems/two-sum/

## 二、题目描述

给定一个整数数组 `nums` 和一个整数目标值 `target`，请在数组中找出和为目标值的两个整数，并返回它们的数组下标。

题目保证每种输入只会对应一个答案，并且同一个元素不能使用两遍。

## 三、题型

数组 + 哈希表

## 四、我的第一思路

最直接的想法是两层循环：

- 第一层枚举第一个数；
- 第二层枚举第二个数；
- 判断两个数之和是否等于 `target`。

这种方法可以做出来，但是时间复杂度是 O(n²)，效率不够高。


## 五、正确思路

使用哈希表记录已经遍历过的数字以及它们对应的下标。

对于当前数字 `nums[i]`，我们希望找到另一个数字：

```text
need = target - nums[i]
```

如果 `need` 已经在哈希表中出现过，说明之前某个位置的数字可以和当前数字组成目标值。

此时直接返回：

```cpp
return {hash[need], i};
```

如果 `need` 没有出现过，就把当前数字和它的下标存入哈希表，留给后面的数字匹配。

---

## 六、代码

```cpp
#include <vector>
#include <unordered_map>
using namespace std;

class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int, int> hash;

        for (int i = 0; i < nums.size(); i++) {
            int need = target - nums[i];

            if (hash.count(need)) {
                return {hash[need], i};
            }

            hash[nums[i]] = i;
        }

        return {};
    }
};
```

---

## 七、关键代码理解

### 1. 为什么要用哈希表？

哈希表可以快速判断某个数字之前是否出现过。

如果不用哈希表，就需要两层循环：

```cpp
for (int i = 0; i < nums.size(); i++) {
    for (int j = i + 1; j < nums.size(); j++) {
        if (nums[i] + nums[j] == target) {
            return {i, j};
        }
    }
}
```

这样时间复杂度是 `O(n²)`。

使用哈希表后，只需要遍历一次数组，时间复杂度可以降为 `O(n)`。

---

### 2. `need = target - nums[i]` 是什么意思？

当前数字是 `nums[i]`，目标和是 `target`。

所以当前数字需要找的另一个数字就是：

```text
need = target - nums[i]
```

例如：

```text
nums[i] = 7
target = 9
need = 9 - 7 = 2
```

也就是说，只要之前出现过 `2`，那么 `2 + 7 = 9`，就找到答案了。

---

### 3. 为什么先判断，再存入哈希表？

代码中是这样写的：

```cpp
if (hash.count(need)) {
    return {hash[need], i};
}

hash[nums[i]] = i;
```

也就是：

1. 先判断之前有没有出现过 `need`；
2. 如果没有，再把当前数字存入哈希表。

这样可以避免同一个元素被使用两次。

例如：

```text
nums = [3, 2, 4]
target = 6
```

当遍历到第一个 `3` 时，如果先把 `3` 存进去，再找 `need = 3`，就可能错误地把同一个 `3` 用两次。

所以正确写法是：**先查找，再存当前值**。

---

### 4. `hash[nums[i]] = i` 存的是什么？

哈希表中存的是：

```text
数字 -> 下标
```

例如：

```text
nums = [2, 7, 11, 15]
```

遍历到 `2` 时，存入：

```text
hash[2] = 0
```

遍历到 `7` 时，发现：

```text
need = 9 - 7 = 2
```

此时 `2` 已经在哈希表中，所以返回：

```cpp
return {hash[2], 1};
```

也就是：

```cpp
return {0, 1};
```

---

## 八、复杂度分析

时间复杂度：`O(n)`

因为只需要遍历一次数组，每个元素最多被访问一次。

空间复杂度：`O(n)`

因为最坏情况下，哈希表中可能存储所有数组元素。

---

## 九、容易出错的地方

### 1. 返回的是下标，不是数字

题目要求返回两个数的数组下标，而不是返回两个数本身。

错误理解：

```cpp
return {need, nums[i]};
```

正确写法：

```cpp
return {hash[need], i};
```

---

### 2. 不能重复使用同一个元素

题目明确说同一个元素不能使用两遍。

所以不能让当前位置的数字和自己配对。

---

### 3. 注意哈希表存储的方向

本题中哈希表应该存：

```text
数字 -> 下标
```

也就是：

```cpp
unordered_map<int, int> hash;
```

其中：

```text
key   = 数字
value = 下标
```

---

## 十、复盘总结

这道题的核心思想是：

> 一边遍历数组，一边用哈希表记录已经出现过的数字。

对于当前数字 `nums[i]`，我们不需要再从头暴力找另一个数，而是直接判断：

```text
target - nums[i]
```

是否已经在哈希表中出现过。

如果出现过，就说明找到了答案。

这类题目以后看到关键词：

* 两数之和
* 找两个数满足某种关系
* 返回下标
* 要降低双重循环复杂度

可以优先考虑：

```text
哈希表
```
