# 🔥 Matrix - Hard Problems Collection

A comprehensive collection of challenging Matrix problems with complete solutions, DP on grids, and detailed explanations.

---

## 📚 Table of Contents

1. [Dungeon Game](#problem-1-dungeon-game)
2. [Cherry Pickup](#problem-2-cherry-pickup)
3. [Cherry Pickup II](#problem-3-cherry-pickup-ii)
4. [Maximal Rectangle](#problem-4-maximal-rectangle)
5. [Count Square Submatrices](#problem-5-count-square-submatrices-with-all-ones)
6. [Max Sum Rectangle No Larger Than K](#problem-6-max-sum-rectangle-no-larger-than-k)
7. [Number of Submatrices That Sum to Target](#problem-7-number-of-submatrices-that-sum-to-target)
8. [Pattern Summary](#-pattern-summary)

---

## Problem 1: Dungeon Game

**LeetCode 174 - Hard**

### Problem Statement
Find minimum initial health to reach bottom-right corner of dungeon, maintaining health > 0.

```
Input: dungeon = [[-2,-3,3],
                  [-5,-10,1],
                  [10,30,-5]]
Output: 7
Explanation: Start with 7, path: (0,0)→(0,2)→(1,2)→(2,2)
```

### 🎯 Intuition
**Reverse DP approach:**
- Work backwards from destination
- dp[i][j] = minimum health needed at (i,j)
- Health must be ≥ 1 after taking damage/heal

**Key insight:** Can't use forward DP because future cells affect minimum health requirement.

### 📊 Visual Representation

```
dungeon = [[-2, -3,  3],
           [-5,-10,  1],
           [10, 30, -5]]

DP (working backwards):
  dp[2][2] = max(1, 1 - (-5)) = 6
  dp[2][1] = max(1, 6 - 30) = 1
  dp[2][0] = max(1, 6 - 10) = 1
  
  dp[1][2] = max(1, 6 - 1) = 5
  dp[1][1] = max(1, min(5, 6) - (-10)) = 16
  dp[1][0] = max(1, 16 - (-5)) = 21
  
  dp[0][2] = max(1, 5 - 3) = 2
  dp[0][1] = max(1, 2 - (-3)) = 5
  dp[0][0] = max(1, 5 - (-2)) = 7

Answer: 7
```

### Solution

```python
def calculateMinimumHP(dungeon):
    """
    Reverse DP from destination to start.
    
    Logic:
    - dp[i][j] = min health needed at (i,j)
    - Work backwards: health = max(1, next_min - dungeon[i][j])
    - Choose path with lower requirement
    
    Time: O(m*n)
    Space: O(m*n)
    """
    if not dungeon:
        return 1
    
    m, n = len(dungeon), len(dungeon[0])
    dp = [[float('inf')] * (n + 1) for _ in range(m + 1)]
    
    # Base case: need 1 HP to survive at destination
    dp[m][n-1] = dp[m-1][n] = 1
    
    # Fill DP table backwards
    for i in range(m - 1, -1, -1):
        for j in range(n - 1, -1, -1):
            min_next = min(dp[i+1][j], dp[i][j+1])
            dp[i][j] = max(1, min_next - dungeon[i][j])
    
    return dp[0][0]

# Example usage
dungeon = [[-2,-3,3],[-5,-10,1],[10,30,-5]]
print(calculateMinimumHP(dungeon))  # Output: 7
```

### Space Optimized: O(n)

```python
def calculateMinimumHP_optimized(dungeon):
    """
    Use single row for DP.
    
    Time: O(m*n)
    Space: O(n)
    """
    m, n = len(dungeon), len(dungeon[0])
    dp = [float('inf')] * (n + 1)
    dp[n-1] = 1
    
    for i in range(m - 1, -1, -1):
        for j in range(n - 1, -1, -1):
            min_next = min(dp[j], dp[j+1])
            dp[j] = max(1, min_next - dungeon[i][j])
    
    return dp[0]
```

### 🔍 Dry Run

```
dungeon = [[-2,-3,3],
           [-5,-10,1],
           [10,30,-5]]

Bottom-right corner (2,2):
  value = -5
  Need at least 1 after taking -5
  min_hp = max(1, 1 - (-5)) = 6

Cell (2,1):
  value = 30
  Next cell needs 6
  min_hp = max(1, 6 - 30) = 1

Cell (1,2):
  value = 1
  Next cell needs 6
  min_hp = max(1, 6 - 1) = 5

Cell (1,1):
  value = -10
  Can go right (needs 5) or down (needs 1)
  Choose min: 1
  min_hp = max(1, 1 - (-10)) = 11
  
Wait, let me recalculate...

Working correctly from (2,2) backwards:
  dp[2][2] = max(1, 1-(-5)) = 6
  dp[1][2] = max(1, 6-1) = 5
  dp[0][2] = max(1, 5-3) = 2
  dp[2][1] = max(1, 6-30) = 1
  dp[2][0] = max(1, 1-10) = 1? No: max(1, dp[2][1]-10) = max(1, 1-10) = max(1,-9) = 1
  
Actually: dp[2][0] = max(1, min(dp[2][1], dp[3][0]) - dungeon[2][0])
                   = max(1, min(1, inf) - 10)
                   = max(1, 1-10) = 1? No...
                   
Let me use the code logic:
  dp[2][1] = max(1, dp[2][2] - 30) = max(1, 6-30) = 1
  dp[2][0] = max(1, dp[2][1] - 10) = max(1, 1-10) = 1
  
This means at (2,0) with value 10, we need 1 HP.
After taking +10, we have 11 HP.
Move to (2,1), need 1 HP minimum but we have 11.
After taking +30, we have 41 HP.
Move to (2,2), after taking -5, we have 36 HP > 0 ✓
```

### ⏱️ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| 2D DP | O(m*n) | O(m*n) | Clear |
| 1D DP | O(m*n) | O(n) | ⭐ Optimal |

---

## Problem 2: Cherry Pickup

**LeetCode 741 - Hard**

### Problem Statement
Collect maximum cherries going from (0,0) to (n-1,n-1) and back.

```
Input: grid = [[0,1,-1],
               [1,0,-1],
               [1,1,1]]
Output: 5
Explanation: Collect 1+1+1+1+1 = 5 cherries
```

### 🎯 Intuition
**Key insight:** Going down and back = two people going down simultaneously.

**DP approach:**
- State: (r1, c1, r2, c2) or simplified: (r1, c1, r2) since r1+c1 = r2+c2
- Both move simultaneously in same number of steps
- Share cherry if at same cell

### Solution

```python
def cherryPickup(grid):
    """
    DP with two simultaneous paths.
    
    Logic:
    - Treat as 2 people moving from (0,0) to (n-1,n-1)
    - State: (r1, c1, r2) where c2 = r1+c1-r2
    - If same cell, count cherry once
    
    Time: O(n^3)
    Space: O(n^3)
    """
    n = len(grid)
    memo = {}
    
    def dp(r1, c1, r2):
        c2 = r1 + c1 - r2
        
        # Out of bounds or blocked
        if (r1 >= n or c1 >= n or r2 >= n or c2 >= n or
            grid[r1][c1] == -1 or grid[r2][c2] == -1):
            return float('-inf')
        
        # Reached destination
        if r1 == n-1 and c1 == n-1:
            return grid[r1][c1]
        
        if (r1, c1, r2) in memo:
            return memo[(r1, c1, r2)]
        
        # Collect cherries
        cherries = grid[r1][c1]
        if r1 != r2:  # Different cells
            cherries += grid[r2][c2]
        
        # Try all 4 combinations of moves
        max_future = max(
            dp(r1+1, c1, r2+1),  # Both down
            dp(r1, c1+1, r2+1),  # p1 right, p2 down
            dp(r1+1, c1, r2),    # p1 down, p2 right
            dp(r1, c1+1, r2)     # Both right
        )
        
        cherries += max_future
        memo[(r1, c1, r2)] = cherries
        return cherries
    
    result = dp(0, 0, 0)
    return max(0, result)

# Example usage
grid = [[0,1,-1],[1,0,-1],[1,1,1]]
print(cherryPickup(grid))  # Output: 5
```

### ⏱️ Complexity Analysis

| Metric | Complexity | Notes |
|--------|------------|-------|
| Time | O(n³) | 3 dimensions |
| Space | O(n³) | Memoization |

---

## Problem 3: Cherry Pickup II

**LeetCode 1463 - Hard**

### Problem Statement
Two robots collect cherries, both start at top, move to bottom.

```
Input: grid = [[3,1,1],
               [2,5,1],
               [1,5,5],
               [2,1,1]]
Output: 24
```

### Solution

```python
def cherryPickup2(grid):
    """
    DP for two robots moving simultaneously.
    
    Logic:
    - Both robots move down row by row
    - State: (row, col1, col2)
    - Try all 9 move combinations
    
    Time: O(m * n^2 * 9) = O(m*n^2)
    Space: O(m*n^2)
    """
    m, n = len(grid), len(grid[0])
    memo = {}
    
    def dp(row, col1, col2):
        # Base cases
        if col1 < 0 or col1 >= n or col2 < 0 or col2 >= n:
            return float('-inf')
        
        if row == m:
            return 0
        
        if (row, col1, col2) in memo:
            return memo[(row, col1, col2)]
        
        # Collect cherries
        cherries = grid[row][col1]
        if col1 != col2:
            cherries += grid[row][col2]
        
        # Try all 9 move combinations
        max_future = float('-inf')
        for dc1 in [-1, 0, 1]:
            for dc2 in [-1, 0, 1]:
                max_future = max(max_future, dp(row+1, col1+dc1, col2+dc2))
        
        cherries += max_future
        memo[(row, col1, col2)] = cherries
        return cherries
    
    return dp(0, 0, n-1)

# Example usage
grid = [[3,1,1],[2,5,1],[1,5,5],[2,1,1]]
print(cherryPickup2(grid))  # Output: 24
```

### ⏱️ Complexity Analysis

| Metric | Complexity | Notes |
|--------|------------|-------|
| Time | O(m*n²) | 9 constant |
| Space | O(m*n²) | DP states |

---

## Problem 4: Maximal Rectangle

**LeetCode 85 - Hard**

### Problem Statement
Find largest rectangle containing only 1s in binary matrix.

```
Input: matrix = [["1","0","1","0","0"],
                 ["1","0","1","1","1"],
                 ["1","1","1","1","1"],
                 ["1","0","0","1","0"]]
Output: 6
```

### 🎯 Intuition
**Reduce to histogram problem:**
- Treat each row as base of histogram
- Heights = consecutive 1s above
- Apply largest rectangle in histogram for each row

### Solution

```python
def maximalRectangle(matrix):
    """
    Reduce to histogram problem.
    
    Logic:
    - Build height array for each row
    - Apply max rectangle in histogram
    
    Time: O(m*n)
    Space: O(n)
    """
    if not matrix:
        return 0
    
    m, n = len(matrix), len(matrix[0])
    heights = [0] * n
    max_area = 0
    
    def largestRectangle(heights):
        stack = []
        max_area = 0
        heights.append(0)
        
        for i, h in enumerate(heights):
            while stack and heights[stack[-1]] > h:
                height_idx = stack.pop()
                width = i if not stack else i - stack[-1] - 1
                max_area = max(max_area, heights[height_idx] * width)
            stack.append(i)
        
        heights.pop()
        return max_area
    
    for i in range(m):
        for j in range(n):
            if matrix[i][j] == '1':
                heights[j] += 1
            else:
                heights[j] = 0
        
        max_area = max(max_area, largestRectangle(heights[:]))
    
    return max_area

# Example usage
matrix = [["1","0","1","0","0"],
          ["1","0","1","1","1"],
          ["1","1","1","1","1"],
          ["1","0","0","1","0"]]
print(maximalRectangle(matrix))  # Output: 6
```

### ⏱️ Complexity Analysis

| Metric | Complexity | Notes |
|--------|------------|-------|
| Time | O(m*n) | Stack per row |
| Space | O(n) | Heights array |

---

## Problem 5: Count Square Submatrices with All Ones

**LeetCode 1277 - Medium/Hard**

### Problem Statement
Count all square submatrices with all 1s.

```
Input: matrix = [[0,1,1,1],
                 [1,1,1,1],
                 [0,1,1,1]]
Output: 15
```

### Solution

```python
def countSquares(matrix):
    """
    DP for counting squares.
    
    Logic:
    - dp[i][j] = max square size ending at (i,j)
    - If matrix[i][j] = 1:
        dp[i][j] = 1 + min(dp[i-1][j], dp[i][j-1], dp[i-1][j-1])
    - Sum all dp values
    
    Time: O(m*n)
    Space: O(m*n)
    """
    if not matrix:
        return 0
    
    m, n = len(matrix), len(matrix[0])
    dp = [[0] * n for _ in range(m)]
    count = 0
    
    for i in range(m):
        for j in range(n):
            if matrix[i][j] == 1:
                if i == 0 or j == 0:
                    dp[i][j] = 1
                else:
                    dp[i][j] = 1 + min(dp[i-1][j], dp[i][j-1], dp[i-1][j-1])
                count += dp[i][j]
    
    return count

# Example usage
matrix = [[0,1,1,1],[1,1,1,1],[0,1,1,1]]
print(countSquares(matrix))  # Output: 15
```

### ⏱️ Complexity Analysis

| Metric | Complexity | Notes |
|--------|------------|-------|
| Time | O(m*n) | Single pass |
| Space | O(m*n) | DP table |

---

## Problem 6: Max Sum Rectangle No Larger Than K

**LeetCode 363 - Hard**

### Problem Statement
Find max sum of rectangle with sum ≤ k.

```
Input: matrix = [[1,0,1],[0,-2,3]], k = 2
Output: 2
```

### Solution

```python
from bisect import bisect_left, insort

def maxSumSubmatrix(matrix, k):
    """
    Fix columns, apply max subarray with limit.
    
    Logic:
    - Fix left and right columns
    - Compress to 1D array (row sums)
    - Find max subarray sum ≤ k using prefix sums + binary search
    
    Time: O(n^2 * m log m)
    Space: O(m)
    """
    if not matrix:
        return 0
    
    m, n = len(matrix), len(matrix[0])
    max_sum = float('-inf')
    
    for left in range(n):
        row_sums = [0] * m
        
        for right in range(left, n):
            # Add current column to row sums
            for i in range(m):
                row_sums[i] += matrix[i][right]
            
            # Find max subarray sum ≤ k
            prefix_sums = [0]
            curr_sum = 0
            
            for row_sum in row_sums:
                curr_sum += row_sum
                
                # Find smallest prefix_sum such that:
                # curr_sum - prefix_sum ≤ k
                # prefix_sum ≥ curr_sum - k
                target = curr_sum - k
                idx = bisect_left(prefix_sums, target)
                
                if idx < len(prefix_sums):
                    max_sum = max(max_sum, curr_sum - prefix_sums[idx])
                
                insort(prefix_sums, curr_sum)
            
            if max_sum == k:
                return k
    
    return max_sum

# Example usage
matrix = [[1,0,1],[0,-2,3]]
print(maxSumSubmatrix(matrix, 2))  # Output: 2
```

### ⏱️ Complexity Analysis

| Metric | Complexity | Notes |
|--------|------------|-------|
| Time | O(n²*m log m) | Binary search |
| Space | O(m) | Prefix sums |

---

## Problem 7: Number of Submatrices That Sum to Target

**LeetCode 1074 - Hard**

### Problem Statement
Count submatrices with sum equal to target.

```
Input: matrix = [[0,1,0],[1,1,1],[0,1,0]], target = 0
Output: 4
```

### Solution

```python
from collections import defaultdict

def numSubmatrixSumTarget(matrix, target):
    """
    Fix columns, use prefix sum + hashmap.
    
    Logic:
    - Fix left and right columns
    - Compress to 1D
    - Count subarrays with sum = target
    
    Time: O(n^2 * m)
    Space: O(m)
    """
    if not matrix:
        return 0
    
    m, n = len(matrix), len(matrix[0])
    count = 0
    
    for left in range(n):
        row_sums = [0] * m
        
        for right in range(left, n):
            for i in range(m):
                row_sums[i] += matrix[i][right]
            
            # Count subarrays with sum = target
            prefix_count = defaultdict(int)
            prefix_count[0] = 1
            curr_sum = 0
            
            for row_sum in row_sums:
                curr_sum += row_sum
                count += prefix_count[curr_sum - target]
                prefix_count[curr_sum] += 1
    
    return count

# Example usage
matrix = [[0,1,0],[1,1,1],[0,1,0]]
print(numSubmatrixSumTarget(matrix, 0))  # Output: 4
```

### ⏱️ Complexity Analysis

| Metric | Complexity | Notes |
|--------|------------|-------|
| Time | O(n²*m) | Hashmap lookup |
| Space | O(m) | HashMap |

---

## 🎯 Pattern Summary

### Core Matrix Patterns

1. **Reverse DP** - Dungeon Game (work backwards)
2. **Simultaneous Paths** - Cherry Pickup (2 robots)
3. **2D to 1D Reduction** - Maximal Rectangle (histogram)
4. **DP on Grid** - Count Squares (build up)
5. **Column Compression** - Max Sum Rectangle (fix columns)
6. **Prefix Sum + HashMap** - Submatrix Count

### Problem Categories

| Category | Problems | Key Technique |
|----------|----------|---------------|
| Path DP | Dungeon, Cherry | Reverse/simultaneous |
| Rectangle | Maximal, Max Sum | Histogram/compression |
| Counting | Square Submatrices | DP accumulation |
| Sum Queries | Submatrix Sum | Prefix + hashmap |

### When to Use Each Pattern

- **Reverse DP:** Future affects current (health requirements)
- **Simultaneous paths:** Round trip = 2 forward paths
- **Column compression:** Fix columns, reduce to 1D problem
- **DP on grid:** Count or find optimal substructures
- **Prefix sum:** Range queries, subarray sums

### Common Techniques

```python
# 1. Column compression
for left in range(n):
    row_sums = [0] * m
    for right in range(left, n):
        for i in range(m):
            row_sums[i] += matrix[i][right]
        # Now solve 1D problem on row_sums

# 2. DP on grid
dp[i][j] = function(dp[i-1][j], dp[i][j-1], dp[i-1][j-1])

# 3. Reverse DP
for i in range(m-1, -1, -1):
    for j in range(n-1, -1, -1):
        dp[i][j] = function(dp[i+1][j], dp[i][j+1])

# 4. Simultaneous paths
def dp(r1, c1, r2):
    c2 = r1 + c1 - r2  # Same step count
    # Process both positions
```

Master these matrix patterns for interview success! 🚀

