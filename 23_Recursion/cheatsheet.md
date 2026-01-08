# 🔄 Recursion - Comprehensive Cheatsheet

## 📚 Core Concepts

**Recursion**: A function calling itself to solve smaller instances of the same problem.

### Key Components
1. **Base Case**: Termination condition
2. **Recursive Case**: Self-referential call with simpler input
3. **Return Value**: Propagates result back up the call stack

### Recursion vs Iteration
| Aspect | Recursion | Iteration |
|--------|-----------|-----------|
| Space | O(n) stack | O(1) |
| Readability | Often cleaner | Can be complex |
| Performance | Function call overhead | Faster |
| Use Case | Trees, graphs, divide & conquer | Simple loops |

---

## Pattern 1: Basic Recursion

### Factorial
```python
def factorial(n):
    """Calculate n!"""
    # Base case
    if n <= 1:
        return 1
    
    # Recursive case
    return n * factorial(n - 1)

# Dry run: factorial(4)
# factorial(4) = 4 * factorial(3)
# factorial(3) = 3 * factorial(2)
# factorial(2) = 2 * factorial(1)
# factorial(1) = 1
# Result: 4 * 3 * 2 * 1 = 24
```

### Fibonacci
```python
def fibonacci(n):
    """nth Fibonacci number (slow - exponential)"""
    if n <= 1:
        return n
    return fibonacci(n - 1) + fibonacci(n - 2)

# Optimized with memoization
def fibonacci_memo(n, memo={}):
    """O(n) time, O(n) space"""
    if n in memo:
        return memo[n]
    
    if n <= 1:
        return n
    
    memo[n] = fibonacci_memo(n - 1, memo) + fibonacci_memo(n - 2, memo)
    return memo[n]

# Using @lru_cache
from functools import lru_cache

@lru_cache(maxsize=None)
def fibonacci_cached(n):
    if n <= 1:
        return n
    return fibonacci_cached(n - 1) + fibonacci_cached(n - 2)
```

### Power Function
```python
def power(base, exp):
    """Calculate base^exp"""
    if exp == 0:
        return 1
    if exp < 0:
        return 1 / power(base, -exp)
    
    return base * power(base, exp - 1)

# Optimized: O(log n)
def power_fast(base, exp):
    """Fast exponentiation"""
    if exp == 0:
        return 1
    
    half = power_fast(base, exp // 2)
    
    if exp % 2 == 0:
        return half * half
    else:
        return half * half * base

# Example: 2^10
# 2^10 = (2^5)^2
# 2^5 = (2^2)^2 * 2
# 2^2 = (2^1)^2
# 2^1 = 2^0 * 2 = 2
```

---

## Pattern 2: Array/List Recursion

### Sum of Array
```python
def sum_array(arr):
    """Sum all elements recursively"""
    # Base case
    if not arr:
        return 0
    
    # Recursive case
    return arr[0] + sum_array(arr[1:])

# Optimized with index
def sum_array_idx(arr, idx=0):
    if idx >= len(arr):
        return 0
    return arr[idx] + sum_array_idx(arr, idx + 1)
```

### Reverse Array
```python
def reverse_array(arr):
    """Reverse array recursively"""
    if len(arr) <= 1:
        return arr
    
    return [arr[-1]] + reverse_array(arr[:-1])

# In-place with helper
def reverse_helper(arr, left, right):
    if left >= right:
        return
    
    arr[left], arr[right] = arr[right], arr[left]
    reverse_helper(arr, left + 1, right - 1)

def reverse_inplace(arr):
    reverse_helper(arr, 0, len(arr) - 1)
    return arr
```

### Find Maximum
```python
def find_max(arr):
    """Find maximum element"""
    if len(arr) == 1:
        return arr[0]
    
    max_of_rest = find_max(arr[1:])
    return arr[0] if arr[0] > max_of_rest else max_of_rest

# Divide and conquer
def find_max_dc(arr, left, right):
    if left == right:
        return arr[left]
    
    mid = (left + right) // 2
    left_max = find_max_dc(arr, left, mid)
    right_max = find_max_dc(arr, mid + 1, right)
    
    return max(left_max, right_max)
```

