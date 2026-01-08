# 🎯 Dynamic Programming - Comprehensive Cheatsheet

## 📚 Table of Contents
1. [Core Concepts](#core-concepts)
2. [Patterns & Templates](#patterns--templates)
3. [Time & Space Complexity](#time--space-complexity)
4. [Pattern Recognition Guide](#pattern-recognition-guide)
5. [Dry Run Examples](#dry-run-examples)
6. [Advanced Techniques](#advanced-techniques)

---

## Core Concepts

### What is Dynamic Programming?
Dynamic Programming (DP) is an algorithmic paradigm that solves complex problems by breaking them down into simpler subproblems and storing the results to avoid redundant calculations.

**Key Principles:**
- **Overlapping Subproblems**: Same subproblems are solved multiple times
- **Optimal Substructure**: Optimal solution can be constructed from optimal solutions of subproblems

### When to Use DP?
✅ Problem asks for **optimization** (min/max/count)  
✅ Problem can be broken into **overlapping subproblems**  
✅ Problem has **choices** at each step  
✅ Keywords: "maximum", "minimum", "longest", "shortest", "count ways"

---

## Patterns & Templates

### 1️⃣ 0/1 Knapsack Pattern
**Recognition**: Fixed capacity, include/exclude items, maximize/minimize value

```python
# Recursive Template
def knapsack(wt, val, W, n):
    # Base case
    if n == 0 or W == 0:
        return 0
    
    # If weight exceeds capacity, skip
    if wt[n-1] > W:
        return knapsack(wt, val, W, n-1)
    
    # Include or exclude
    include = val[n-1] + knapsack(wt, val, W - wt[n-1], n-1)
    exclude = knapsack(wt, val, W, n-1)
    
    return max(include, exclude)

# Memoization Template
def knapsack_memo(wt, val, W, n, dp):
    if n == 0 or W == 0:
        return 0
    
    if dp[n][W] != -1:
        return dp[n][W]
    
    if wt[n-1] > W:
        dp[n][W] = knapsack_memo(wt, val, W, n-1, dp)
    else:
        include = val[n-1] + knapsack_memo(wt, val, W - wt[n-1], n-1, dp)
        exclude = knapsack_memo(wt, val, W, n-1, dp)
        dp[n][W] = max(include, exclude)
    
    return dp[n][W]

# Bottom-Up Template
def knapsack_dp(wt, val, W, n):
    dp = [[0] * (W + 1) for _ in range(n + 1)]
    
    for i in range(1, n + 1):
        for w in range(1, W + 1):
            if wt[i-1] <= w:
                dp[i][w] = max(
                    val[i-1] + dp[i-1][w - wt[i-1]],  # include
                    dp[i-1][w]                         # exclude
                )
            else:
                dp[i][w] = dp[i-1][w]
    
    return dp[n][W]
```

**Related Problems:**
- Subset Sum
- Equal Sum Partition
- Minimum Subset Sum Difference
- Target Sum
- Count of Subsets with Given Sum

---

### 2️⃣ Unbounded Knapsack Pattern
**Recognition**: Unlimited supply of items, can reuse items

```python
def unbounded_knapsack(wt, val, W, n):
    dp = [[0] * (W + 1) for _ in range(n + 1)]
    
    for i in range(1, n + 1):
        for w in range(1, W + 1):
            if wt[i-1] <= w:
                dp[i][w] = max(
                    val[i-1] + dp[i][w - wt[i-1]],  # include (can reuse)
                    dp[i-1][w]                       # exclude
                )
            else:
                dp[i][w] = dp[i-1][w]
    
    return dp[n][W]
```

**Related Problems:**
- Rod Cutting
- Coin Change (count ways)
- Coin Change (minimum coins)
- Minimum Cost for Tickets

---

### 3️⃣ Longest Common Subsequence (LCS) Pattern
**Recognition**: Two sequences, find common/different elements

```python
def lcs(s1, s2, n, m):
    dp = [[0] * (m + 1) for _ in range(n + 1)]
    
    for i in range(1, n + 1):
        for j in range(1, m + 1):
            if s1[i-1] == s2[j-1]:
                dp[i][j] = 1 + dp[i-1][j-1]
            else:
                dp[i][j] = max(dp[i-1][j], dp[i][j-1])
    
    return dp[n][m]

# Print LCS
def print_lcs(s1, s2, n, m, dp):
    result = []
    i, j = n, m
    
    while i > 0 and j > 0:
        if s1[i-1] == s2[j-1]:
            result.append(s1[i-1])
            i -= 1
            j -= 1
        elif dp[i-1][j] > dp[i][j-1]:
            i -= 1
        else:
            j -= 1
    
    return ''.join(reversed(result))
```

**Related Problems:**
- Longest Common Substring
- Shortest Common Supersequence
- Minimum Insertions/Deletions to Convert String
- Longest Palindromic Subsequence
- Minimum Insertions to Make Palindrome

---

### 4️⃣ Longest Increasing Subsequence (LIS) Pattern
**Recognition**: Find increasing/decreasing subsequence

```python
# O(n²) Solution
def lis(arr):
    n = len(arr)
    dp = [1] * n
    
    for i in range(1, n):
        for j in range(i):
            if arr[j] < arr[i]:
                dp[i] = max(dp[i], dp[j] + 1)
    
    return max(dp)

# O(n log n) Solution using Binary Search
def lis_optimized(arr):
    from bisect import bisect_left
    
    sub = []
    for num in arr:
        pos = bisect_left(sub, num)
        if pos == len(sub):
            sub.append(num)
        else:
            sub[pos] = num
    
    return len(sub)
```

**Related Problems:**
- Longest Bitonic Subsequence
- Maximum Sum Increasing Subsequence
- Number of LIS
- Russian Doll Envelopes
- Box Stacking

---

### 5️⃣ Matrix Chain Multiplication (MCM) Pattern
**Recognition**: Partition problem, try all possible partitions

```python
def mcm(arr, i, j, dp):
    # Base case: single matrix
    if i >= j:
        return 0
    
    if dp[i][j] != -1:
        return dp[i][j]
    
    min_cost = float('inf')
    
    # Try all possible partitions
    for k in range(i, j):
        cost = (mcm(arr, i, k, dp) + 
                mcm(arr, k+1, j, dp) + 
                arr[i-1] * arr[k] * arr[j])
        min_cost = min(min_cost, cost)
    
    dp[i][j] = min_cost
    return min_cost

# Template structure
def mcm_template(arr, i, j):
    # Base condition
    if i >= j:
        return base_value
    
    ans = optimal_value  # min/max based on problem
    
    # Try all partitions
    for k in range(i, j):
        temp = (solve(i, k) + 
                solve(k+1, j) + 
                cost_function(i, k, j))
        ans = optimize(ans, temp)
    
    return ans
```

**Related Problems:**
- Palindrome Partitioning
- Burst Balloons
- Boolean Parenthesization
- Scramble String
- Egg Dropping Problem

---

### 6️⃣ DP on Trees Pattern
**Recognition**: Tree structure, decisions at each node

```python
class TreeNode:
    def __init__(self, val=0):
        self.val = val
        self.left = None
        self.right = None

def dp_on_tree(root):
    if not root:
        return 0
    
    # Include current node
    include = root.val
    if root.left:
        include += dp_on_tree(root.left.left) + dp_on_tree(root.left.right)
    if root.right:
        include += dp_on_tree(root.right.left) + dp_on_tree(root.right.right)
    
    # Exclude current node
    exclude = dp_on_tree(root.left) + dp_on_tree(root.right)
    
    return max(include, exclude)
```

**Related Problems:**
- House Robber III
- Binary Tree Maximum Path Sum
- Diameter of Binary Tree
- Longest Univalue Path

---

### 7️⃣ DP on Grid Pattern
**Recognition**: 2D grid, move from start to end

```python
def grid_dp(grid):
    m, n = len(grid), len(grid[0])
    dp = [[0] * n for _ in range(m)]
    
    # Base case
    dp[0][0] = grid[0][0]
    
    # Fill first row
    for j in range(1, n):
        dp[0][j] = dp[0][j-1] + grid[0][j]
    
    # Fill first column
    for i in range(1, m):
        dp[i][0] = dp[i-1][0] + grid[i][0]
    
    # Fill rest of grid
    for i in range(1, m):
        for j in range(1, n):
            dp[i][j] = grid[i][j] + min(dp[i-1][j], dp[i][j-1])
    
    return dp[m-1][n-1]
```

**Related Problems:**
- Unique Paths
- Unique Paths II
- Minimum Path Sum
- Dungeon Game
- Cherry Pickup

---

### 8️⃣ Kadane's Algorithm Pattern
**Recognition**: Maximum/minimum subarray sum

```python
def kadane(arr):
    max_sum = arr[0]
    current_sum = arr[0]
    
    for i in range(1, len(arr)):
        current_sum = max(arr[i], current_sum + arr[i])
        max_sum = max(max_sum, current_sum)
    
    return max_sum

# 2D Kadane's Algorithm
def kadane_2d(matrix):
    m, n = len(matrix), len(matrix[0])
    max_sum = float('-inf')
    
    for left in range(n):
        temp = [0] * m
        for right in range(left, n):
            for i in range(m):
                temp[i] += matrix[i][right]
            
            max_sum = max(max_sum, kadane(temp))
    
    return max_sum
```

**Related Problems:**
- Maximum Subarray
- Maximum Circular Subarray
- Maximum Product Subarray
- Maximum Sum Rectangle

---

### 9️⃣ Partition DP Pattern
**Recognition**: Split array/string into parts optimally

```python
def partition_dp(arr, k):
    n = len(arr)
    dp = [[float('inf')] * (k + 1) for _ in range(n + 1)]
    dp[0][0] = 0
    
    for i in range(1, n + 1):
        for j in range(1, min(i, k) + 1):
            for p in range(j - 1, i):
                cost = calculate_cost(arr, p, i)
                dp[i][j] = min(dp[i][j], dp[p][j-1] + cost)
    
    return dp[n][k]
```

**Related Problems:**
- Partition Array for Maximum Sum
- Split Array Largest Sum
- Minimum Cost to Cut a Stick

---

### 🔟 Digit DP Pattern
**Recognition**: Count numbers with certain properties

```python
def digit_dp(n):
    s = str(n)
    memo = {}
    
    def dp(pos, tight, started):
        if pos == len(s):
            return 1 if started else 0
        
        if (pos, tight, started) in memo:
            return memo[(pos, tight, started)]
        
        limit = int(s[pos]) if tight else 9
        result = 0
        
        for digit in range(0, limit + 1):
            new_tight = tight and (digit == limit)
            new_started = started or (digit != 0)
            result += dp(pos + 1, new_tight, new_started)
        
        memo[(pos, tight, started)] = result
        return result
    
    return dp(0, True, False)
```

**Related Problems:**
- Numbers with Repeated Digits
- Count Special Integers
- Numbers At Most N Given Digit Set

---

## 🎨 Dry Run Examples

### Example 1: 0/1 Knapsack Dry Run

**Problem**: weights = [1, 3, 4, 5], values = [1, 4, 5, 7], capacity = 7

```
Step-by-step DP Table Construction:

    W:  0   1   2   3   4   5   6   7
i=0     0   0   0   0   0   0   0   0
i=1     0   1   1   1   1   1   1   1   (wt[0]=1, val[0]=1)
i=2     0   1   1   4   5   5   5   5   (wt[1]=3, val[1]=4)
i=3     0   1   1   4   5   6   6   9   (wt[2]=4, val[2]=5)
i=4     0   1   1   4   5   7   8   9   (wt[3]=5, val[3]=7)

Final Answer: dp[4][7] = 9

Items Selected: Item 2 (wt=3, val=4) + Item 3 (wt=4, val=5) = Total wt=7, val=9
```

**Visualization:**
```
┌─────────────────────────────────┐
│  Capacity = 7                   │
│  ┌───┐  ┌───┐  ┌───┐  ┌───┐   │
│  │ 1 │  │ 4 │  │ 5 │  │ 7 │   │ Values
│  └───┘  └───┘  └───┘  └───┘   │
│  wt=1   wt=3   wt=4   wt=5     │
│                                  │
│  Selected: Item2 + Item3        │
│  Total: wt=7, value=9 ✓        │
└─────────────────────────────────┘
```

---

### Example 2: LCS Dry Run

**Problem**: s1 = "AGGTAB", s2 = "GXTXAYB"

```
DP Table Construction:

       ""  G  X  T  X  A  Y  B
    "" 0   0  0  0  0  0  0  0
    A  0   0  0  0  0  1  1  1
    G  0   1  1  1  1  1  1  1
    G  0   1  1  1  1  1  1  1
    T  0   1  1  2  2  2  2  2
    A  0   1  1  2  2  3  3  3
    B  0   1  1  2  2  3  3  4

LCS Length: 4
LCS String: "GTAB"

Backtracking Path:
(6,7) → (5,6) → (4,3) → (2,2) → (0,0)
  B       A       T       G
```

**Visualization:**
```
s1: A G G T A B
s2: G X T X A Y B

LCS:  G   T   A   B
      ↓   ↓   ↓   ↓
s1: A G G T A B
s2: G X T X A Y B
```

---

### Example 3: Matrix Chain Multiplication Dry Run

**Problem**: arr = [40, 20, 30, 10, 30], Find minimum multiplications

```
Matrices: A1(40×20), A2(20×30), A3(30×10), A4(10×30)

DP Table (min operations):

      1    2     3     4
1     0   24000 26000 30000
2          0     6000  10500
3                0     9000
4                       0

Calculation for dp[1][4]:
- k=1: (0 + 6000 + 40×20×30) = 30000
- k=2: (24000 + 9000 + 40×30×30) = 69000
- k=3: (26000 + 0 + 40×10×30) = 38000

Minimum: 30000 operations
Optimal Parenthesization: (A1 × (A2 × A3 × A4))
```

---

## ⏱️ Time & Space Complexity

| Pattern | Time Complexity | Space Complexity |
|---------|----------------|------------------|
| 0/1 Knapsack | O(n × W) | O(n × W) → O(W) |
| Unbounded Knapsack | O(n × W) | O(n × W) → O(W) |
| LCS | O(m × n) | O(m × n) → O(min(m,n)) |
| LIS (DP) | O(n²) | O(n) |
| LIS (Binary Search) | O(n log n) | O(n) |
| MCM | O(n³) | O(n²) |
| Grid DP | O(m × n) | O(m × n) → O(n) |
| Kadane's | O(n) | O(1) |
| Partition DP | O(n² × k) | O(n × k) |
| Digit DP | O(log n × states) | O(log n × states) |

---

## 🎯 Pattern Recognition Guide

### Ask These Questions:

1. **Can the problem be broken into smaller subproblems?**
   → Consider DP

2. **Are the same subproblems solved multiple times?**
   → Use Memoization

3. **Does the problem ask for optimization (min/max/count)?**
   → Likely DP

4. **Are there choices at each step?**
   → Consider 0/1 or Unbounded Knapsack

5. **Does it involve two sequences?**
   → Consider LCS pattern

6. **Does it involve finding subsequence?**
   → Consider LIS pattern

7. **Does it involve partitioning?**
   → Consider MCM or Partition DP

8. **Does it involve a grid?**
   → Consider Grid DP

9. **Does it involve tree structure?**
   → Consider DP on Trees

10. **Does it involve contiguous subarray?**
    → Consider Kadane's

---

## 🚀 Advanced Techniques

### Space Optimization

```python
# From O(n × m) to O(m)
def space_optimized_dp(n, m):
    prev = [0] * m
    curr = [0] * m
    
    for i in range(n):
        for j in range(m):
            curr[j] = calculate(prev, curr, i, j)
        prev, curr = curr, prev
    
    return prev[-1]
```

### State Compression (Bitmask DP)

```python
def bitmask_dp(n):
    dp = [0] * (1 << n)  # 2^n states
    
    for mask in range(1 << n):
        for i in range(n):
            if mask & (1 << i):
                prev_mask = mask ^ (1 << i)
                dp[mask] = max(dp[mask], dp[prev_mask] + cost[i])
    
    return dp[(1 << n) - 1]
```

### DP with Binary Search

```python
from bisect import bisect_left

def dp_with_binary_search(arr):
    n = len(arr)
    dp = [float('inf')] * (n + 1)
    dp[0] = float('-inf')
    
    for num in arr:
        pos = bisect_left(dp, num)
        dp[pos] = num
    
    return max(i for i in range(n + 1) if dp[i] != float('inf')) - 1
```

---

## 📊 Common DP State Definitions

| Problem Type | State Definition | Transition |
|-------------|------------------|------------|
| Knapsack | dp[i][w] = max value using first i items with capacity w | dp[i][w] = max(dp[i-1][w], val[i] + dp[i-1][w-wt[i]]) |
| LCS | dp[i][j] = LCS length of s1[0:i] and s2[0:j] | dp[i][j] = 1 + dp[i-1][j-1] if match else max(dp[i-1][j], dp[i][j-1]) |
| LIS | dp[i] = LIS ending at index i | dp[i] = max(dp[j] + 1) for all j < i where arr[j] < arr[i] |
| Paths | dp[i][j] = ways to reach cell (i,j) | dp[i][j] = dp[i-1][j] + dp[i][j-1] |
| Palindrome | dp[i][j] = is s[i:j+1] palindrome | dp[i][j] = (s[i]==s[j]) and dp[i+1][j-1] |

---

## 🎓 Pro Tips

1. **Start with Recursion**: Write recursive solution first, then optimize
2. **Identify States**: What changes between subproblems?
3. **Base Cases**: Always handle edge cases
4. **Direction**: Bottom-up or Top-down? Choose based on problem
5. **Space Optimization**: After solving, try to reduce space
6. **Print Solutions**: Learn to reconstruct the actual solution, not just the value

---

## 📚 Practice Problems by Difficulty

### Easy
- Climbing Stairs
- House Robber
- Min Cost Climbing Stairs
- Fibonacci Number

### Medium
- Longest Increasing Subsequence
- Coin Change
- Partition Equal Subset Sum
- Decode Ways
- Unique Paths
- Word Break

### Hard
- Edit Distance
- Regular Expression Matching
- Burst Balloons
- Wildcard Matching
- Interleaving String
- Distinct Subsequences

---

## 🔗 Resources

- **LeetCode DP Tag**: Practice 150+ DP problems
- **Aditya Verma's DP Playlist**: YouTube (Highly Recommended)
- **CSES Problem Set**: DP Section
- **AtCoder DP Contest**: 26 classic DP problems

---

**Remember**: Dynamic Programming is all about **recognizing patterns**. Once you master these core patterns, you can solve most DP problems by identifying which pattern fits best! 🚀
