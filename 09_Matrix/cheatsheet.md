# 🔲 Matrix Algorithms - Comprehensive Cheatsheet

## 📚 Core Concepts

**Matrix**: 2D array with rows and columns.

### Common Patterns
1. Traversal (Spiral, Diagonal, Zigzag)
2. Rotation & Transpose
3. Search in Matrix
4. Matrix Modifications
5. Path Problems (DP)
6. Matrix Exponentiation

---

## Pattern 1: Matrix Traversal

### Spiral Order
```python
def spiralOrder(matrix):
    """Traverse matrix in spiral order"""
    if not matrix:
        return []
    
    result = []
    top, bottom = 0, len(matrix) - 1
    left, right = 0, len(matrix[0]) - 1
    
    while top <= bottom and left <= right:
        # Traverse right
        for col in range(left, right + 1):
            result.append(matrix[top][col])
        top += 1
        
        # Traverse down
        for row in range(top, bottom + 1):
            result.append(matrix[row][right])
        right -= 1
        
        # Traverse left (if still valid)
        if top <= bottom:
            for col in range(right, left - 1, -1):
                result.append(matrix[bottom][col])
            bottom -= 1
        
        # Traverse up (if still valid)
        if left <= right:
            for row in range(bottom, top - 1, -1):
                result.append(matrix[row][left])
            left += 1
    
    return result
```

### Visualization
```
Matrix:
1  2  3  4
5  6  7  8
9  10 11 12

Spiral Order:
1 → 2 → 3 → 4
            ↓
5 → 6 → 7   8
↑           ↓
9 → 10→ 11→ 12

Result: [1,2,3,4,8,12,11,10,9,5,6,7]
```

### Diagonal Traversal
```python
def findDiagonalOrder(mat):
    """Traverse diagonals alternating direction"""
    if not mat:
        return []
    
    rows, cols = len(mat), len(mat[0])
    result = []
    row = col = 0
    going_up = True
    
    for _ in range(rows * cols):
        result.append(mat[row][col])
        
        if going_up:
            if col == cols - 1:
                row += 1
                going_up = False
            elif row == 0:
                col += 1
                going_up = False
            else:
                row -= 1
                col += 1
        else:
            if row == rows - 1:
                col += 1
                going_up = True
            elif col == 0:
                row += 1
                going_up = True
            else:
                row += 1
                col -= 1
    
    return result
```

### Zigzag Traversal
```python
def printZigzag(matrix):
    """Print matrix in zigzag pattern"""
    rows, cols = len(matrix), len(matrix[0])
    result = []
    
    for i in range(rows):
        if i % 2 == 0:
            # Left to right
            for j in range(cols):
                result.append(matrix[i][j])
        else:
            # Right to left
            for j in range(cols - 1, -1, -1):
                result.append(matrix[i][j])
    
    return result
```

---

## Pattern 2: Rotation & Transpose

### Rotate 90° Clockwise
```python
def rotate(matrix):
    """Rotate matrix 90° clockwise in-place"""
    n = len(matrix)
    
    # Step 1: Transpose
    for i in range(n):
        for j in range(i + 1, n):
            matrix[i][j], matrix[j][i] = matrix[j][i], matrix[i][j]
    
    # Step 2: Reverse each row
    for i in range(n):
        matrix[i].reverse()
```

### Visualization
```
Original:
1 2 3
4 5 6
7 8 9

After Transpose:
1 4 7
2 5 8
3 6 9

After Reverse Rows (90° clockwise):
7 4 1
8 5 2
9 6 3
```

### Rotate 90° Counter-Clockwise
```python
def rotateCounterClockwise(matrix):
    n = len(matrix)
    
    # Step 1: Transpose
    for i in range(n):
        for j in range(i + 1, n):
            matrix[i][j], matrix[j][i] = matrix[j][i], matrix[i][j]
    
    # Step 2: Reverse each column (reverse matrix vertically)
    for j in range(n):
        for i in range(n // 2):
            matrix[i][j], matrix[n-1-i][j] = matrix[n-1-i][j], matrix[i][j]
```