### Check Palindrome
```python
def is_palindrome(s):
    """Check if string is palindrome"""
    # Base case
    if len(s) <= 1:
        return True
    
    # Check first and last characters
    if s[0] != s[-1]:
        return False
    
    # Recurse on middle
    return is_palindrome(s[1:-1])

# With indices (more efficient)
def is_palindrome_idx(s, left=0, right=None):
    if right is None:
        right = len(s) - 1
    
    if left >= right:
        return True
    
    if s[left] != s[right]:
        return False
    
    return is_palindrome_idx(s, left + 1, right - 1)
```

---

## Pattern 3: Binary Recursion (Divide & Conquer)

### Binary Search
```python
def binary_search(arr, target, left=0, right=None):
    """Find target in sorted array"""
    if right is None:
        right = len(arr) - 1
    
    # Base case
    if left > right:
        return -1
    
    mid = (left + right) // 2
    
    if arr[mid] == target:
        return mid
    elif arr[mid] > target:
        return binary_search(arr, target, left, mid - 1)
    else:
        return binary_search(arr, target, mid + 1, right)
```

### Merge Sort
```python
def merge_sort(arr):
    """Sort array using merge sort"""
    # Base case
    if len(arr) <= 1:
        return arr
    
    # Divide
    mid = len(arr) // 2
    left = merge_sort(arr[:mid])
    right = merge_sort(arr[mid:])
    
    # Conquer (merge)
    return merge(left, right)

def merge(left, right):
    """Merge two sorted arrays"""
    result = []
    i = j = 0
    
    while i < len(left) and j < len(right):
        if left[i] <= right[j]:
            result.append(left[i])
            i += 1
        else:
            result.append(right[j])
            j += 1
    
    result.extend(left[i:])
    result.extend(right[j:])
    return result
```

### Quick Sort
```python
def quick_sort(arr):
    """Sort array using quick sort"""
    if len(arr) <= 1:
        return arr
    
    pivot = arr[len(arr) // 2]
    left = [x for x in arr if x < pivot]
    middle = [x for x in arr if x == pivot]
    right = [x for x in arr if x > pivot]
    
    return quick_sort(left) + middle + quick_sort(right)

# In-place version
def quick_sort_inplace(arr, low=0, high=None):
    if high is None:
        high = len(arr) - 1
    
    if low < high:
        pivot_idx = partition(arr, low, high)
        quick_sort_inplace(arr, low, pivot_idx - 1)
        quick_sort_inplace(arr, pivot_idx + 1, high)
    
    return arr

def partition(arr, low, high):
    pivot = arr[high]
    i = low - 1
    
    for j in range(low, high):
        if arr[j] <= pivot:
            i += 1
            arr[i], arr[j] = arr[j], arr[i]
    
    arr[i + 1], arr[high] = arr[high], arr[i + 1]
    return i + 1
```

---

## Pattern 4: Tree Recursion

### Binary Tree Traversals
```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

def inorder(root):
    """Left -> Root -> Right"""
    if not root:
        return []
    
    return inorder(root.left) + [root.val] + inorder(root.right)

def preorder(root):
    """Root -> Left -> Right"""
    if not root:
        return []
    
    return [root.val] + preorder(root.left) + preorder(root.right)

def postorder(root):
    """Left -> Right -> Root"""
    if not root:
        return []
    
    return postorder(root.left) + postorder(root.right) + [root.val]
```

### Tree Height
```python
def max_depth(root):
    """Calculate tree height"""
    if not root:
        return 0
    
    left_height = max_depth(root.left)
    right_height = max_depth(root.right)
    
    return 1 + max(left_height, right_height)
```

