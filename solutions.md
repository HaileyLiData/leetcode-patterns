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

### 443. String Compression 压缩字符串 (Medium)

[题目链接](https://leetcode.com/problems/string-compression/)

- 题目：连续重复的字母 → 字母 + 个数（1 个不写个数；10 个以上拆成多位，12 → '1' '2'）；直接写回 chars（覆盖着写），返回新长度；超出返回长度的格子不用管
- 思路：三根手指，每组做三步「数、写、跳」
  - 数：j 从 i 同一格出发往后走，停在重复的字母之后、新开始的字母那里 → 个数 = j - i（j 自己不算，不用 + 1）
  - 写：在 write 格写字母；个数 > 1 时 str(个数) 一位一位写进去；每写一格 write 走一步
  - 跳：i = j，去下一组
- 为什么覆盖不出错：写的格数不会比读的多，write 永远不会跑到 i 前面
- i 和 write 都从 0 开始但会分开：i 记读到哪（会跳），write 记写到哪；return 的是 write
- ch = chars[i] 只是为了好读，直接写 chars[i] 也对
- 时间 O(n)，空间 O(1)

```python
class Solution:
    def compress(self, chars: List[str]) -> int:
        write = 0
        i = 0
        while i < len(chars):
            ch = chars[i]
            j = i
            while j < len(chars) and chars[j] == ch:
                j = j + 1
            chars[write] = ch
            write = write + 1
            if j - i > 1:
                for d in str(j - i):
                    chars[write] = d
                    write = write + 1
            i = j
        return write
```

## 2. Two Pointers 双指针

### 283. Move Zeroes 移动零 (Easy)

[题目链接](https://leetcode.com/problems/move-zeroes/)

- 题目：把所有 0 移到末尾，非零数保持原来的相对顺序（relative order）；必须原地改（in place），函数不 return
- 思路：快慢两根手指，同向走
  - slow = 下一个非零数该放第几格（不是“0 在哪”）；一开始还没放过，所以是 0
  - fast 从头扫到尾；遇到非零数 → 和 slow 那格交换，slow 往前一格
  - slow 落后 fast 时，slow 那格一定是 0 → 0 一个一个被换到后面
- 交换 `nums[slow], nums[fast] = nums[fast], nums[slow]`：先把右边两个值都拿到手，再放进左边
- 每一圈用的是交换后的 nums（原地改，只有一个 list）
- 易错：交换只看 slow 和 fast 的格子号，不是把数挪到“最近的 0”；卡住先在数字上面写出格子号 0 1 2 3 4
- 时间 O(n)，空间 O(1)

```python
class Solution:
    def moveZeroes(self, nums: List[int]) -> None:
        slow = 0
        for fast in range(len(nums)):
            if nums[fast] != 0:
                nums[slow], nums[fast] = nums[fast], nums[slow]
                slow = slow + 1
```

### 392. Is Subsequence 判断子序列 (Easy)

[题目链接](https://leetcode.com/problems/is-subsequence/)

- 题目：s 是不是 t 的子序列：从 t 里删掉一些字母（可以一个都不删），不打乱剩下字母的相对位置，能不能得到 s（"ace" 是 "abcde" 的子序列，"aec" 不是）
- 思路：两根手指，同向走，各管一个字符串
  - ch 走 t：`for ch in t`，每圈一格
  - i 指着 s = 下一个要找的字母；每圈只和 `s[i]` 这一个比，匹配上才 i + 1
  - 隔开的字母：ch 照样走过去，i 原地等着
  - 顺序乱了找不到：两根手指都只往前、不回头，i 一次只找一个
- `i < len(s)` 每圈检查 i 还在不在 s 里（全找到后再读 s[i] 会 IndexError）；写在 and 前面：前面不成立就不看后面
- `return i == len(s)`：== 自己就给出 True / False
- 易错：第一次提交写成 `i = 1` → Wrong Answer 16 / 23，挂在 s = ""（应返回 True）。i = 1 跳过了 s[0]；从头开始的手指起点是 0。题目说 can be none → 想到空字符串
- 时间 O(len(t))，空间 O(1)

```python
class Solution:
    def isSubsequence(self, s: str, t: str) -> bool:
        i = 0
        for ch in t:
            if i < len(s) and s[i] == ch:
                i = i + 1
        return i == len(s)
```

### 11. Container With Most Water 盛最多水的容器 (Medium)

[题目链接](https://leetcode.com/problems/container-with-most-water/)

- 题目：每个数 = 一根竖线，下标 = 位置，值 = 高度；挑两根线和 x 轴组成桶，求最多装多少水。may not slant = 不允许倾斜 → 水只能到矮的那根
- 水量 = 宽 × 高
  - 宽 = 两根线的**下标相减**（不是数中间夹了几根）
  - 高 = 两根里**矮的那根**（短板）
- 思路：两根手指，相向走
  - 先站两头（最宽），算水量，存进 best
  - 每次扔掉**矮的那根**：它对面已经是离它最远的线，以后再配谁都更窄、水高也超不过它自己 → 它已交出最好成绩
  - 高的留着；对面出现更高的线，它变成矮的一边，就轮到它走
- 直接遍历（两层 for，每两根都配）答案也对，但 10 万根线 ≈ 50 亿次会超时；双指针最多 n 次
- 易错 / 我卡住的地方：
  - 两根一样高的 8，靠下标分辨（我先算成第二个 8 配 7 = 14，第一个 8 配 7 才是 49）
  - 扔掉 7 不会丢 49：每圈**先算、先存进 best、最后才扔**
  - `if` 不是“小于才移动”，每圈一定动一边，`if` 只是挑动哪边；一样高走 else
- 时间 O(n)，空间 O(1)

```python
class Solution:
    def maxArea(self, height: List[int]) -> int:
        left = 0
        right = len(height) - 1
        best = 0
        while left < right:
            width = right - left
            h = min(height[left], height[right])
            water = width * h
            best = max(best, water)
            if height[left] < height[right]:
                left = left + 1
            else:
                right = right - 1
        return best
```

### 1679. Max Number of K-Sum Pairs K 和数对的最大数目 (Medium)

[题目链接](https://leetcode.com/problems/max-number-of-k-sum-pairs/)

- 题目：一次操作 = 挑两个和等于 k 的数删掉（每个数只能用一次），最多能做几次（[3,1,3,4,3]，k = 6 → 三个 3 只能配一对 → 1）
- 思路：**先排序**，再两根手指相向走
  - 排序后最左 = 最小、最右 = 最大，下面三条才成立（原数组左右谁大谁小不一定）
  - 和 < k：太小 → left + 1 换大的；左边这个数跟最大的配都不够，跟谁配都不够 → 扔掉
  - 和 > k：太大 → right − 1 换小的；右边这个数跟最小的配都太大 → 扔掉
  - 和 = k：配成一对，count + 1，两个数用掉了 → 两边都往里走
- 和 11 同一个骨架：相向走、每步扔掉一个“已经没用”的数；11 扔矮的，这题看和比 k 小还是大
- 时间 O(n log n)（排序），空间 O(1)

```python
class Solution:
    def maxOperations(self, nums: List[int], k: int) -> int:
        nums.sort()
        left = 0
        right = len(nums) - 1
        count = 0
        while left < right:
            s = nums[left] + nums[right]
            if s == k:
                count = count + 1
                left = left + 1
                right = right - 1
            elif s < k:
                left = left + 1
            else:
                right = right - 1
        return count
```

## 3. Sliding Window 滑动窗口

### 643. Maximum Average Subarray I 子数组最大平均数 I (Easy)

[题目链接](https://leetcode.com/problems/maximum-average-subarray-i/)

- 题目：找长度正好是 k 的连续子数组（contiguous subarray），返回最大的平均值；误差小于 10^-5 算对（除法有小数，写代码不用管）
- 每段长度都是 k → 和最大的那段平均也最大 → 先找最大的和，最后除以 k
- 思路：长度 k 的框（窗口）每次往右挪一格
  - 第一个窗口：`sum(nums[:k])`，前 k 个（切片 [开始:结束] 顾头不顾尾）
  - 挪一格只变两个数：新的和 = 旧的和 + 进来的 − 出去的，不用重新加 k 个
  - 进来 `nums[i]`，出去 `nums[i - k]`：两个下标正好差 k（隔着一个窗口的长度）
- `for i in range(k, len(nums))`：i = 新进来的数；0～k−1 已经在第一个窗口里，所以从 k 开始
- 易错：
  - `range(k, len(nums))` 不要 −1：range 自己不取结尾；`right = len(nums) - 1` 是直接当门牌号才要 −1
  - `i - k` 搞不清就代 i = 4 进去算，不要在脑子里加 1 减 1
- 时间 O(n)，空间 O(1)

```python
class Solution:
    def findMaxAverage(self, nums: List[int], k: int) -> float:
        window = sum(nums[:k])
        best = window
        for i in range(k, len(nums)):
            window = window + (nums[i] - nums[i - k])
            best = max(best, window)
        return best / k
```

### 1456. Maximum Number of Vowels in a Substring of Given Length 定长子串中元音的最大数目 (Medium)

[题目链接](https://leetcode.com/problems/maximum-number-of-vowels-in-a-substring-of-given-length/)

- 题目：s 里所有长度为 k 的子串（substring，连续的一段）中，元音字母（a、e、i、o、u）最多有几个
- 思路：和 643 同一个定长窗口，框里算的不是“和”，而是“元音有几个”
  - 第一段（还没滑）：数前 k 个字母里有几个元音 → count，也放进 best
  - 第二段（开始滑）：进来的 `s[i]` 是元音 +1，出去的 `s[i - k]` 是元音 −1
- `vowels = set("aeiou")`：set() 是 Python 自带函数，装成集合 {a, e, i, o, u}，只用来问“在不在”
- 用**两个 if**，不是 if / elif：进来、出去可能都是元音，两件事都要做
- 压缩写法（认识就行）：`sum(1 for ch in s[:k] if ch in vowels)`；`count + ((s[i] in vowels) - (s[i - k] in vowels))`（True 当 1、False 当 0）
- 易错：
  - `range(3, 9)` → 3, 4, 5, 6, 7, 8：**包含开头、不包含结尾**（我以为两头都不包含）；不确定就 `print(list(range(3, 9)))`
  - 下标 + 1 = 第几个：k = 3、i = 3（第 4 个）进来，i − k = 0（第 1 个）出去
- 时间 O(n)，空间 O(1)

```python
class Solution:
    def maxVowels(self, s: str, k: int) -> int:
        vowels = set("aeiou")
        count = 0
        for ch in s[:k]:
            if ch in vowels:
                count = count + 1
        best = count
        for i in range(k, len(s)):
            if s[i] in vowels:
                count = count + 1
            if s[i - k] in vowels:
                count = count - 1
            best = max(best, count)
        return best
```
