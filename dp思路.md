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
<summary>4. 最长有效括号(⭐️) </summary>

```python
class Solution(object):
    def longestValidParentheses(self, s):
        """
        :type s: str
        :rtype: int
        """
        # 用栈反而比dp更方便 
        # 要用哨兵-1来占位，这样可以方便判断是否
        stack = [-1]

        ans = 0
        for i in range(len(s)):
            if s[i] == '(':
                # 这里放的是i而不是s[i]
                stack.append(i)
            else:
                stack.pop()
                # 判断弹完后是空了还是有一个哨兵
                if not stack:
                    stack.append(i)
                else:
                    # 或者对比栈顶
                    ans = max(ans,i - stack[-1])
        return ans


```
</details>

### 背包dp

<details>
<summary>1. 完全平方数 </summary>

```python
class Solution(object):
    def numSquares(self, n):
        """
        :type n: int
        :rtype: int
        """
        # 完全背包dp
        # 解法和零钱兑换一样
        # dp[i] 表示凑出i这个数字最少要多少个平方数
        
        # 因为是最小值，所以要让每个值初始为最大，因为最后每个数字总能拆成平方和，所以不必担心有inf
        dp = [float('inf')] * (n+1)
        dp[0] = 0

        squares = []
        i = 1
        # 这里如果n本身也是平方数
        while i*i <= n:
            squares.append(i*i)
            i+=1
        
        #历遍到n的每一个数字，让每个数字都尽量是用完全平方数来表示
        # 之所以要用square从小到大，是因为要让小数字先被平方数替代

        for square in squares:
            for i in range(square,n+1):
                dp[i] = min(dp[i],dp[i-square]+1)

        return dp[n]

```
</details>


<details>
<summary>2. 零钱兑换 </summary>

```python
class Solution(object):
    def coinChange(self, coins, amount):
        """
        :type coins: List[int]
        :type amount: int
        :rtype: int
        """
        # 和完全平方数一样
        dp = [float('inf')] * (amount+1)
        dp[0] = 0

        for coin in coins:
            # 判断的时候要从coin开始，不然会出现负下标
            for i in range(coin,amount+1):
                dp[i] = min(dp[i],dp[i-coin]+1)

        # 有可能凑不出来，所以要返回-1，要判断一下结果是不是float('inf')
        if dp[amount] == float('inf'):
            return -1 
        return dp[amount]


```
</details>


<details>
<summary>3. 分割等和子集(⭐️) </summary>

```python
class Solution(object):
    def canPartition(self, nums):
        """
        :type nums: List[int]
        :rtype: bool
        """
        n = len(nums)

        if sum(nums) % 2 ==1 :
            return False

        target = sum(nums) // 2
        # 由于只要看能不能组成target所以数组的长度是target+1
        dp = [False] * (target+1)
        dp[0] = True

        for num in nums:
            # 需要倒着遍历，才能保证每个数字只用一次
            for j in range(target,num-1,-1):
                # 后面就只需要找出一组和为j的数字即可。
                dp[j] = dp[j] or dp[j - num]
        
        return dp[target]

```
</details>

### 前缀划分dp

<details>
<summary>1. 单词拆分 </summary>

```python
class Solution(object):
    def wordBreak(self, s, wordDict):
        """
        :type s: str
        :type wordDict: List[str]
        :rtype: bool
        """
        # 前缀划分dp
        
        wordset = set(wordDict)
        n = len(s)

        dp = [False] *(n+1)
        # dp[i]表示前i个字符能否被切分
        dp[0] = True

        for i in range(1,n+1):
            for j in range(i):
                if dp[j] and s[j:i] in wordset:
                    dp[i] = True
                    break

        return dp[n]
        

```
</details>

### 序列型dp

<details>
<summary>1. 最长递增子序列(⭐️) </summary>

```python
class Solution(object):
    def lengthOfLIS(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        # dp[i] = 以 nums[i] 结尾的最长递增子序列长度
        # 由于要判断每个数字的真实情况，所以不需n+1
        n = len(nums)

        dp = [1] *(n)

        for i in range(n):
            for j in range(i):
                if nums[j]<nums[i]:
                    dp[i] = max(dp[i],dp[j]+1)
        return max(dp)


```
</details>

### 状态构造型dp


<details>
<summary>1. 杨辉三角 </summary>

```python
class Solution(object):
    def generate(self, numRows):
        """
        :type numRows: int
        :rtype: List[List[int]]
        """
        # 一维线性dp    
        triangle = []
        for i in range(numRows):
            row = [1] * (i+1)
            # （1,0）是空区间，不会报错，（1,1）也是空区间
            
            for j in range(1,i):
                row[j] = triangle[i-1][j-1] + triangle[i-1][j]

            triangle.append(row)

        return triangle


```
</details>

### 区间dp

<details>
<summary>1. 最长回文子(⭐️) </summary>

```python
class Solution(object):
    def longestPalindrome(self, s):
        """
        :type s: str
        :rtype: str
        """
        
        # 定义中心延伸
        def expand(left,right):
            while left >= 0 and right <= len(s) - 1 and s[left] == s[right]:
                left-=1 
                right+=1
            return left+1,right-1

        left,right =0,0

        # 遍历数组的每一种情况
        for i in range(len(s)):
            l1, r1 = expand(i,i)
            if r1 - l1 > right -left:
                left,right = l1,r1
            l2 ,r2 = expand(i,i+1)
            if r2-l2 > right-left:
                left,right = l2,r2
        # 切片用:而不是,
        return s[left:right+1]

```
</details>



### 二维路径dp

<details>
<summary>1. 不同路径 </summary>

```python
class Solution(object):
    def uniquePaths(self, m, n):
        """
        :type m: int
        :type n: int
        :rtype: int
        """
        # 到 (i, j) 的路径数 = 到上面那个格子的路径数 + 到左边那个格子的路径数

        # dp[i][j] = dp[i][j-1] + dp[i-1][j]

        # 一般来说是m行n列

        dp = [[0]*n for _ in range(m)]

        dp[0][0] = 1
        for i in range(1,m):
            dp[i][0] = 1

        for j in range(1,n):
            dp[0][j] = 1


        for i in range(1,m):
            for j in range(1,n):
                dp[i][j] = dp[i-1][j] + dp[i][j-1]

        return dp[m-1][n-1]

```
</details>


<details>
<summary>2. 最小路径和 </summary>

```python
class Solution(object):
    def minPathSum(self, grid):
        """
        :type grid: List[List[int]]
        :rtype: int
        """
        m = len(grid)
        n = len(grid[0])
        
        dp = [[0]*n for _ in range(m)]
        dp[0][0] = grid[0][0]
        # 所有的遍历都应该是从1开始而不是0
        for i in range(1,m):
            dp[i][0] = dp[i-1][0] + grid[i][0]

        for j in range(1,n):
            dp[0][j] = dp[0][j-1]+grid[0][j]

        for i in range(1,m):
            for j in range(1,n):
                dp[i][j] = min(dp[i-1][j],dp[i][j-1])+ grid[i][j]
            

        return dp[m-1][n-1]
        



```
</details>

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


