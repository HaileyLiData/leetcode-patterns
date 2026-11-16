# Solutions 题解

先按 LeetCode 75 的 22 类排列，之后加 NeetCode 150。每题：思路、易错点、复杂度、代码。每类的套路模板和我的思考过程见 [patterns.md](./patterns.md)。

---

## 1. Array / String 数组/字符串

### 1768. Merge Strings Alternately 交替合并字符串 (Easy)

[题目链接](https://leetcode.com/problems/merge-strings-alternately/)

- 套路：一个指针 i 从左往右，两个单词轮流放字母
- 易错：工具后面跟圆括号 ()，取第几个用方括号 [] —— `"".join(res)`、`res.append(word1[i])`
- 时间 O(m + n)，空间 O(m + n)

```python
class Solution:
    def mergeAlternately(self, word1: str, word2: str) -> str:
        res = []
        i = 0
        while i < len(word1) or i < len(word2):
            if i < len(word1):
                res.append(word1[i])
            if i < len(word2):
                res.append(word2[i])
            i = i + 1
        return "".join(res)
```

### 1071. Greatest Common Divisor of Strings 字符串的最大公因子 (Easy)

[题目链接](https://leetcode.com/problems/greatest-common-divisor-of-strings/)

- 思路：str1 + str2 != str2 + str1 → 没有共同单元；单元长度 = 两个长度的最大公约数 gcd
- 时间 O(m + n)，空间 O(m + n)

```python
from math import gcd


class Solution:
    def gcdOfStrings(self, str1: str, str2: str) -> str:
        if str1 + str2 != str2 + str1:
            return ""
        k = gcd(len(str1), len(str2))
        return str1[:k]
```

### 1431. Kids With the Greatest Number of Candies 拥有最多糖果的孩子 (Easy)

[题目链接](https://leetcode.com/problems/kids-with-the-greatest-number-of-candies/)

- 思路：先找最大值 m，每个孩子加上 extraCandies 后和 m 比；比较的结果本身就是 True / False
- 时间 O(n)，空间 O(n)

```python
from typing import List


class Solution:
    def kidsWithCandies(self, candies: List[int], extraCandies: int) -> List[bool]:
        m = max(candies)
        return [c + extraCandies >= m for c in candies]
```

### 605. Can Place Flowers 种花问题 (Easy)

[题目链接](https://leetcode.com/problems/can-place-flowers/)

- 思路：贪心，能种就种；左右都空才能种
- 易错：or 的顺序 —— 安全检查（是不是边上）放前面，挡住后面越界的取值
- 时间 O(n)，空间 O(1)

```python
from typing import List


class Solution:
    def canPlaceFlowers(self, flowerbed: List[int], n: int) -> bool:
        count = 0
        for i in range(len(flowerbed)):
            if flowerbed[i] == 0:
                left_empty = (i == 0) or (flowerbed[i - 1] == 0)
                right_empty = (i == len(flowerbed) - 1) or (flowerbed[i + 1] == 0)
                if left_empty and right_empty:
                    flowerbed[i] = 1
                    count = count + 1
        return count >= n
```

### 345. Reverse Vowels of a String 反转字符串中的元音字母 (Easy)

[题目链接](https://leetcode.com/problems/reverse-vowels-of-a-string/)

- 套路：双指针，相向而行（left 从左、right 从右，往中间走，碰头就停）
- 第一次盲写（2026-11-15）忘了 while → 只走一步就 return
- 时间 O(n)，空间 O(n)

```python
class Solution:
    def reverseVowels(self, s: str) -> str:
        vowels = set("aeiouAEIOU")
        chars = list(s)
        left = 0
        right = len(chars) - 1
        while left < right:
            if chars[left] not in vowels:
                left = left + 1
            elif chars[right] not in vowels:
                right = right - 1
            else:
                chars[left], chars[right] = chars[right], chars[left]
                left = left + 1
                right = right - 1
        return "".join(chars)
```