### Transpose Matrix
```python
def transpose(matrix):
    """Swap rows and columns"""
    rows, cols = len(matrix), len(matrix[0])
    
    # For non-square matrix, create new matrix
    result = [[0] * rows for _ in range(cols)]
    
    for i in range(rows):
        for j in range(cols):
            result[j][i] = matrix[i][j]
    
    return result
```

---

## Pattern 3: Search in Matrix

### Search in Row-Wise & Column-Wise Sorted
```python
def searchMatrix(matrix, target):
    """Search in row and column sorted matrix"""
    if not matrix:
        return False
    
    rows, cols = len(matrix), len(matrix[0])
    
    # Start from top-right corner
    row, col = 0, cols - 1
    
    while row < rows and col >= 0:
        if matrix[row][col] == target:
            return True
        elif matrix[row][col] > target:
            col -= 1  # Move left
        else:
            row += 1  # Move down
    
    return False
```

### Visualization
```
Matrix:
[1,  4,  7,  11, 15]
[2,  5,  8,  12, 19]
[3,  6,  9,  16, 22]
[10, 13, 14, 17, 24]

Search for 5:
Start at (0,4): 15 > 5, move left
       (0,3): 11 > 5, move left
       (0,2): 7 > 5, move left
       (0,1): 4 < 5, move down
       (1,1): 5 = 5, Found! ✓
```

### Binary Search in Sorted Matrix
```python
def searchMatrix_sorted(matrix, target):
    """Search in fully sorted matrix (treat as 1D array)"""
    if not matrix:
        return False
    
    rows, cols = len(matrix), len(matrix[0])
    left, right = 0, rows * cols - 1
    
    while left <= right:
        mid = (left + right) // 2
        # Convert 1D index to 2D
        mid_val = matrix[mid // cols][mid % cols]
        
        if mid_val == target:
            return True
        elif mid_val < target:
            left = mid + 1
        else:
            right = mid - 1
    
    return False
```

---

## Pattern 4: Matrix Modifications

### Set Matrix Zeroes
```python
def setZeroes(matrix):
    """Set entire row and column to 0 if element is 0"""
    rows, cols = len(matrix), len(matrix[0])
    
    # Use first row and column as markers
    first_row_zero = any(matrix[0][j] == 0 for j in range(cols))
    first_col_zero = any(matrix[i][0] == 0 for i in range(rows))
    
    # Mark zeros in first row and column
    for i in range(1, rows):
        for j in range(1, cols):
            if matrix[i][j] == 0:
                matrix[i][0] = 0
                matrix[0][j] = 0
    
    # Set zeros based on markers
    for i in range(1, rows):
        for j in range(1, cols):
            if matrix[i][0] == 0 or matrix[0][j] == 0:
                matrix[i][j] = 0
    
    # Handle first row and column
    if first_row_zero:
        for j in range(cols):
            matrix[0][j] = 0
    
    if first_col_zero:
        for i in range(rows):
            matrix[i][0] = 0
```

### Game of Life
```python
def gameOfLife(board):
    """Conway's Game of Life - in-place update"""
    rows, cols = len(board), len(board[0])
    
    # Encoding: 2 = alive -> dead, 3 = dead -> alive
    
    def count_neighbors(r, c):
        count = 0
        for dr in [-1, 0, 1]:
            for dc in [-1, 0, 1]:
                if dr == 0 and dc == 0:
                    continue
                nr, nc = r + dr, c + dc
                if 0 <= nr < rows and 0 <= nc < cols:
                    if board[nr][nc] in [1, 2]:  # Currently or was alive
                        count += 1
        return count
    
    # First pass: mark state changes
    for i in range(rows):
        for j in range(cols):
            neighbors = count_neighbors(i, j)
            
            if board[i][j] == 1:
                if neighbors < 2 or neighbors > 3:
                    board[i][j] = 2  # Dies
            else:
                if neighbors == 3:
                    board[i][j] = 3  # Becomes alive
    
    # Second pass: update to final state
    for i in range(rows):
        for j in range(cols):
            if board[i][j] == 2:
                board[i][j] = 0
            elif board[i][j] == 3:
                board[i][j] = 1
```

