# 🔥 Dynamic Programming - Hard Problems Collection

## Table of Contents
1. [Edit Distance](#1-edit-distance)
2. [Burst Balloons](#2-burst-balloons)
3. [Regular Expression Matching](#3-regular-expression-matching)
4. [Wildcard Matching](#4-wildcard-matching)
5. [Interleaving String](#5-interleaving-string)
6. [Distinct Subsequences](#6-distinct-subsequences)
7. [Max Rectangle in Binary Matrix](#7-max-rectangle-in-binary-matrix)
8. [Palindrome Partitioning II](#8-palindrome-partitioning-ii)
9. [Word Break II](#9-word-break-ii)
10. [Scramble String](#10-scramble-string)

---

## 1. Edit Distance
**LeetCode**: [#72 - Edit Distance](https://leetcode.com/problems/edit-distance/)

### Problem Statement
Given two strings `word1` and `word2`, return the minimum number of operations required to convert `word1` to `word2`. You can perform: Insert, Delete, Replace.

### Intuition
- This is a classic DP problem on two strings
- At each position, we have 3 choices: insert, delete, or replace
- Build solution bottom-up by considering prefixes of both strings

### Approach
1. Create 2D DP table where `dp[i][j]` = min operations to convert `word1[0:i]` to `word2[0:j]`
2. Base cases: 
   - Empty string to string of length n = n insertions
   - String of length n to empty = n deletions
3. If characters match: `dp[i][j] = dp[i-1][j-1]`
4. If they don't match: `dp[i][j] = 1 + min(insert, delete, replace)`

### Visual Representation
```
word1 = "horse", word2 = "ros"

      ""  r  o  s
  ""  0   1  2  3
  h   1   1  2  3
  o   2   2  1  2
  r   3   2  2  2
  s   4   3  3  2
  e   5   4  4  3

Answer: 3 operations
  horse → rorse (replace h with r)
  rorse → rose (delete r)
  rose → ros (delete e)
```

### Dry Run
```
Step-by-step for "horse" → "ros":

Initial: dp[0][0] = 0
Fill first row: [0, 1, 2, 3] (insertions)
Fill first col: [0, 1, 2, 3, 4, 5] (deletions)

For dp[1][1]: 'h' vs 'r' (not equal)
  - Insert: dp[1][0] + 1 = 2
  - Delete: dp[0][1] + 1 = 2
  - Replace: dp[0][0] + 1 = 1 ✓
  dp[1][1] = 1

For dp[2][2]: 'o' vs 'o' (equal)
  dp[2][2] = dp[1][1] = 1 ✓

Continue this process...
Final answer: dp[5][3] = 3
```

### Code Solution
```python
def minDistance(word1: str, word2: str) -> int:
    m, n = len(word1), len(word2)
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    
    # Base cases
    for i in range(m + 1):
        dp[i][0] = i
    for j in range(n + 1):
        dp[0][j] = j
    
    # Fill the table
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if word1[i-1] == word2[j-1]:
                dp[i][j] = dp[i-1][j-1]
            else:
                dp[i][j] = 1 + min(
                    dp[i-1][j],    # Delete
                    dp[i][j-1],    # Insert
                    dp[i-1][j-1]   # Replace
                )
    
    return dp[m][n]

# Space Optimized: O(n) space
def minDistance_optimized(word1: str, word2: str) -> int:
    m, n = len(word1), len(word2)
    prev = list(range(n + 1))
    
    for i in range(1, m + 1):
        curr = [i] + [0] * n
        for j in range(1, n + 1):
            if word1[i-1] == word2[j-1]:
                curr[j] = prev[j-1]
            else:
                curr[j] = 1 + min(prev[j], curr[j-1], prev[j-1])
        prev = curr
    
    return prev[n]
```

**Time Complexity**: O(m × n)  
**Space Complexity**: O(m × n) → O(n) optimized

---

## 2. Burst Balloons
**LeetCode**: [#312 - Burst Balloons](https://leetcode.com/problems/burst-balloons/)

### Problem Statement
Given n balloons with coins on them. Burst balloons to collect maximum coins. When you burst balloon i, you get `nums[i-1] * nums[i] * nums[i+1]` coins.

### Intuition
- Can't burst in any order - each burst affects neighbors
- Think backwards: which balloon to burst LAST in a range?
- Use MCM (Matrix Chain Multiplication) pattern

### Approach
1. Add 1 at both ends of array (imaginary balloons)
2. For each subarray [left, right], try bursting each balloon k last
3. Coins = nums[left] × nums[k] × nums[right] + dp[left][k] + dp[k][right]

### Visual Representation
```
nums = [3, 1, 5, 8]
After adding boundaries: [1, 3, 1, 5, 8, 1]

Burst order (backwards thinking):
┌───────────────────────────┐
│  [1, 3, 1, 5, 8, 1]       │
│   ↑           ↑    ↑       │
│  left        k    right    │
│                            │
│  Try bursting each k last  │
│  in range [left, right]    │
└───────────────────────────┘
```

### Dry Run
```
nums = [3, 1, 5, 8]
After padding: [1, 3, 1, 5, 8, 1]

Build DP table for gaps of increasing length:

Gap 1: Adjacent balloons
  dp[0][2] = 1*3*1 = 3
  dp[1][3] = 3*1*5 = 15
  dp[2][4] = 1*5*8 = 40
  dp[3][5] = 5*8*1 = 40

Gap 2:
  dp[0][3]: Burst 1 last: 1*3*5 + 0 + 15 = 30
            Burst 2 last: 1*1*5 + 3 + 0 = 8
            Choose: 30

Continue building...
Final: dp[0][5] = 167
```

### Code Solution
```python
def maxCoins(nums: list[int]) -> int:
    # Add boundary balloons
    nums = [1] + nums + [1]
    n = len(nums)
    dp = [[0] * n for _ in range(n)]
    
    # Length of subarray
    for length in range(2, n):
        for left in range(n - length):
            right = left + length
            
            # Try bursting each balloon k last in range [left, right]
            for k in range(left + 1, right):
                coins = nums[left] * nums[k] * nums[right]
                coins += dp[left][k] + dp[k][right]
                dp[left][right] = max(dp[left][right], coins)
    
    return dp[0][n-1]

# Memoization approach
def maxCoins_memo(nums: list[int]) -> int:
    nums = [1] + nums + [1]
    memo = {}
    
    def dp(left, right):
        if left + 1 == right:
            return 0
        
        if (left, right) in memo:
            return memo[(left, right)]
        
        max_coins = 0
        for k in range(left + 1, right):
            coins = nums[left] * nums[k] * nums[right]
            coins += dp(left, k) + dp(k, right)
            max_coins = max(max_coins, coins)
        
        memo[(left, right)] = max_coins
        return max_coins
    
    return dp(0, len(nums) - 1)
```

**Time Complexity**: O(n³)  
**Space Complexity**: O(n²)

---

## 3. Regular Expression Matching
**LeetCode**: [#10 - Regular Expression Matching](https://leetcode.com/problems/regular-expression-matching/)

### Problem Statement
Implement regex matching with '.' (any character) and '*' (zero or more of preceding element).

### Intuition
- Two strings comparison → 2D DP
- '*' can match zero or more of previous character
- Need to handle empty string cases carefully

### Approach
1. `dp[i][j]` = does `s[0:i]` match `p[0:j]`
2. If `p[j] == '*'`: 
   - Match zero occurrences: `dp[i][j-2]`
   - Match one or more: `dp[i-1][j]` if characters match
3. Else match current character and check `dp[i-1][j-1]`

### Visual Representation
```
s = "aab", p = "c*a*b"

      ""  c  *  a  *  b
  ""  T   F  T  F  T  F
  a   F   F  F  T  T  F
  a   F   F  F  F  T  F
  b   F   F  F  F  F  T

Pattern "c*a*b":
  c* → match 0 times
  a* → match 2 'a's
  b  → match 1 'b'
Result: True ✓
```

### Dry Run
```
s = "aa", p = "a*"

Initialize:
dp[0][0] = True (empty matches empty)

For p[1] = '*':
  dp[0][2] = dp[0][0] = True (match 0 'a's)

For s[0] = 'a', p[0] = 'a':
  dp[1][1] = dp[0][0] = True

For s[0] = 'a', p[1] = '*':
  Match 0: dp[1][0] = False
  Match 1+: dp[0][2] = True
  dp[1][2] = True

For s[1] = 'a', p[1] = '*':
  Match 1+: dp[0][2] = True (since s[1] matches 'a')
  dp[2][2] = True

Answer: True
```

### Code Solution
```python
def isMatch(s: str, p: str) -> bool:
    m, n = len(s), len(p)
    dp = [[False] * (n + 1) for _ in range(m + 1)]
    dp[0][0] = True
    
    # Handle patterns like a*, a*b*, etc.
    for j in range(2, n + 1):
        if p[j-1] == '*':
            dp[0][j] = dp[0][j-2]
    
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if p[j-1] == '*':
                # Match zero occurrences
                dp[i][j] = dp[i][j-2]
                
                # Match one or more occurrences
                if p[j-2] == s[i-1] or p[j-2] == '.':
                    dp[i][j] = dp[i][j] or dp[i-1][j]
            else:
                # Current characters match
                if p[j-1] == s[i-1] or p[j-1] == '.':
                    dp[i][j] = dp[i-1][j-1]
    
    return dp[m][n]
```

**Time Complexity**: O(m × n)  
**Space Complexity**: O(m × n)

---

## 4. Wildcard Matching
**LeetCode**: [#44 - Wildcard Matching](https://leetcode.com/problems/wildcard-matching/)

### Problem Statement
Implement wildcard pattern matching with '?' (any single char) and '*' (any sequence).

### Intuition
- Similar to regex but '*' can match any sequence
- Use 2D DP where `dp[i][j]` = does `s[0:i]` match `p[0:j]`

### Approach
1. If `p[j] == '*'`: can match zero or more characters
   - Match zero: `dp[i][j-1]`
   - Match one or more: `dp[i-1][j]`
2. If `p[j] == '?'` or chars match: `dp[i-1][j-1]`

### Visual Representation
```
s = "adceb", p = "*a*b"

      ""  *  a  *  b
  ""  T   T  F  F  F
  a   F   T  T  T  F
  d   F   T  F  T  F
  c   F   T  F  T  F
  e   F   T  F  T  F
  b   F   T  F  T  T

Path: * matches "", a matches a, * matches "dce", b matches b
Result: True ✓
```

### Code Solution
```python
def isMatch(s: str, p: str) -> bool:
    m, n = len(s), len(p)
    dp = [[False] * (n + 1) for _ in range(m + 1)]
    dp[0][0] = True
    
    # Handle leading '*'s
    for j in range(1, n + 1):
        if p[j-1] == '*':
            dp[0][j] = dp[0][j-1]
    
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if p[j-1] == '*':
                # Match zero or one/more characters
                dp[i][j] = dp[i][j-1] or dp[i-1][j]
            elif p[j-1] == '?' or s[i-1] == p[j-1]:
                dp[i][j] = dp[i-1][j-1]
    
    return dp[m][n]

# Space optimized version
def isMatch_optimized(s: str, p: str) -> bool:
    m, n = len(s), len(p)
    prev = [False] * (n + 1)
    prev[0] = True
    
    for j in range(1, n + 1):
        if p[j-1] == '*':
            prev[j] = prev[j-1]
    
    for i in range(1, m + 1):
        curr = [False] * (n + 1)
        for j in range(1, n + 1):
            if p[j-1] == '*':
                curr[j] = curr[j-1] or prev[j]
            elif p[j-1] == '?' or s[i-1] == p[j-1]:
                curr[j] = prev[j-1]
        prev = curr
    
    return prev[n]
```

**Time Complexity**: O(m × n)  
**Space Complexity**: O(n)

---

## 5. Interleaving String
**LeetCode**: [#97 - Interleaving String](https://leetcode.com/problems/interleaving-string/)

### Problem Statement
Given s1, s2, s3, determine if s3 is formed by interleaving s1 and s2.

### Intuition
- At each position in s3, character comes from either s1 or s2
- Use 2D DP where `dp[i][j]` = can we form `s3[0:i+j]` using `s1[0:i]` and `s2[0:j]`

### Visual Representation
```
s1 = "aabcc", s2 = "dbbca", s3 = "aadbbcbcac"

      ""  d  b  b  c  a
  ""  T   F  F  F  F  F
  a   T   F  F  F  F  F
  a   T   T  T  T  T  F
  b   F   T  T  F  T  F
  c   F   F  T  T  T  T
  c   F   F  F  T  F  T

Result: True ✓

One valid interleaving:
a(s1) a(s1) d(s2) b(s2) b(s2) c(s1) b(s2) c(s1) a(s2) c(error)
```

### Code Solution
```python
def isInterleave(s1: str, s2: str, s3: str) -> bool:
    m, n = len(s1), len(s2)
    
    if m + n != len(s3):
        return False
    
    dp = [[False] * (n + 1) for _ in range(m + 1)]
    dp[0][0] = True
    
    # Fill first row (only s2)
    for j in range(1, n + 1):
        dp[0][j] = dp[0][j-1] and s2[j-1] == s3[j-1]
    
    # Fill first column (only s1)
    for i in range(1, m + 1):
        dp[i][0] = dp[i-1][0] and s1[i-1] == s3[i-1]
    
    # Fill rest of table
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            dp[i][j] = (
                (dp[i-1][j] and s1[i-1] == s3[i+j-1]) or
                (dp[i][j-1] and s2[j-1] == s3[i+j-1])
            )
    
    return dp[m][n]
```

**Time Complexity**: O(m × n)  
**Space Complexity**: O(m × n)

---

## 6. Distinct Subsequences
**LeetCode**: [#115 - Distinct Subsequences](https://leetcode.com/problems/distinct-subsequences/)

### Problem Statement
Count number of distinct subsequences of string s that equal string t.

### Intuition
- For each character in t, count ways to match it in s
- If chars match: include it or skip it
- Use 2D DP

### Visual Representation
```
s = "rabbbit", t = "rabbit"

        ""  r  a  b  b  i  t
    ""  1   0  0  0  0  0  0
    r   1   1  0  0  0  0  0
    a   1   1  1  0  0  0  0
    b   1   1  1  1  0  0  0
    b   1   1  1  2  1  0  0
    b   1   1  1  3  3  0  0
    i   1   1  1  3  3  3  0
    t   1   1  1  3  3  3  3

Answer: 3 distinct ways
```

### Code Solution
```python
def numDistinct(s: str, t: str) -> int:
    m, n = len(s), len(t)
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    
    # Empty string can be formed in one way
    for i in range(m + 1):
        dp[i][0] = 1
    
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            # Don't use s[i-1]
            dp[i][j] = dp[i-1][j]
            
            # Use s[i-1] if it matches t[j-1]
            if s[i-1] == t[j-1]:
                dp[i][j] += dp[i-1][j-1]
    
    return dp[m][n]
```

**Time Complexity**: O(m × n)  
**Space Complexity**: O(m × n)

---

## 7. Maximal Rectangle
**LeetCode**: [#85 - Maximal Rectangle](https://leetcode.com/problems/maximal-rectangle/)

### Problem Statement
Find the largest rectangle containing only 1's in a binary matrix.

### Intuition
- Treat each row as base of histogram
- For each row, calculate height of consecutive 1's
- Apply largest rectangle in histogram algorithm

### Visual Representation
```
matrix = [
  ["1","0","1","0","0"],
  ["1","0","1","1","1"],
  ["1","1","1","1","1"],
  ["1","0","0","1","0"]
]

Heights for each row:
Row 0: [1, 0, 1, 0, 0]
Row 1: [2, 0, 2, 1, 1]
Row 2: [3, 1, 3, 2, 2]
Row 3: [4, 0, 0, 3, 0]

Max rectangle: 6 (at row 2)

Visual:
┌─────────┐
│ 1 0 1 0 0│
│ 1 0 █ █ █│  ← Max area = 6
│ 1 █ █ █ █│
│ 1 0 0 1 0│
└─────────┘
```

### Code Solution
```python
def maximalRectangle(matrix: list[list[str]]) -> int:
    if not matrix:
        return 0
    
    m, n = len(matrix), len(matrix[0])
    heights = [0] * n
    max_area = 0
    
    for i in range(m):
        for j in range(n):
            # Update heights
            if matrix[i][j] == '1':
                heights[j] += 1
            else:
                heights[j] = 0
        
        # Calculate max rectangle for this row
        max_area = max(max_area, largestRectangleArea(heights))
    
    return max_area

def largestRectangleArea(heights: list[int]) -> int:
    stack = []
    max_area = 0
    heights.append(0)  # Sentinel
    
    for i, h in enumerate(heights):
        while stack and heights[stack[-1]] > h:
            height = heights[stack.pop()]
            width = i if not stack else i - stack[-1] - 1
            max_area = max(max_area, height * width)
        stack.append(i)
    
    heights.pop()  # Remove sentinel
    return max_area
```

**Time Complexity**: O(m × n)  
**Space Complexity**: O(n)

---

## 8. Palindrome Partitioning II
**LeetCode**: [#132 - Palindrome Partitioning II](https://leetcode.com/problems/palindrome-partitioning-ii/)

### Problem Statement
Find minimum cuts needed to partition string into palindromes.

### Intuition
- Use MCM pattern: try all possible cuts
- Pre-compute palindrome checks for optimization
- `dp[i]` = min cuts for s[0:i]

### Visual Representation
```
s = "aab"

Palindrome table:
    a  a  b
a   T  F  F
a      T  F
b         T

DP array:
i=0: dp[0] = -1 (base)
i=1: "a" is palindrome → dp[1] = 0
i=2: "aa" is palindrome → dp[2] = 0
i=3: "aab" → best is "aa" + "b" → dp[3] = 1

Answer: 1 cut
```

### Code Solution
```python
def minCut(s: str) -> int:
    n = len(s)
    
    # Precompute palindrome check
    is_palindrome = [[False] * n for _ in range(n)]
    for i in range(n):
        is_palindrome[i][i] = True
    
    for length in range(2, n + 1):
        for i in range(n - length + 1):
            j = i + length - 1
            if s[i] == s[j]:
                if length == 2:
                    is_palindrome[i][j] = True
                else:
                    is_palindrome[i][j] = is_palindrome[i+1][j-1]
    
    # DP for minimum cuts
    dp = [float('inf')] * n
    for i in range(n):
        if is_palindrome[0][i]:
            dp[i] = 0
        else:
            for j in range(i):
                if is_palindrome[j+1][i]:
                    dp[i] = min(dp[i], dp[j] + 1)
    
    return dp[n-1]
```

**Time Complexity**: O(n²)  
**Space Complexity**: O(n²)

---

## 9. Word Break II
**LeetCode**: [#140 - Word Break II](https://leetcode.com/problems/word-break-ii/)

### Problem Statement
Given string s and dictionary, return all possible sentences by adding spaces.

### Intuition
- Backtracking with memoization
- At each position, try all possible words from dictionary
- Store results for each starting position

### Code Solution
```python
def wordBreak(s: str, wordDict: list[str]) -> list[str]:
    word_set = set(wordDict)
    memo = {}
    
    def backtrack(start):
        if start in memo:
            return memo[start]
        
        if start == len(s):
            return [""]
        
        result = []
        for end in range(start + 1, len(s) + 1):
            word = s[start:end]
            if word in word_set:
                rest = backtrack(end)
                for sentence in rest:
                    if sentence:
                        result.append(word + " " + sentence)
                    else:
                        result.append(word)
        
        memo[start] = result
        return result
    
    return backtrack(0)
```

**Time Complexity**: O(n × 2^n) worst case  
**Space Complexity**: O(n × 2^n)

---

## 10. Scramble String
**LeetCode**: [#87 - Scramble String](https://leetcode.com/problems/scramble-string/)

### Problem Statement
Given two strings, determine if one is a scrambled version of other using binary tree representation.

### Intuition
- At each level, can either swap or not swap
- Recursively check all possible split points
- Use memoization to avoid recomputation

### Visual Representation
```
s1 = "great", s2 = "rgeat"

Split at position 2:
    gr | eat
    
For "gr" and "rg": swap → True
For "eat" and "eat": no swap → True

Result: True ✓
```

### Code Solution
```python
def isScramble(s1: str, s2: str) -> bool:
    if s1 == s2:
        return True
    
    if sorted(s1) != sorted(s2):
        return False
    
    n = len(s1)
    memo = {}
    
    def helper(s1, s2):
        if (s1, s2) in memo:
            return memo[(s1, s2)]
        
        if s1 == s2:
            return True
        
        if sorted(s1) != sorted(s2):
            return False
        
        n = len(s1)
        for i in range(1, n):
            # No swap
            if helper(s1[:i], s2[:i]) and helper(s1[i:], s2[i:]):
                memo[(s1, s2)] = True
                return True
            
            # Swap
            if helper(s1[:i], s2[n-i:]) and helper(s1[i:], s2[:n-i]):
                memo[(s1, s2)] = True
                return True
        
        memo[(s1, s2)] = False
        return False
    
    return helper(s1, s2)
```

**Time Complexity**: O(n^4)  
**Space Complexity**: O(n^3)

---

## 🎯 Key Takeaways

1. **Edit Distance**: Classic 2-string DP, 3 operations
2. **Burst Balloons**: Think backwards, MCM pattern
3. **Regex/Wildcard**: Handle special characters carefully
4. **Interleaving**: Two sources merging into one
5. **Distinct Subsequences**: Count all possible ways
6. **Maximal Rectangle**: Combine histogram technique with DP
7. **Palindrome Partitioning II**: Precompute palindrome checks
8. **Word Break II**: Backtracking with memoization
9. **Scramble String**: Recursive partitioning with swaps

## 📈 Interview Frequency
- **FAANG**: Very High (especially Edit Distance, Burst Balloons)
- **Startups**: High (focus on practical DP problems)
- **FAANG++**: Must know all patterns thoroughly

---

**Practice Strategy**: Master one problem from each pattern, then solve variations! 🚀
