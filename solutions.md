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
- 第一遍练写（2026-11-15）忘了 while → 只走一步就 return
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

### 151. Reverse Words in a String 反转字符串中的单词 (Medium)

[题目链接](https://leetcode.com/problems/reverse-words-in-a-string/)

- 思路：split() 切成单词 → reversed() 倒过来 → " ".join() 用一个空格拼回
- 易错：split() 括号要空着（自动去掉首尾和多余空格）；split(" ") 会切出空字符串 ''
- 和 NLP 的关系：清理空格 = text normalization，split() = 最简单的 tokenization
- 时间 O(n)，空间 O(n)

```python
class Solution:
    def reverseWords(self, s: str) -> str:
        words = s.split()
        return " ".join(reversed(words))
```

### 238. Product of Array Except Self 除自身以外数组的乘积 (Medium)

[题目链接](https://leetcode.com/problems/product-of-array-except-self/)

- 题目：盖住一格，把剩下的数乘起来；不许用除法，要 O(n)
- 思路（三步，先学这个版本）：左边一排存每格的"左边乘积" → 右边一排存"右边乘积"（写法和左边对称）→ 逐格 左 × 右
- 套路：前缀积 / 后缀积（第 4 类前缀和的乘法版），见 patterns.md
- 易错：每格先抄、再乘（顺序反了会把自己算进去）；起点 left = 1、right = 1（乘法里"还没有数"是 1，写 0 会全变 0）
- 省空间版（Follow up，O(1) 额外空间）：不另开 right_products，把右边乘积直接乘进 answer，熟了再看
- 时间 O(n)，空间 O(n)

```python
class Solution:
    def productExceptSelf(self, nums: List[int]) -> List[int]:
        n = len(nums)

        left_products = [1] * n
        left = 1
        for i in range(n):
            left_products[i] = left
            left = left * nums[i]

        right_products = [1] * n
        right = 1
        for i in reversed(range(n)):
            right_products[i] = right
            right = right * nums[i]

        answer = []
        for i in range(n):
            answer.append(left_products[i] * right_products[i])
        return answer
```

### 334. Increasing Triplet Subsequence 递增的三元子序列 (Medium)

[题目链接](https://leetcode.com/problems/increasing-triplet-subsequence/)

- 题目：有没有三个数，位置从左到右、数字越来越大（严格变大，相等不算）；只问有没有，找到一组就 True
- 思路：first = 目前见过的最小；second = 在某个更小的数后面出现的第二小。来一个数：比 first 小 → 更新 first；否则比 second 小 → 更新 second；比两个都大 → 凑齐三个，True
- 起始值：first、second 都放 float("inf") 无穷大（找最小从最大的开始，第一个数一定能顶掉它）
- 易错：必须用 <=，不能用 <（第一遍提交写成 <，挂在全是 1 的测试：相等的数掉到下一档，1 < 1 < 1 被当成 True）
- 时间 O(n)，空间 O(1)

```python
class Solution:
    def increasingTriplet(self, nums: List[int]) -> bool:
        first = float("inf")
        second = float("inf")
        for num in nums:
            if num <= first:
                first = num
            elif num <= second:
                second = num
            else:
                return True
        return False
```