---

## Pattern 5: Path Problems

### Unique Paths
```python
def uniquePaths(m, n):
    """Number of unique paths from top-left to bottom-right"""
    dp = [[1] * n for _ in range(m)]
    
    for i in range(1, m):
        for j in range(1, n):
            dp[i][j] = dp[i-1][j] + dp[i][j-1]
    
    return dp[m-1][n-1]

# Space optimized
def uniquePaths_optimized(m, n):
    dp = [1] * n
    
    for i in range(1, m):
        for j in range(1, n):
            dp[j] += dp[j-1]
    
    return dp[n-1]
```

### Minimum Path Sum
```python
def minPathSum(grid):
    """Minimum sum path from top-left to bottom-right"""
    rows, cols = len(grid), len(grid[0])
    
    # Modify grid in-place
    for i in range(rows):
        for j in range(cols):
            if i == 0 and j == 0:
                continue
            elif i == 0:
                grid[i][j] += grid[i][j-1]
            elif j == 0:
                grid[i][j] += grid[i-1][j]
            else:
                grid[i][j] += min(grid[i-1][j], grid[i][j-1])
    
    return grid[rows-1][cols-1]
```

### Maximal Square
```python
def maximalSquare(matrix):
    """Largest square containing only 1s"""
    if not matrix:
        return 0
    
    rows, cols = len(matrix), len(matrix[0])
    dp = [[0] * cols for _ in range(rows)]
    max_side = 0
    
    for i in range(rows):
        for j in range(cols):
            if matrix[i][j] == '1':
                if i == 0 or j == 0:
                    dp[i][j] = 1
                else:
                    dp[i][j] = min(dp[i-1][j], dp[i][j-1], dp[i-1][j-1]) + 1
                
                max_side = max(max_side, dp[i][j])
    
    return max_side * max_side
```

---

## Pattern 6: Special Matrix Operations

### Toeplitz Matrix
```python
def isToeplitzMatrix(matrix):
    """Check if every diagonal has same elements"""
    rows, cols = len(matrix), len(matrix[0])
    
    for i in range(rows - 1):
        for j in range(cols - 1):
            if matrix[i][j] != matrix[i+1][j+1]:
                return False
    
    return True
```

### Valid Sudoku
```python
def isValidSudoku(board):
    """Check if Sudoku board is valid"""
    rows = [set() for _ in range(9)]
    cols = [set() for _ in range(9)]
    boxes = [set() for _ in range(9)]
    
    for i in range(9):
        for j in range(9):
            if board[i][j] == '.':
                continue
            
            num = board[i][j]
            box_idx = (i // 3) * 3 + j // 3
            
            if num in rows[i] or num in cols[j] or num in boxes[box_idx]:
                return False
            
            rows[i].add(num)
            cols[j].add(num)
            boxes[box_idx].add(num)
    
    return True
```

### Matrix Median
```python
def findMedian(matrix):
    """Find median in row-wise sorted matrix"""
    rows, cols = len(matrix), len(matrix[0])
    
    min_val = min(row[0] for row in matrix)
    max_val = max(row[-1] for row in matrix)
    
    desired = (rows * cols + 1) // 2
    
    while min_val < max_val:
        mid = (min_val + max_val) // 2
        
        # Count elements <= mid
        count = 0
        for row in matrix:
            count += binary_search_count(row, mid)
        
        if count < desired:
            min_val = mid + 1
        else:
            max_val = mid
    
    return min_val

def binary_search_count(row, target):
    """Count elements <= target in sorted row"""
    left, right = 0, len(row)
    
    while left < right:
        mid = (left + right) // 2
        if row[mid] <= target:
            left = mid + 1
        else:
            right = mid
    
    return left
```