### Tree Diameter
```python
def diameter_of_binary_tree(root):
    """Find diameter (longest path between any two nodes)"""
    def helper(node):
        if not node:
            return 0, 0  # (height, diameter)
        
        left_height, left_diam = helper(node.left)
        right_height, right_diam = helper(node.right)
        
        current_height = 1 + max(left_height, right_height)
        current_diam = max(left_height + right_height, left_diam, right_diam)
        
        return current_height, current_diam
    
    return helper(root)[1]
```

### Path Sum
```python
def has_path_sum(root, target_sum):
    """Check if root-to-leaf path exists with sum"""
    if not root:
        return False
    
    # Leaf node
    if not root.left and not root.right:
        return root.val == target_sum
    
    # Recurse with reduced sum
    remaining = target_sum - root.val
    return (has_path_sum(root.left, remaining) or 
            has_path_sum(root.right, remaining))
```

---

## Pattern 5: Backtracking

### Generate Subsets
```python
def subsets(nums):
    """Generate all subsets"""
    result = []
    
    def backtrack(start, current):
        # Add current subset
        result.append(current[:])
        
        for i in range(start, len(nums)):
            # Include nums[i]
            current.append(nums[i])
            backtrack(i + 1, current)
            # Backtrack
            current.pop()
    
    backtrack(0, [])
    return result

# Dry run: [1, 2]
# backtrack(0, [])
#   result: [[]]
#   i=0: current=[1]
#     backtrack(1, [1])
#       result: [[], [1]]
#       i=1: current=[1,2]
#         backtrack(2, [1,2])
#           result: [[], [1], [1,2]]
#   i=1: current=[2]
#     backtrack(2, [2])
#       result: [[], [1], [1,2], [2]]
```

### Generate Permutations
```python
def permute(nums):
    """Generate all permutations"""
    result = []
    
    def backtrack(current, remaining):
        if not remaining:
            result.append(current[:])
            return
        
        for i in range(len(remaining)):
            current.append(remaining[i])
            backtrack(current, remaining[:i] + remaining[i+1:])
            current.pop()
    
    backtrack([], nums)
    return result

# Alternative: Using visited set
def permute_visited(nums):
    result = []
    
    def backtrack(current, visited):
        if len(current) == len(nums):
            result.append(current[:])
            return
        
        for i in range(len(nums)):
            if i not in visited:
                current.append(nums[i])
                visited.add(i)
                backtrack(current, visited)
                current.pop()
                visited.remove(i)
    
    backtrack([], set())
    return result
```

### Generate Combinations
```python
def combine(n, k):
    """Generate all k-length combinations from 1 to n"""
    result = []
    
    def backtrack(start, current):
        # Base case
        if len(current) == k:
            result.append(current[:])
            return
        
        for i in range(start, n + 1):
            current.append(i)
            backtrack(i + 1, current)
            current.pop()
    
    backtrack(1, [])
    return result
```

### N-Queens
```python
def solve_n_queens(n):
    """Place n queens on n×n board"""
    result = []
    board = [['.'] * n for _ in range(n)]
    
    def is_valid(row, col):
        # Check column
        for i in range(row):
            if board[i][col] == 'Q':
                return False
        
        # Check diagonal \
        i, j = row - 1, col - 1
        while i >= 0 and j >= 0:
            if board[i][j] == 'Q':
                return False
            i -= 1
            j -= 1
        
        # Check diagonal /
        i, j = row - 1, col + 1
        while i >= 0 and j < n:
            if board[i][j] == 'Q':
                return False
            i -= 1
            j += 1
        
        return True
    
    def backtrack(row):
        if row == n:
            result.append([''.join(r) for r in board])
            return
        
        for col in range(n):
            if is_valid(row, col):
                board[row][col] = 'Q'
                backtrack(row + 1)
                board[row][col] = '.'
    
    backtrack(0)
    return result
```

---

## Pattern 6: String Recursion

