# 🔥 Backtracking - Hard Problems Collection

A curated collection of challenging Backtracking problems with complete solutions, multiple approaches, and detailed explanations.

---

## 📚 Table of Contents

1. [N-Queens II](#problem-1-n-queens-ii)
2. [Sudoku Solver](#problem-2-sudoku-solver)
3. [Expression Add Operators](#problem-3-expression-add-operators)
4. [Remove Invalid Parentheses](#problem-4-remove-invalid-parentheses)
5. [Word Search II](#problem-5-word-search-ii)
6. [Palindrome Partitioning II](#problem-6-palindrome-partitioning-ii)
7. [Regular Expression Matching](#problem-7-regular-expression-matching)
8. [Pattern Summary](#-pattern-summary)

---

## Problem 1: N-Queens II

**LeetCode 52 - Hard**

### Problem Statement
Return the number of distinct solutions to the n-queens puzzle.

```
Input: n = 4
Output: 2
Explanation: Two solutions exist for 4-queens
```

### 🎯 Intuition
Use **backtracking** with constraint checking:
- Place queens row by row
- For each row, try each column
- Check if position is safe (no conflicts)
- Backtrack if conflict found

**Optimization:** Use sets to track occupied columns, diagonals, anti-diagonals in O(1).

### 📊 Visual Representation

```
4x4 board (. = empty, Q = queen):

Solution 1:       Solution 2:
. Q . .           . . Q .
. . . Q           Q . . .
Q . . .           . . . Q
. . Q .           . Q . .

Diagonal tracking:
  Diagonal: row - col
  Anti-diagonal: row + col

Example placement at (2, 1):
  Column: 1
  Diagonal: 2 - 1 = 1
  Anti-diagonal: 2 + 1 = 3
```

### Solution

```python
def totalNQueens(n):
    """
    Backtrack with set-based conflict checking.
    
    Logic:
    - Place queens row by row
    - Track occupied columns and diagonals
    - Use sets for O(1) conflict check
    
    Time: O(n!) - trying all permutations
    Space: O(n) - recursion depth + sets
    """
    def backtrack(row):
        if row == n:
            return 1
        
        count = 0
        for col in range(n):
            diagonal = row - col
            anti_diagonal = row + col
            
            # Check if position is safe
            if (col in columns or 
                diagonal in diagonals or 
                anti_diagonal in anti_diagonals):
                continue
            
            # Place queen
            columns.add(col)
            diagonals.add(diagonal)
            anti_diagonals.add(anti_diagonal)
            
            # Recurse
            count += backtrack(row + 1)
            
            # Backtrack
            columns.remove(col)
            diagonals.remove(diagonal)
            anti_diagonals.remove(anti_diagonal)
        
        return count
    
    columns = set()
    diagonals = set()  # row - col
    anti_diagonals = set()  # row + col
    
    return backtrack(0)

# Example usage
print(totalNQueens(4))  # Output: 2
print(totalNQueens(8))  # Output: 92
```

### Optimized: Bitmasking

```python
def totalNQueens_bitmask(n):
    """
    Use bit manipulation for faster conflict checking.
    
    Time: O(n!)
    Space: O(n)
    """
    def backtrack(row, cols, diags, anti_diags):
        if row == n:
            return 1
        
        # Find available positions
        available = ((1 << n) - 1) & ~(cols | diags | anti_diags)
        
        count = 0
        while available:
            # Get rightmost bit
            position = available & -available
            available -= position
            
            # Place queen and recurse
            count += backtrack(
                row + 1,
                cols | position,
                (diags | position) >> 1,
                (anti_diags | position) << 1
            )
        
        return count
    
    return backtrack(0, 0, 0, 0)
```

### 🔍 Dry Run

```
n = 4

Row 0:
  Try col 0: Place Q at (0,0)
    columns={0}, diags={0}, anti={0}
    
    Row 1:
      Try col 0: conflict (column)
      Try col 1: conflict (anti-diagonal: 1+0=1, 0+0=0? No, 1!=0)
      Try col 2: Safe! Place Q at (1,2)
        columns={0,2}, diags={0,-1}, anti={0,3}
        
        Row 2:
          Try all columns: all have conflicts
          Backtrack
      
      Try col 3: Safe! Place Q at (1,3)
        Continue...
  
  Try col 1: Place Q at (0,1)
    ...

Total solutions found: 2
```

### ⏱️ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Set-based | O(n!) | O(n) | ⭐ Clean code |
| Bitmask | O(n!) | O(n) | Faster in practice |

---

## Problem 2: Sudoku Solver

**LeetCode 37 - Hard**

### Problem Statement
Write program to solve a Sudoku puzzle by filling empty cells.

```
Input: board = 
[["5","3",".",".","7",".",".",".","."],
 ["6",".",".","1","9","5",".",".","."],
 [".","9","8",".",".",".",".","6","."],
 ["8",".",".",".","6",".",".",".","3"],
 ["4",".",".","8",".","3",".",".","1"],
 ["7",".",".",".","2",".",".",".","6"],
 [".","6",".",".",".",".","2","8","."],
 [".",".",".","4","1","9",".",".","5"],
 [".",".",".",".","8",".",".","7","9"]]

Output: Filled board with valid solution
```

### 🎯 Intuition
**Backtracking with constraint propagation:**
1. Find empty cell
2. Try digits 1-9
3. Check if valid (row, column, box)
4. Recurse on next empty cell
5. Backtrack if no solution

**Optimization:** Choose cell with fewest possibilities first.

### 📊 Visual Representation

```
Sudoku constraints:

Row constraint:
  Each row must have 1-9 exactly once

Column constraint:
  Each column must have 1-9 exactly once

Box constraint (3x3):
  Each 3x3 box must have 1-9 exactly once

Box index calculation:
  box_id = (row // 3) * 3 + (col // 3)
  
Example: cell (4,7)
  box_id = (4//3)*3 + (7//3) = 1*3 + 2 = 5
```

### Solution

```python
def solveSudoku(board):
    """
    Backtracking with validity checking.
    
    Logic:
    - Find empty cell
    - Try digits 1-9
    - Check row, column, box constraints
    - Recurse if valid
    - Backtrack if stuck
    
    Time: O(9^m) where m = empty cells
    Space: O(m) recursion depth
    """
    def is_valid(row, col, num):
        """Check if placing num at (row,col) is valid."""
        # Check row
        if num in board[row]:
            return False
        
        # Check column
        if any(board[i][col] == num for i in range(9)):
            return False
        
        # Check 3x3 box
        box_row, box_col = 3 * (row // 3), 3 * (col // 3)
        for i in range(box_row, box_row + 3):
            for j in range(box_col, box_col + 3):
                if board[i][j] == num:
                    return False
        
        return True
    
    def backtrack():
        """Fill board using backtracking."""
        # Find next empty cell
        for i in range(9):
            for j in range(9):
                if board[i][j] == '.':
                    # Try digits 1-9
                    for num in '123456789':
                        if is_valid(i, j, num):
                            board[i][j] = num
                            
                            if backtrack():
                                return True
                            
                            # Backtrack
                            board[i][j] = '.'
                    
                    return False  # No valid digit found
        
        return True  # Board filled successfully
    
    backtrack()

# Example usage
board = [
    ["5","3",".",".","7",".",".",".","."],
    ["6",".",".","1","9","5",".",".","."],
    [".","9","8",".",".",".",".","6","."],
    ["8",".",".",".","6",".",".",".","3"],
    ["4",".",".","8",".","3",".",".","1"],
    ["7",".",".",".","2",".",".",".","6"],
    [".","6",".",".",".",".","2","8","."],
    [".",".",".","4","1","9",".",".","5"],
    [".",".",".",".","8",".",".","7","9"]
]
solveSudoku(board)
```

### Optimized: With Constraint Sets

```python
def solveSudoku_optimized(board):
    """
    Use sets to track available digits.
    
    Time: O(9^m)
    Space: O(1) - fixed space for sets
    """
    # Initialize constraint sets
    rows = [set() for _ in range(9)]
    cols = [set() for _ in range(9)]
    boxes = [set() for _ in range(9)]
    
    # Fill initial constraints
    for i in range(9):
        for j in range(9):
            if board[i][j] != '.':
                num = board[i][j]
                rows[i].add(num)
                cols[j].add(num)
                boxes[(i // 3) * 3 + j // 3].add(num)
    
    def backtrack(pos):
        if pos == 81:  # Filled all cells
            return True
        
        row, col = pos // 9, pos % 9
        
        if board[row][col] != '.':
            return backtrack(pos + 1)
        
        box = (row // 3) * 3 + col // 3
        
        for num in '123456789':
            if num not in rows[row] and num not in cols[col] and num not in boxes[box]:
                # Place digit
                board[row][col] = num
                rows[row].add(num)
                cols[col].add(num)
                boxes[box].add(num)
                
                if backtrack(pos + 1):
                    return True
                
                # Backtrack
                board[row][col] = '.'
                rows[row].remove(num)
                cols[col].remove(num)
                boxes[box].remove(num)
        
        return False
    
    backtrack(0)
```

### ⏱️ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Basic | O(9^m) | O(m) | m = empty cells |
| With Sets | O(9^m) | O(1) | ⭐ Faster checks |

---

## Problem 3: Expression Add Operators

**LeetCode 282 - Hard**

### Problem Statement
Given string of digits and target, insert `+`, `-`, or `*` operators to make expression equal target.

```
Input: num = "123", target = 6
Output: ["1+2+3", "1*2*3"]

Input: num = "232", target = 8
Output: ["2*3+2", "2+3*2"]
```

### 🎯 Intuition
**Backtracking with expression evaluation:**
- Try placing operators after each digit
- Track current value and last operand (for `*` precedence)
- Key: Handle `*` correctly by reversing last operation

**Formula for `*` operation:**
```
current_value = prev_value - last_operand + last_operand * new_operand
```

### 📊 Visual Representation

```
num = "123", target = 6

Decision tree:
              ""
         /    |    \
        1    12    123
       / \   / \
      +  - * ...
     / \   
   1+2 1-2 1*2
  / | \
1+2+3 1+2-3 1+2*3

Tracking for "1+2*3":
  Step 1: "1" → val=1, last=1
  Step 2: "1+2" → val=1+2=3, last=2
  Step 3: "1+2*3":
    Need to undo +2 and apply *:
    val = 3 - 2 + 2*3 = 7
    last = 2*3 = 6
```

### Solution

```python
def addOperators(num, target):
    """
    Backtracking with expression evaluation.
    
    Logic:
    - Try all possible operator placements
    - Track current value and last operand
    - Handle * by reversing last operation
    
    Time: O(4^n) - 4 choices per position
    Space: O(n) - recursion depth
    """
    result = []
    
    def backtrack(index, path, value, last):
        """
        index: current position in num
        path: expression built so far
        value: current evaluated value
        last: last operand (for * handling)
        """
        if index == len(num):
            if value == target:
                result.append(path)
            return
        
        for i in range(index, len(num)):
            # Extract current number
            curr_str = num[index:i+1]
            curr_num = int(curr_str)
            
            # Skip numbers with leading zeros
            if len(curr_str) > 1 and curr_str[0] == '0':
                break
            
            if index == 0:
                # First number, no operator
                backtrack(i + 1, curr_str, curr_num, curr_num)
            else:
                # Try +
                backtrack(i + 1, path + '+' + curr_str, 
                         value + curr_num, curr_num)
                
                # Try -
                backtrack(i + 1, path + '-' + curr_str,
                         value - curr_num, -curr_num)
                
                # Try *
                backtrack(i + 1, path + '*' + curr_str,
                         value - last + last * curr_num,
                         last * curr_num)
    
    backtrack(0, "", 0, 0)
    return result

# Example usage
print(addOperators("123", 6))
# Output: ['1+2+3', '1*2*3']

print(addOperators("232", 8))
# Output: ['2*3+2', '2+3*2']
```

### Alternative: With Memoization

```python
def addOperators_memo(num, target):
    """
    Add memoization (though limited benefit).
    
    Time: O(4^n)
    Space: O(n)
    """
    result = []
    memo = {}
    
    def backtrack(index, path, value, last):
        key = (index, value, last)
        if key in memo:
            return memo[key]
        
        if index == len(num):
            if value == target:
                result.append(path)
            return
        
        for i in range(index, len(num)):
            curr_str = num[index:i+1]
            
            if len(curr_str) > 1 and curr_str[0] == '0':
                break
            
            curr_num = int(curr_str)
            
            if index == 0:
                backtrack(i + 1, curr_str, curr_num, curr_num)
            else:
                backtrack(i + 1, path + '+' + curr_str,
                         value + curr_num, curr_num)
                backtrack(i + 1, path + '-' + curr_str,
                         value - curr_num, -curr_num)
                backtrack(i + 1, path + '*' + curr_str,
                         value - last + last * curr_num,
                         last * curr_num)
        
        memo[key] = True
    
    backtrack(0, "", 0, 0)
    return result
```

### 🔍 Dry Run

```
num = "12", target = 3

backtrack(0, "", 0, 0):
  
  i=0: curr="1", num=1
    First number: backtrack(1, "1", 1, 1)
    
    backtrack(1, "1", 1, 1):
      i=1: curr="2", num=2
        Try +: backtrack(2, "1+2", 3, 2)
          index==len → value==target? 3==3 ✓
          result = ["1+2"]
        
        Try -: backtrack(2, "1-2", -1, -2)
          index==len → value==target? -1==3 ✗
        
        Try *: backtrack(2, "1*2", 2, 2)
          index==len → value==target? 2==3 ✗
  
  i=1: curr="12", num=12
    First number: backtrack(2, "12", 12, 12)
      index==len → value==target? 12==3 ✗

Final result: ["1+2"]
```

### ⏱️ Complexity Analysis

| Metric | Complexity | Notes |
|--------|------------|-------|
| Time | O(4^n * n) | 4 choices, n for string ops |
| Space | O(n) | Recursion depth |

---

## Problem 4: Remove Invalid Parentheses

**LeetCode 301 - Hard**

### Problem Statement
Remove minimum number of invalid parentheses to make string valid. Return all possible results.

```
Input: s = "()())()"
Output: ["(())()", "()()()"]

Input: s = "(a)())()"
Output: ["(a())()", "(a)()()"]
```

### 🎯 Intuition
**BFS approach:**
1. Generate all possible strings by removing one character
2. Check if any are valid
3. If found, those are optimal (minimum removals)
4. If not, continue with next level

**Why BFS?** Ensures minimum removals (shortest distance).

### 📊 Visual Representation

```
s = "()())()"

Level 0: "()())()" - invalid

Level 1 (remove 1 char):
  ")(())()","()()()", "()())(", ... - check each
  No valid strings yet

Level 2 (remove 2 chars):
  Remove indices {2,4}: "(())()"  ✓ valid!
  Remove indices {4,5}: "()()()" ✓ valid!
  ...

Found valid strings at level 2
Answer: ["(())()", "()()()"]
```

### Approach 1: BFS

```python
from collections import deque

def removeInvalidParentheses(s):
    """
    BFS to find minimum removals.
    
    Logic:
    - Generate all strings with 1 char removed
    - Check each for validity
    - First level with valid strings is optimal
    
    Time: O(2^n) worst case
    Space: O(2^n)
    """
    def is_valid(string):
        """Check if parentheses are balanced."""
        count = 0
        for char in string:
            if char == '(':
                count += 1
            elif char == ')':
                count -= 1
                if count < 0:
                    return False
        return count == 0
    
    if is_valid(s):
        return [s]
    
    queue = deque([s])
    visited = {s}
    result = []
    found = False
    
    while queue and not found:
        size = len(queue)
        
        for _ in range(size):
            current = queue.popleft()
            
            # Try removing each character
            for i in range(len(current)):
                if current[i] not in '()':
                    continue
                
                next_str = current[:i] + current[i+1:]
                
                if next_str in visited:
                    continue
                
                if is_valid(next_str):
                    result.append(next_str)
                    found = True
                
                if not found:
                    visited.add(next_str)
                    queue.append(next_str)
    
    return result if result else [""]

# Example usage
print(removeInvalidParentheses("()())()"))
# Output: ['(())()', '()()()']
```

### Approach 2: DFS with Pruning

```python
def removeInvalidParentheses_dfs(s):
    """
    DFS with early pruning.
    
    Time: O(2^n)
    Space: O(n)
    """
    # Count minimum removals needed
    def min_removals():
        left = right = 0
        for char in s:
            if char == '(':
                left += 1
            elif char == ')':
                if left > 0:
                    left -= 1
                else:
                    right += 1
        return left, right
    
    result = []
    min_left, min_right = min_removals()
    
    def dfs(index, left, right, left_rem, right_rem, path):
        """
        index: current position in s
        left, right: current open/close count
        left_rem, right_rem: removals remaining
        path: current string
        """
        if index == len(s):
            if left_rem == 0 and right_rem == 0 and left == right:
                result.append(path)
            return
        
        char = s[index]
        
        # Option 1: Remove current character
        if char == '(' and left_rem > 0:
            dfs(index + 1, left, right, left_rem - 1, right_rem, path)
        elif char == ')' and right_rem > 0:
            dfs(index + 1, left, right, left_rem, right_rem - 1, path)
        
        # Option 2: Keep current character
        if char != '(' and char != ')':
            dfs(index + 1, left, right, left_rem, right_rem, path + char)
        elif char == '(':
            dfs(index + 1, left + 1, right, left_rem, right_rem, path + char)
        elif char == ')' and left > right:
            dfs(index + 1, left, right + 1, left_rem, right_rem, path + char)
    
    dfs(0, 0, 0, min_left, min_right, "")
    return result if result else [""]
```

### ⏱️ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| BFS | O(2^n) | O(2^n) | ⭐ Finds minimum |
| DFS | O(2^n) | O(n) | Less space |

---

## Problem 5: Word Search II

**LeetCode 212 - Hard**

### Problem Statement
Find all words from list that exist in board (can be formed by adjacent cells).

```
Input: board = [["o","a","a","n"],
                ["e","t","a","e"],
                ["i","h","k","r"],
                ["i","f","l","v"]],
       words = ["oath","pea","eat","rain"]
Output: ["eat","oath"]
```

### 🎯 Intuition
Use **Trie + DFS backtracking:**
1. Build trie from words
2. DFS on board
3. Match against trie simultaneously
4. Mark found words

**Why Trie?** Efficient prefix checking and early pruning.

### 📊 Visual Representation

```
Trie for ["oath", "eat"]:
        root
        /  \
       o    e
       |    |
       a    a
       |    |
       t    t*
       |
       h*

Board search from 'o':
  o → a → t → h (found "oath"!)

Pruning: If current path not in trie, stop early
```

### Solution

```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.word = None

def findWords(board, words):
    """
    Trie + DFS for efficient word search.
    
    Logic:
    - Build trie from words
    - DFS on each cell
    - Match against trie
    - Mark cells visited during DFS
    
    Time: O(M*N * 4^L) where L = max word length
    Space: O(W*L) for trie, W = number of words
    """
    # Build trie
    root = TrieNode()
    for word in words:
        node = root
        for char in word:
            if char not in node.children:
                node.children[char] = TrieNode()
            node = node.children[char]
        node.word = word
    
    result = []
    m, n = len(board), len(board[0])
    
    def dfs(r, c, node):
        """DFS with trie matching."""
        char = board[r][c]
        
        if char not in node.children:
            return
        
        next_node = node.children[char]
        
        # Found a word
        if next_node.word:
            result.append(next_node.word)
            next_node.word = None  # Avoid duplicates
        
        # Mark as visited
        board[r][c] = '#'
        
        # Explore neighbors
        for dr, dc in [(0,1), (1,0), (0,-1), (-1,0)]:
            nr, nc = r + dr, c + dc
            if 0 <= nr < m and 0 <= nc < n and board[nr][nc] != '#':
                dfs(nr, nc, next_node)
        
        # Backtrack
        board[r][c] = char
        
        # Optimization: Remove leaf nodes
        if not next_node.children:
            del node.children[char]
    
    # Start DFS from each cell
    for i in range(m):
        for j in range(n):
            if board[i][j] in root.children:
                dfs(i, j, root)
    
    return result

# Example usage
board = [["o","a","a","n"],
         ["e","t","a","e"],
         ["i","h","k","r"],
         ["i","f","l","v"]]
words = ["oath","pea","eat","rain"]
print(findWords(board, words))  # Output: ['eat', 'oath']
```

### ⏱️ Complexity Analysis

| Metric | Complexity | Notes |
|--------|------------|-------|
| Time | O(M*N*4^L) | M*N cells, 4 directions, L depth |
| Space | O(W*L) | Trie size |

---

## Problem 6: Palindrome Partitioning II

**LeetCode 132 - Hard**

### Problem Statement
Given string `s`, partition it such that every substring is a palindrome. Return minimum cuts needed.

```
Input: s = "aab"
Output: 1
Explanation: "aa|b" requires 1 cut
```

### 🎯 Intuition
**DP approach (not pure backtracking):**
- `dp[i]` = minimum cuts for s[0:i+1]
- For each position i, try all possible last partitions
- Use palindrome check table for O(1) lookup

### 📊 Visual Representation

```
s = "aab"

Palindrome table:
    a  a  b
a [[T, F, F],
a  [-, T, F],
b  [-, -, T]]

DP:
  dp[0] = 0 (single char)
  dp[1] = ? 
    "aa" is palindrome → 0 cuts
  dp[2] = ?
    "aab" not palindrome
    "a" + "ab": dp[0] + 1 = 1
    "aa" + "b": dp[1] + 1 = 1
    Min = 1

Answer: 1
```

### Solution

```python
def minCut(s):
    """
    DP with palindrome table.
    
    Logic:
    - Precompute palindrome table
    - dp[i] = min cuts for s[0:i+1]
    - For each i, try all valid partitions
    
    Time: O(n²)
    Space: O(n²)
    """
    n = len(s)
    
    # Build palindrome table
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

# Example usage
print(minCut("aab"))  # Output: 1
print(minCut("ab"))   # Output: 1
print(minCut("aba"))  # Output: 0
```

### Optimized: O(n) Space

```python
def minCut_optimized(s):
    """
    Expand from center for palindrome check.
    
    Time: O(n²)
    Space: O(n)
    """
    n = len(s)
    dp = list(range(n))  # Worst case: i cuts
    
    def expand(left, right):
        """Expand palindrome and update dp."""
        while left >= 0 and right < n and s[left] == s[right]:
            if left == 0:
                dp[right] = 0
            else:
                dp[right] = min(dp[right], dp[left-1] + 1)
            left -= 1
            right += 1
    
    for i in range(n):
        expand(i, i)      # Odd length
        expand(i, i + 1)  # Even length
    
    return dp[n-1]
```

### ⏱️ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| DP Table | O(n²) | O(n²) | Clear logic |
| Expand Center | O(n²) | O(n) | ⭐ Space optimal |

---

## Problem 7: Regular Expression Matching

**LeetCode 10 - Hard**

### Problem Statement
Implement regex matching with `.` and `*`.
- `.` matches any single character
- `*` matches zero or more of preceding element

```
Input: s = "aa", p = "a*"
Output: true

Input: s = "ab", p = ".*"
Output: true
```

### 🎯 Intuition
**DP or Recursion with memoization:**
- If `p[j+1] == '*'`: match 0 or more times
- Otherwise: must match current characters

### Solution

```python
def isMatch(s, p):
    """
    DP for regex matching.
    
    Logic:
    - dp[i][j] = s[0:i] matches p[0:j]
    - Handle . and * cases
    
    Time: O(m*n)
    Space: O(m*n)
    """
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
                # Match 0 times or 1+ times
                dp[i][j] = dp[i][j-2] or \
                          (dp[i-1][j] and (s[i-1] == p[j-2] or p[j-2] == '.'))
            else:
                # Must match current characters
                dp[i][j] = dp[i-1][j-1] and \
                          (s[i-1] == p[j-1] or p[j-1] == '.')
    
    return dp[m][n]
```

### ⏱️ Complexity Analysis

| Metric | Complexity | Notes |
|--------|------------|-------|
| Time | O(m*n) | Fill DP table |
| Space | O(m*n) | DP table |

---

## 🎯 Pattern Summary

### Core Backtracking Patterns

1. **Constraint Satisfaction** - N-Queens, Sudoku
2. **Expression Building** - Add Operators
3. **String Manipulation** - Remove Parentheses
4. **Trie + Backtracking** - Word Search II
5. **DP Optimization** - Palindrome Partitioning
6. **Pattern Matching** - Regex

### Key Techniques

1. **Pruning**: Stop early when constraint violated
2. **Memoization**: Cache repeated subproblems
3. **Trie**: Efficient prefix checking
4. **Bitmask**: Fast state representation

Master these patterns for backtracking success! 🚀