---

## Pattern 7: Matrix Exponentiation

### Fibonacci using Matrix
```python
def fibonacci(n):
    """Calculate nth Fibonacci using matrix exponentiation"""
    if n <= 1:
        return n
    
    def matrix_multiply(A, B):
        return [
            [A[0][0]*B[0][0] + A[0][1]*B[1][0], A[0][0]*B[0][1] + A[0][1]*B[1][1]],
            [A[1][0]*B[0][0] + A[1][1]*B[1][0], A[1][0]*B[0][1] + A[1][1]*B[1][1]]
        ]
    
    def matrix_power(M, n):
        if n == 1:
            return M
        
        result = [[1, 0], [0, 1]]  # Identity
        
        while n > 0:
            if n & 1:
                result = matrix_multiply(result, M)
            M = matrix_multiply(M, M)
            n >>= 1
        
        return result
    
    M = [[1, 1], [1, 0]]
    result = matrix_power(M, n - 1)
    
    return result[0][0]
```

---

## 🎨 Dry Run Examples

### Spiral Order
```
Matrix:
1  2  3
4  5  6
7  8  9

Initial: top=0, bottom=2, left=0, right=2

Iteration 1:
- Right: [1,2,3], top=1
- Down: [6,9], right=1
- Left: [8,7], bottom=1
- Up: [4], left=1

Result so far: [1,2,3,6,9,8,7,4]

Iteration 2:
- Right: [5], top=2
- Loop exits (top > bottom)

Final: [1,2,3,6,9,8,7,4,5]
```

---

## ⏱️ Complexity Analysis

| Pattern | Time | Space |
|---------|------|-------|
| Spiral Traversal | O(m*n) | O(1) |
| Rotate 90° | O(n²) | O(1) |
| Search Sorted | O(m+n) | O(1) |
| Set Zeroes | O(m*n) | O(1) |
| Unique Paths | O(m*n) | O(m*n) |
| Matrix Power | O(log n) | O(1) |

---

## 🎯 Must-Know Problems

### Easy
- [54. Spiral Matrix](https://leetcode.com/problems/spiral-matrix/)
- [48. Rotate Image](https://leetcode.com/problems/rotate-image/)
- [766. Toeplitz Matrix](https://leetcode.com/problems/toeplitz-matrix/)

### Medium
- [73. Set Matrix Zeroes](https://leetcode.com/problems/set-matrix-zeroes/)
- [74. Search a 2D Matrix](https://leetcode.com/problems/search-a-2d-matrix/)
- [240. Search a 2D Matrix II](https://leetcode.com/problems/search-a-2d-matrix-ii/)
- [289. Game of Life](https://leetcode.com/problems/game-of-life/)
- [64. Minimum Path Sum](https://leetcode.com/problems/minimum-path-sum/)
- [221. Maximal Square](https://leetcode.com/problems/maximal-square/)

### Hard
- [37. Sudoku Solver](https://leetcode.com/problems/sudoku-solver/)
- [329. Longest Increasing Path in Matrix](https://leetcode.com/problems/longest-increasing-path-in-a-matrix/)

---

## 💡 Pro Tips

1. **Spiral traversal**: Use 4 boundaries (top, bottom, left, right)
2. **Rotation**: Transpose then reverse rows/columns
3. **Search**: Start from corner (top-right or bottom-left)
4. **Space optimization**: Use first row/column as markers
5. **Matrix power**: Use for linear recurrence relations
6. **Direction arrays**: `[(0,1), (1,0), (0,-1), (-1,0)]` for 4 directions

---

**Master matrix operations - they appear frequently in interviews! 🔲**