### Generate Parentheses
```python
def generate_parentheses(n):
    """Generate all valid n pairs of parentheses"""
    result = []
    
    def backtrack(current, open_count, close_count):
        # Base case
        if len(current) == 2 * n:
            result.append(current)
            return
        
        # Add opening parenthesis
        if open_count < n:
            backtrack(current + '(', open_count + 1, close_count)
        
        # Add closing parenthesis
        if close_count < open_count:
            backtrack(current + ')', open_count, close_count + 1)
    
    backtrack('', 0, 0)
    return result
```

### Letter Case Permutation
```python
def letter_case_permutation(s):
    """Generate all letter case permutations"""
    result = []
    
    def backtrack(idx, current):
        if idx == len(s):
            result.append(''.join(current))
            return
        
        char = s[idx]
        
        if char.isalpha():
            # Lowercase
            current.append(char.lower())
            backtrack(idx + 1, current)
            current.pop()
            
            # Uppercase
            current.append(char.upper())
            backtrack(idx + 1, current)
            current.pop()
        else:
            current.append(char)
            backtrack(idx + 1, current)
            current.pop()
    
    backtrack(0, [])
    return result
```

### Word Search
```python
def exist(board, word):
    """Find if word exists in board"""
    rows, cols = len(board), len(board[0])
    
    def backtrack(r, c, idx):
        # Base case: found word
        if idx == len(word):
            return True
        
        # Out of bounds or mismatch
        if (r < 0 or r >= rows or c < 0 or c >= cols or 
            board[r][c] != word[idx]):
            return False
        
        # Mark visited
        temp = board[r][c]
        board[r][c] = '#'
        
        # Explore 4 directions
        found = (backtrack(r + 1, c, idx + 1) or
                 backtrack(r - 1, c, idx + 1) or
                 backtrack(r, c + 1, idx + 1) or
                 backtrack(r, c - 1, idx + 1))
        
        # Restore cell
        board[r][c] = temp
        
        return found
    
    for r in range(rows):
        for c in range(cols):
            if backtrack(r, c, 0):
                return True
    
    return False
```

---

## Pattern 7: Mathematical Recursion

### GCD (Euclidean Algorithm)
```python
def gcd(a, b):
    """Greatest Common Divisor"""
    if b == 0:
        return a
    return gcd(b, a % b)

# Example: gcd(48, 18)
# gcd(48, 18)
# gcd(18, 12)  # 48 % 18 = 12
# gcd(12, 6)   # 18 % 12 = 6
# gcd(6, 0)    # 12 % 6 = 0
# return 6
```

### Tower of Hanoi
```python
def tower_of_hanoi(n, source, destination, auxiliary):
    """Move n disks from source to destination"""
    if n == 1:
        print(f"Move disk 1 from {source} to {destination}")
        return
    
    # Move n-1 disks to auxiliary
    tower_of_hanoi(n - 1, source, auxiliary, destination)
    
    # Move largest disk to destination
    print(f"Move disk {n} from {source} to {destination}")
    
    # Move n-1 disks from auxiliary to destination
    tower_of_hanoi(n - 1, auxiliary, destination, source)

# Example: tower_of_hanoi(3, 'A', 'C', 'B')
# Move disk 1 from A to C
# Move disk 2 from A to B
# Move disk 1 from C to B
# Move disk 3 from A to C
# Move disk 1 from B to A
# Move disk 2 from B to C
# Move disk 1 from A to C
```

### Count Ways to Climb Stairs
```python
def climb_stairs(n):
    """Count ways to climb n stairs (1 or 2 steps at a time)"""
    if n <= 2:
        return n
    
    return climb_stairs(n - 1) + climb_stairs(n - 2)

# With memoization
@lru_cache(maxsize=None)
def climb_stairs_memo(n):
    if n <= 2:
        return n
    return climb_stairs_memo(n - 1) + climb_stairs_memo(n - 2)
```

---

## 🎨 Recursion Tree Visualization

