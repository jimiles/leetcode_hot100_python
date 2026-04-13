# dp题目可以分为8类：


### 线性一维dp



<details>
<summary>1. 爬楼梯 </summary>

```python
class Solution(object):
    def climbStairs(self, n):
        """
        :type n: int
        :rtype: int
        """
        # 一维线性dp
        if n <= 2:
            return n   
        
        dp = [0] * (n+1)
        dp[0] = 0
        dp[1] = 1
        dp[2] = 2

        for i in range(3,n+1):
            dp[i] = dp[i-1] + dp[i-2]

        return dp[n]

```
</details>


<details>
<summary>2. 打家劫舍 </summary>

```python
class Solution(object):
    def rob(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        # 不能连偷两家人

        n = len(nums)

        if n == 0 :
            return 0

        if n == 1:
            return nums[0]

        dp = [0] * (n+1)
        dp[0] = 0
        dp[1] = nums[0]
        for i in range(2,n+1):
        # dp[i] = max(不偷当前这家，偷当前这家)
        # dp[i] 表示第i间房子前的结果
            dp[i] = max(dp[i-1],dp[i-2] + nums[i-1])

        return dp[n]
```
</details>

<details>
<summary>3. 乘积最大字数组 </summary>

```python
class Solution(object):
    def maxProduct(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        # 因为有负数的存在，所以大小值都要维护。
        # 同时维护“以当前位置结尾的最大乘积”和“以当前位置结尾的最小乘积”。
        n = len(nums)

        max_dp = [0] * n
        min_dp = [0] * n

        max_dp[0] = min_dp[0] = nums[0]
        res = nums[0]
        for i in range(1,n):
            max_dp[i] = max(
                nums[i],
                max_dp[i-1] * nums[i],
                min_dp[i-1] * nums[i] 
            )
            
            min_dp[i] = min(
                nums[i],
                max_dp[i-1] * nums[i],
                min_dp[i-1] * nums[i] 
            )

            res = max(res,max_dp[i])

        return res


```
</details>

<details>
<summary>4. 最长有效括号 </summary>

```python

```
</details>

### 背包dp
1. 完全平方数
2. 零钱兑换
3. 分割等和子集

### 前缀划分dp
1. 单词拆分

### 序列型dp
1. 最长递增子序列

### 状态构造型dp
1. 杨辉三角

### 二维路径dp
1. 不同路径
2. 最小路径和

### 区间dp
1. 最长回文子串


### 双序列dp


<details>
<summary>1. 最长公共子序列</summary>

```python
class Solution(object):
    def longestCommonSubsequence(self, text1, text2):
        """
        :type text1: str
        :type text2: str
        :rtype: int
        """
        # 双序列dp，分别看dp[i][j]是text1,text2的前i个字符(可以是前0个)
        m = len(text1)
        n = len(text2)


        dp = [[0]*(n+1) for _ in range(m+1)]

        # 注意范围从1开始
        for i in range(1,m+1):
            for j in range(1,n+1):
                # 如果 i,j 不一样，那么从左或上找一个大的就行，如果一样，就从i-1,j-1 + 1
                if text1[i-1] == text2[j-1]:
                    dp[i][j] = dp[i-1][j-1] + 1
                else:
                    dp[i][j] = max(dp[i][j-1],dp[i-1][j])

        return dp[m][n]
        
```


</details>

<details>
<summary>2. 编辑距离</summary>

```python
class Solution(object):
    def minDistance(self, word1, word2):
        """
        :type word1: str
        :type word2: str
        :rtype: int
        """
        # 双序列dp
        # word1的前i变成word2的前j需要的最小操作次数    
        m = len(word1)
        n = len(word2)

        dp = [[0] * (n+1) for _ in range(m+1)]
        # 相比于最长子序列只是多了边缘扫描
        for i in range(m+1):
            dp[i][0] = i
        for j in range(n+1):
            dp[0][j] = j 
        
        for i in range(1,m+1):
            for j in range(1,n+1):
                if word1[i-1] == word2[j-1]:
                    dp[i][j] = dp[i-1][j-1]
                else:
                    dp[i][j] = min(dp[i-1][j]+1,
                                    dp[i][j-1]+1,
                                    dp[i-1][j-1]+1)

        return dp[m][n]
        
```

</details>
