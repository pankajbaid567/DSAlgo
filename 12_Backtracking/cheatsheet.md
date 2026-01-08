# 🔄 Backtracking - Comprehensive Cheatsheet

## Core Template

```python
def backtrack(path, choices):
    if is_solution(path):
        result.append(path[:])  # Make a copy!
        return
    
    for choice in choices:
        if is_valid(choice):
            # Make choice
            path.append(choice)
            
            # Recurse
            backtrack(path, get_new_choices(choice))
            
            # Undo choice (backtrack)
            path.pop()
```

---

## Common Patterns

### Pattern 1: Subsets

```python
def subsets(nums):
    result = []
    
    def backtrack(start, path):
        result.append(path[:])
        
        for i in range(start, len(nums)):
            path.append(nums[i])
            backtrack(i + 1, path)
            path.pop()
    
    backtrack(0, [])
    return result

# With duplicates
def subsets_with_dup(nums):
    result = []
    nums.sort()
    
    def backtrack(start, path):
        result.append(path[:])
        
        for i in range(start, len(nums)):
            if i > start and nums[i] == nums[i-1]:
                continue
            path.append(nums[i])
            backtrack(i + 1, path)
            path.pop()
    
    backtrack(0, [])
    return result
```

### Pattern 2: Permutations

```python
def permute(nums):
    result = []
    
    def backtrack(path, remaining):
        if not remaining:
            result.append(path[:])
            return
        
        for i in range(len(remaining)):
            path.append(remaining[i])
            backtrack(path, remaining[:i] + remaining[i+1:])
            path.pop()
    
    backtrack([], nums)
    return result

# With duplicates
def permuteUnique(nums):
    result = []
    nums.sort()
    used = [False] * len(nums)
    
    def backtrack(path):
        if len(path) == len(nums):
            result.append(path[:])
            return
        
        for i in range(len(nums)):
            if used[i]:
                continue
            if i > 0 and nums[i] == nums[i-1] and not used[i-1]:
                continue
            
            used[i] = True
            path.append(nums[i])
            backtrack(path)
            path.pop()
            used[i] = False
    
    backtrack([])
    return result
```

### Pattern 3: Combinations

```python
def combine(n, k):
    result = []
    
    def backtrack(start, path):
        if len(path) == k:
            result.append(path[:])
            return
        
        for i in range(start, n + 1):
            path.append(i)
            backtrack(i + 1, path)
            path.pop()
    
    backtrack(1, [])
    return result

# Combination Sum
def combinationSum(candidates, target):
    result = []
    
    def backtrack(start, path, total):
        if total == target:
            result.append(path[:])
            return
        if total > target:
            return
        
        for i in range(start, len(candidates)):
            path.append(candidates[i])
            backtrack(i, path, total + candidates[i])  # Can reuse
            path.pop()
    
    backtrack(0, [], 0)
    return result
```

### Pattern 4: N-Queens

```python
def solveNQueens(n):
    result = []
    board = [['.'] * n for _ in range(n)]
    
    def is_valid(row, col):
        # Check column
        for i in range(row):
            if board[i][col] == 'Q':
                return False
        
        # Check diagonal
        i, j = row - 1, col - 1
        while i >= 0 and j >= 0:
            if board[i][j] == 'Q':
                return False
            i -= 1
            j -= 1
        
        # Check anti-diagonal
        i, j = row - 1, col + 1
        while i >= 0 and j < n:
            if board[i][j] == 'Q':
                return False
            i -= 1
            j += 1
        
        return True
    
    def backtrack(row):
        if row == n:
            result.append([''.join(row) for row in board])
            return
        
        for col in range(n):
            if is_valid(row, col):
                board[row][col] = 'Q'
                backtrack(row + 1)
                board[row][col] = '.'
    
    backtrack(0)
    return result
```

### Pattern 5: Palindrome Partitioning

```python
def partition(s):
    result = []
    
    def is_palindrome(string):
        return string == string[::-1]
    
    def backtrack(start, path):
        if start == len(s):
            result.append(path[:])
            return
        
        for end in range(start + 1, len(s) + 1):
            substring = s[start:end]
            if is_palindrome(substring):
                path.append(substring)
                backtrack(end, path)
                path.pop()
    
    backtrack(0, [])
    return result
```

### Pattern 6: Word Search

```python
def exist(board, word):
    rows, cols = len(board), len(board[0])
    
    def backtrack(r, c, index):
        if index == len(word):
            return True
        
        if (r < 0 or r >= rows or c < 0 or c >= cols or 
            board[r][c] != word[index]):
            return False
        
        temp = board[r][c]
        board[r][c] = '#'  # Mark visited
        
        found = (backtrack(r+1, c, index+1) or
                 backtrack(r-1, c, index+1) or
                 backtrack(r, c+1, index+1) or
                 backtrack(r, c-1, index+1))
        
        board[r][c] = temp  # Restore
        return found
    
    for r in range(rows):
        for c in range(cols):
            if backtrack(r, c, 0):
                return True
    
    return False
```

### Pattern 7: Sudoku Solver

```python
def solveSudoku(board):
    def is_valid(row, col, num):
        # Check row
        if num in board[row]:
            return False
        
        # Check column
        if num in [board[i][col] for i in range(9)]:
            return False
        
        # Check 3x3 box
        box_row, box_col = 3 * (row // 3), 3 * (col // 3)
        for i in range(box_row, box_row + 3):
            for j in range(box_col, box_col + 3):
                if board[i][j] == num:
                    return False
        
        return True
    
    def backtrack():
        for row in range(9):
            for col in range(9):
                if board[row][col] == '.':
                    for num in '123456789':
                        if is_valid(row, col, num):
                            board[row][col] = num
                            
                            if backtrack():
                                return True
                            
                            board[row][col] = '.'
                    
                    return False
        return True
    
    backtrack()
```

---

## 🎯 Must-Know Problems

### Easy
- [78. Subsets](https://leetcode.com/problems/subsets/)
- [46. Permutations](https://leetcode.com/problems/permutations/)
- [77. Combinations](https://leetcode.com/problems/combinations/)

### Medium
- [39. Combination Sum](https://leetcode.com/problems/combination-sum/)
- [40. Combination Sum II](https://leetcode.com/problems/combination-sum-ii/)
- [90. Subsets II](https://leetcode.com/problems/subsets-ii/)
- [47. Permutations II](https://leetcode.com/problems/permutations-ii/)
- [22. Generate Parentheses](https://leetcode.com/problems/generate-parentheses/)
- [131. Palindrome Partitioning](https://leetcode.com/problems/palindrome-partitioning/)
- [79. Word Search](https://leetcode.com/problems/word-search/)

### Hard
- [51. N-Queens](https://leetcode.com/problems/n-queens/)
- [37. Sudoku Solver](https://leetcode.com/problems/sudoku-solver/)
- [52. N-Queens II](https://leetcode.com/problems/n-queens-ii/)

---

## Pro Tips

1. **Always make a copy** when adding to result: `result.append(path[:])`
2. **Backtrack**: Always undo changes after recursion
3. **Skip duplicates**: Sort first, then check `nums[i] == nums[i-1]`
4. **Prune early**: Check validity before recursing
5. **Mark visited**: Use temp variable or separate visited set

---

**Backtracking explores all possibilities efficiently! 🔄**