### Fibonacci(4)
```
                    fib(4)
                   /      \
              fib(3)      fib(2)
             /     \      /    \
        fib(2)   fib(1) fib(1) fib(0)
        /    \
    fib(1)  fib(0)

Result: 1 + 0 + 1 + 1 + 0 = 3
```

### Subsets([1,2])
```
                    []
                   /  \
                 /     \
              [1]       [2]
              /
           [1,2]

Result: [[], [1], [1,2], [2]]
```

---

## ⏱️ Complexity Analysis

| Pattern | Time | Space (Stack) |
|---------|------|---------------|
| Linear Recursion | O(n) | O(n) |
| Binary Recursion | O(2^n) | O(n) |
| Tree Recursion | O(branches^depth) | O(depth) |
| Divide & Conquer | O(n log n) | O(log n) |
| Backtracking | O(2^n) or O(n!) | O(n) |

---

## 🎯 Must-Know Problems

### Easy
- [509. Fibonacci Number](https://leetcode.com/problems/fibonacci-number/)
- [344. Reverse String](https://leetcode.com/problems/reverse-string/)
- [206. Reverse Linked List](https://leetcode.com/problems/reverse-linked-list/)
- [104. Maximum Depth of Binary Tree](https://leetcode.com/problems/maximum-depth-of-binary-tree/)

### Medium
- [78. Subsets](https://leetcode.com/problems/subsets/)
- [46. Permutations](https://leetcode.com/problems/permutations/)
- [77. Combinations](https://leetcode.com/problems/combinations/)
- [22. Generate Parentheses](https://leetcode.com/problems/generate-parentheses/)
- [39. Combination Sum](https://leetcode.com/problems/combination-sum/)
- [79. Word Search](https://leetcode.com/problems/word-search/)

### Hard
- [51. N-Queens](https://leetcode.com/problems/n-queens/)
- [37. Sudoku Solver](https://leetcode.com/problems/sudoku-solver/)
- [212. Word Search II](https://leetcode.com/problems/word-search-ii/)

---

## 💡 Pro Tips

1. **Always define base case first**: Prevents infinite recursion
2. **Make problem smaller**: Each recursive call should work on smaller input
3. **Use memoization**: Cache results to avoid recomputation
4. **Visualize recursion tree**: Helps understand flow
5. **Consider iterative alternative**: Often more efficient
6. **Watch stack overflow**: Deep recursion can exceed stack limit
7. **Tail recursion**: Last operation is recursive call (some languages optimize)

---

## 🚨 Common Mistakes

```python
# ❌ Missing base case
def bad_factorial(n):
    return n * bad_factorial(n - 1)  # Infinite recursion!

# ✅ Correct
def good_factorial(n):
    if n <= 1:
        return 1
    return n * good_factorial(n - 1)

# ❌ Not making problem smaller
def bad_countdown(n):
    print(n)
    return bad_countdown(n)  # Never terminates!

# ✅ Correct
def good_countdown(n):
    if n <= 0:
        return
    print(n)
    return good_countdown(n - 1)

# ❌ Modifying shared state incorrectly
result = []
def bad_subsets(nums, idx):
    if idx == len(nums):
        result.append(current)  # Bug: appends reference!
        return

# ✅ Correct
def good_subsets(nums, idx, current, result):
    if idx == len(nums):
        result.append(current[:])  # Copy the list
        return
```

---

## 🔥 Recursion vs Iteration Trade-offs

```python
# Recursion: Clean but uses stack
def factorial_recursive(n):
    return 1 if n <= 1 else n * factorial_recursive(n - 1)

# Iteration: More efficient
def factorial_iterative(n):
    result = 1
    for i in range(2, n + 1):
        result *= i
    return result

# When to use recursion:
# ✅ Trees and graphs
# ✅ Divide and conquer
# ✅ Backtracking
# ✅ When problem naturally recursive

# When to use iteration:
# ✅ Simple loops
# ✅ Performance critical
# ✅ Large inputs (avoid stack overflow)
```

---

**Master recursion - think recursively, code elegantly! 🔄**
