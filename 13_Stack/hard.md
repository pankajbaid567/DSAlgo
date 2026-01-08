# 🔥 Stack - Hard Problems Collection

A curated collection of challenging Stack problems with complete solutions, multiple approaches, and detailed explanations.

---

## 📚 Table of Contents

1. [Largest Rectangle in Histogram](#problem-1-largest-rectangle-in-histogram)
2. [Basic Calculator III](#problem-2-basic-calculator-iii)
3. [Maximal Rectangle](#problem-3-maximal-rectangle)
4. [Remove K Digits](#problem-4-remove-k-digits)
5. [132 Pattern](#problem-5-132-pattern)
6. [Valid Parenthesis String](#problem-6-valid-parenthesis-string)
7. [Longest Valid Parentheses](#problem-7-longest-valid-parentheses)
8. [Pattern Summary](#-pattern-summary)

---

## Problem 1: Largest Rectangle in Histogram

**LeetCode 84 - Hard**

### Problem Statement
Given an array of integers `heights` representing the histogram's bar height where the width of each bar is 1, return the area of the largest rectangle in the histogram.

```
Input: heights = [2,1,5,6,2,3]
Output: 10
Explanation: Largest rectangle is 5*2 = 10
```

### 🎯 Intuition
The key insight is using a **monotonic increasing stack** to efficiently find the left and right boundaries for each bar where it can extend to form a rectangle.

For each bar:
- **Left boundary**: Previous smaller element
- **Right boundary**: Next smaller element
- **Width**: right - left - 1
- **Area**: height * width

### 📊 Visual Representation

```
Height array: [2,1,5,6,2,3]

Visualization:
       6 ▓
     5 ▓ ▓
       ▓ ▓   3 ▓
 2 ▓   ▓ ▓ 2 ▓ ▓
   ▓ 1 ▓ ▓ ▓ ▓ ▓
Index: 0 1 2 3 4 5

For bar at index 2 (height=5):
- Left boundary: index 1 (height=1 < 5)
- Right boundary: index 4 (height=2 < 5)
- Width: 4 - 1 - 1 = 2
- Area: 5 * 2 = 10

Monotonic stack process:
Stack: [index where heights are increasing]
When current height < stack top:
  - Pop and calculate area
  - Current index is right boundary
  - New stack top is left boundary
```

### Approach 1: Monotonic Stack (Optimal)

```python
def largestRectangleArea(heights):
    """
    Use monotonic increasing stack to track potential rectangles.
    
    Logic:
    - Maintain stack of indices with increasing heights
    - When current < stack top: time to calculate area
    - Pop elements and calculate their maximum rectangle
    
    Time: O(n) - each element pushed/popped once
    Space: O(n) - stack storage
    """
    stack = []  # Stores indices
    max_area = 0
    heights.append(0)  # Sentinel to pop all remaining
    
    for i in range(len(heights)):
        # When current height is smaller, calculate areas
        while stack and heights[i] < heights[stack[-1]]:
            h_index = stack.pop()
            h = heights[h_index]
            
            # Width calculation
            if stack:
                width = i - stack[-1] - 1
            else:
                width = i  # Can extend to beginning
            
            area = h * width
            max_area = max(max_area, area)
        
        stack.append(i)
    
    return max_area

# Example usage
heights = [2,1,5,6,2,3]
print(largestRectangleArea(heights))  # Output: 10
```

### Approach 2: Divide and Conquer

```python
def largestRectangleArea_DC(heights):
    """
    Divide array at minimum, recursively solve left/right.
    Also consider rectangle spanning across minimum.
    
    Time: O(n log n) average, O(n²) worst case
    Space: O(n) recursion stack
    """
    def helper(left, right):
        if left > right:
            return 0
        
        # Find minimum height in range
        min_idx = left
        for i in range(left, right + 1):
            if heights[i] < heights[min_idx]:
                min_idx = i
        
        # Three candidates:
        # 1. Rectangle using min_height spanning whole range
        curr_area = heights[min_idx] * (right - left + 1)
        
        # 2. Maximum in left subarray
        left_area = helper(left, min_idx - 1)
        
        # 3. Maximum in right subarray
        right_area = helper(min_idx + 1, right)
        
        return max(curr_area, left_area, right_area)
    
    return helper(0, len(heights) - 1)
```

### Approach 3: Left-Right Arrays

```python
def largestRectangleArea_LR(heights):
    """
    Precompute left and right boundaries for each bar.
    
    Time: O(n)
    Space: O(n)
    """
    n = len(heights)
    left = [0] * n   # left[i] = index of previous smaller
    right = [0] * n  # right[i] = index of next smaller
    
    # Calculate left boundaries
    stack = []
    for i in range(n):
        while stack and heights[stack[-1]] >= heights[i]:
            stack.pop()
        left[i] = stack[-1] if stack else -1
        stack.append(i)
    
    # Calculate right boundaries
    stack = []
    for i in range(n - 1, -1, -1):
        while stack and heights[stack[-1]] >= heights[i]:
            stack.pop()
        right[i] = stack[-1] if stack else n
        stack.append(i)
    
    # Calculate maximum area
    max_area = 0
    for i in range(n):
        width = right[i] - left[i] - 1
        area = heights[i] * width
        max_area = max(max_area, area)
    
    return max_area
```

### 🔍 Dry Run

```
heights = [2, 1, 5, 6, 2, 3]

Using Monotonic Stack:

i=0, h=2:
  stack=[], push 0
  stack=[0]

i=1, h=1:
  stack=[0], heights[0]=2 > 1
    pop 0: h=2, width=1, area=2
    max_area=2
  push 1
  stack=[1]

i=2, h=5:
  stack=[1], heights[1]=1 < 5
  push 2
  stack=[1,2]

i=3, h=6:
  stack=[1,2], heights[2]=5 < 6
  push 3
  stack=[1,2,3]

i=4, h=2:
  stack=[1,2,3], heights[3]=6 > 2
    pop 3: h=6, width=4-2-1=1, area=6
    max_area=6
  heights[2]=5 > 2
    pop 2: h=5, width=4-1-1=2, area=10
    max_area=10
  heights[1]=1 < 2
  push 4
  stack=[1,4]

i=5, h=3:
  stack=[1,4], heights[4]=2 < 3
  push 5
  stack=[1,4,5]

i=6, h=0 (sentinel):
  Pop all and calculate areas
  
Final max_area = 10
```

### ⏱️ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Monotonic Stack | O(n) | O(n) | ⭐ Optimal |
| Divide & Conquer | O(n log n) | O(n) | Average case |
| Left-Right Arrays | O(n) | O(n) | Three passes |

---

## Problem 2: Basic Calculator III

**LeetCode 772 - Hard**

### Problem Statement
Implement a basic calculator to evaluate a string expression with `+`, `-`, `*`, `/`, and parentheses.

```
Input: s = "2*(5+5*2)/3+(6/2+8)"
Output: 21
```

### 🎯 Intuition
Use **two stacks**:
1. **Number stack**: Stores intermediate results
2. **Operator stack**: Stores pending operators

Key rules:
- Higher precedence operators (`*`, `/`) execute immediately
- Lower precedence operators (`+`, `-`) wait
- Parentheses force new evaluation context

### 📊 Visual Representation

```
Expression: "2*(5+5*2)/3"

Stack Evolution:
                                    
Step 1: "2"        nums=[2]         ops=[]
Step 2: "*"        nums=[2]         ops=['*']
Step 3: "("        nums=[2]         ops=['*','(']
Step 4: "5"        nums=[2,5]       ops=['*','(']
Step 5: "+"        nums=[2,5]       ops=['*','(','+']
Step 6: "5"        nums=[2,5,5]     ops=['*','(','+']
Step 7: "*"        Execute +        
                   nums=[2,10]      ops=['*','(','*']
Step 8: "2"        nums=[2,10,2]    ops=['*','(','*']
Step 9: ")"        Execute *: 10*2=20
                   Execute (): pop '('
                   nums=[2,20]      ops=['*']
Step 10: "/"       Execute *: 2*20=40
                   nums=[40]        ops=['/']
Step 11: "3"       nums=[40,3]      ops=['/']
Final:             Execute /: 40/3=13
```

### Solution

```python
def calculate(s):
    """
    Evaluate expression with +, -, *, /, and parentheses.
    
    Logic:
    - Use two stacks for numbers and operators
    - Handle precedence: * / > + -
    - Handle parentheses by recursion or stack tracking
    
    Time: O(n)
    Space: O(n)
    """
    def precedence(op):
        if op in '+-':
            return 1
        if op in '*/':
            return 2
        return 0
    
    def apply_op(a, b, op):
        if op == '+': return a + b
        if op == '-': return a - b
        if op == '*': return a * b
        if op == '/': return int(a / b)  # Truncate toward zero
    
    nums = []
    ops = []
    i = 0
    n = len(s)
    
    while i < n:
        if s[i] == ' ':
            i += 1
            continue
        
        # Handle number
        if s[i].isdigit():
            num = 0
            while i < n and s[i].isdigit():
                num = num * 10 + int(s[i])
                i += 1
            nums.append(num)
            continue
        
        # Handle opening parenthesis
        if s[i] == '(':
            ops.append(s[i])
        
        # Handle closing parenthesis
        elif s[i] == ')':
            # Execute all ops until matching '('
            while ops and ops[-1] != '(':
                b = nums.pop()
                a = nums.pop()
                op = ops.pop()
                nums.append(apply_op(a, b, op))
            ops.pop()  # Remove '('
        
        # Handle operator
        else:
            # Execute higher/equal precedence ops first
            while (ops and ops[-1] != '(' and
                   precedence(ops[-1]) >= precedence(s[i])):
                b = nums.pop()
                a = nums.pop()
                op = ops.pop()
                nums.append(apply_op(a, b, op))
            ops.append(s[i])
        
        i += 1
    
    # Execute remaining ops
    while ops:
        b = nums.pop()
        a = nums.pop()
        op = ops.pop()
        nums.append(apply_op(a, b, op))
    
    return nums[0]

# Example usage
s = "2*(5+5*2)/3+(6/2+8)"
print(calculate(s))  # Output: 21
```

### Alternative: Recursive Descent Parser

```python
def calculate_recursive(s):
    """
    Use recursive descent parsing.
    
    Grammar:
    expression := term (('+' | '-') term)*
    term       := factor (('*' | '/') factor)*
    factor     := number | '(' expression ')'
    """
    def parse_expression(idx):
        left, idx = parse_term(idx)
        
        while idx < len(s) and s[idx] in '+-':
            op = s[idx]
            idx += 1
            right, idx = parse_term(idx)
            if op == '+':
                left += right
            else:
                left -= right
        
        return left, idx
    
    def parse_term(idx):
        left, idx = parse_factor(idx)
        
        while idx < len(s) and s[idx] in '*/':
            op = s[idx]
            idx += 1
            right, idx = parse_factor(idx)
            if op == '*':
                left *= right
            else:
                left = int(left / right)
        
        return left, idx
    
    def parse_factor(idx):
        # Skip spaces
        while idx < len(s) and s[idx] == ' ':
            idx += 1
        
        # Handle parentheses
        if s[idx] == '(':
            idx += 1  # Skip '('
            result, idx = parse_expression(idx)
            idx += 1  # Skip ')'
            return result, idx
        
        # Handle number
        num = 0
        while idx < len(s) and s[idx].isdigit():
            num = num * 10 + int(s[idx])
            idx += 1
        
        return num, idx
    
    s = s.replace(' ', '')  # Remove all spaces
    result, _ = parse_expression(0)
    return result
```

### ⏱️ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Two Stacks | O(n) | O(n) | ⭐ Iterative |
| Recursive | O(n) | O(n) | Elegant |

---

## Problem 3: Maximal Rectangle

**LeetCode 85 - Hard**

### Problem Statement
Given a binary matrix filled with 0's and 1's, find the largest rectangle containing only 1's and return its area.

```
Input: matrix = [
  ["1","0","1","0","0"],
  ["1","0","1","1","1"],
  ["1","1","1","1","1"],
  ["1","0","0","1","0"]
]
Output: 6
Explanation: Rectangle from (1,2) to (2,4) has area 6
```

### 🎯 Intuition
Convert 2D problem to **multiple 1D histogram problems**!

For each row:
- Build histogram where height = consecutive 1's above
- Apply "Largest Rectangle in Histogram" algorithm
- Track maximum across all rows

### 📊 Visual Representation

```
Original Matrix:
["1","0","1","0","0"]
["1","0","1","1","1"]
["1","1","1","1","1"]
["1","0","0","1","0"]

Build Histograms Row by Row:

After row 0: [1, 0, 1, 0, 0]
    1   1
    ▓   ▓

After row 1: [2, 0, 2, 1, 1]
    2   2
    ▓   ▓ 1 1
    ▓   ▓ ▓ ▓

After row 2: [3, 1, 3, 2, 2]
    3   3
    ▓   ▓ 2 2
    ▓ 1 ▓ ▓ ▓
    ▓ ▓ ▓ ▓ ▓

After row 3: [4, 0, 0, 3, 0]
    4     3
    ▓     ▓
    ▓     ▓
    ▓     ▓
    ▓     ▓

For row 2 histogram [3,1,3,2,2]:
  Maximum rectangle = min(3,1,3,2,2) * 5 
                    or min(3,2,2) * 3 = 6
```

### Solution

```python
def maximalRectangle(matrix):
    """
    Convert 2D to multiple 1D histogram problems.
    
    Logic:
    - For each row, build histogram of heights
    - Apply largest rectangle in histogram
    - Track maximum area
    
    Time: O(m * n)
    Space: O(n)
    """
    if not matrix or not matrix[0]:
        return 0
    
    def largestRectangle(heights):
        """Helper: Largest rectangle in histogram."""
        stack = []
        max_area = 0
        heights.append(0)
        
        for i in range(len(heights)):
            while stack and heights[i] < heights[stack[-1]]:
                h_idx = stack.pop()
                h = heights[h_idx]
                width = i if not stack else i - stack[-1] - 1
                max_area = max(max_area, h * width)
            stack.append(i)
        
        heights.pop()
        return max_area
    
    rows, cols = len(matrix), len(matrix[0])
    heights = [0] * cols
    max_area = 0
    
    for r in range(rows):
        for c in range(cols):
            # Build histogram for current row
            if matrix[r][c] == '1':
                heights[c] += 1
            else:
                heights[c] = 0
        
        # Find largest rectangle in current histogram
        area = largestRectangle(heights[:])
        max_area = max(max_area, area)
    
    return max_area

# Example usage
matrix = [
    ["1","0","1","0","0"],
    ["1","0","1","1","1"],
    ["1","1","1","1","1"],
    ["1","0","0","1","0"]
]
print(maximalRectangle(matrix))  # Output: 6
```

### Alternative: DP Approach

```python
def maximalRectangle_DP(matrix):
    """
    Use DP to track height, left, right boundaries.
    
    Time: O(m * n)
    Space: O(n)
    """
    if not matrix or not matrix[0]:
        return 0
    
    rows, cols = len(matrix), len(matrix[0])
    height = [0] * cols
    left = [0] * cols
    right = [cols] * cols
    max_area = 0
    
    for r in range(rows):
        curr_left, curr_right = 0, cols
        
        # Update height
        for c in range(cols):
            if matrix[r][c] == '1':
                height[c] += 1
            else:
                height[c] = 0
        
        # Update left boundary
        for c in range(cols):
            if matrix[r][c] == '1':
                left[c] = max(left[c], curr_left)
            else:
                left[c] = 0
                curr_left = c + 1
        
        # Update right boundary
        for c in range(cols - 1, -1, -1):
            if matrix[r][c] == '1':
                right[c] = min(right[c], curr_right)
            else:
                right[c] = cols
                curr_right = c
        
        # Calculate area
        for c in range(cols):
            area = height[c] * (right[c] - left[c])
            max_area = max(max_area, area)
    
    return max_area
```

### ⏱️ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Histogram | O(m*n) | O(n) | ⭐ Clean |
| DP | O(m*n) | O(n) | Complex |

---

## Problem 4: Remove K Digits

**LeetCode 402 - Hard**

### Problem Statement
Given string `num` representing a non-negative integer, remove `k` digits to make the number as small as possible. Return the smallest possible number as a string.

```
Input: num = "1432219", k = 3
Output: "1219"
Explanation: Remove 4, 3, 2 to get 1219
```

### 🎯 Intuition
Use **monotonic increasing stack** with greedy approach:
- Always try to keep smaller digits at the front
- Remove larger digits when possible
- If digit is smaller than stack top, remove stack top

Think of it as building the smallest number digit by digit!

### 📊 Visual Representation

```
num = "1432219", k = 3

Process:
         Stack         k_remaining
Start:   []           3

'1':     [1]          3  (keep 1)

'4':     [1,4]        3  (4 > 1, keep)

'3':     [1,3]        2  (3 < 4, remove 4, k--)

'2':     [1,2]        1  (2 < 3, remove 3, k--)

'2':     [1,2,2]      1  (2 = 2, keep)

'1':     [1,1]        0  (1 < 2, remove 2, k--)

'9':     [1,1,9]      0  (k=0, keep all remaining)

Result: "1219"

Key Insight: We're building smallest number by:
- Removing digits that create "peaks"
- Keeping monotonic increasing sequence
```

### Solution

```python
def removeKdigits(num, k):
    """
    Use monotonic stack to build smallest number.
    
    Logic:
    - Iterate through digits
    - Remove larger digits when we can (k > 0)
    - Build monotonic increasing sequence
    
    Time: O(n)
    Space: O(n)
    """
    stack = []
    
    for digit in num:
        # Remove larger digits from stack
        while stack and k > 0 and stack[-1] > digit:
            stack.pop()
            k -= 1
        
        # Avoid leading zeros
        if stack or digit != '0':
            stack.append(digit)
    
    # If k > 0, remove from end
    while k > 0 and stack:
        stack.pop()
        k -= 1
    
    return ''.join(stack) if stack else '0'

# Example usage
print(removeKdigits("1432219", 3))  # Output: "1219"
print(removeKdigits("10200", 1))    # Output: "200"
print(removeKdigits("10", 2))       # Output: "0"
```

### Edge Cases

```python
def test_edge_cases():
    # All zeros
    assert removeKdigits("000", 1) == "0"
    
    # Monotonic increasing
    assert removeKdigits("123456", 3) == "123"
    
    # Monotonic decreasing
    assert removeKdigits("654321", 3) == "321"
    
    # Leading zeros after removal
    assert removeKdigits("10200", 1) == "200"
    
    # Remove all digits
    assert removeKdigits("10", 2) == "0"
    
    # No removal needed
    assert removeKdigits("123", 0) == "123"
```

### 🔍 Dry Run

```
num = "5337", k = 2

digit='5': stack=[5], k=2
digit='3': 3<5, pop 5, k=1
           stack=[3], k=1
digit='3': 3=3, stack=[3,3], k=1
digit='7': 7>3, stack=[3,3,7], k=1

k=1 remaining, remove from end
stack=[3,3]

Result: "33"
```

### ⏱️ Complexity Analysis

| Operation | Time | Space | Notes |
|-----------|------|-------|-------|
| Building | O(n) | O(n) | Each digit processed once |
| Total | O(n) | O(n) | ⭐ Optimal |

---

## Problem 5: 132 Pattern

**LeetCode 456 - Hard**

### Problem Statement
Given array of `n` integers, check if there exists a triplet `(i, j, k)` where `i < j < k` and `nums[i] < nums[k] < nums[j]` (132 pattern).

```
Input: nums = [3,1,4,2]
Output: true
Explanation: 132 pattern exists: 1 < 2 < 4
```

### 🎯 Intuition
Use **monotonic decreasing stack** to track potential "3" values:
- Iterate from right to left
- Stack maintains candidates for "3" (middle value)
- Track "2" (rightmost smaller value)
- Check if current element can be "1"

Pattern: Find `nums[i] < second < stack.top()`

### 📊 Visual Representation

```
nums = [3, 1, 4, 2]

Right to left scan:
                 stack      second
Index 3: num=2   [2]       -∞
Index 2: num=4   [4]       2 (pop 2, update second)
Index 1: num=1   [4]       2 (1 < 2 < 4? YES! ✓)

Visual:
  3   4
      ↑ stack top (3)
  1 ↓ 2
    ↑   ↑ second (2)
    1 (found! 1 < 2 < 4)

Key: We need leftmost < second < stack_top
```

### Solution

```python
def find132pattern(nums):
    """
    Use monotonic stack to find 132 pattern.
    
    Logic:
    - Scan right to left
    - Maintain stack of potential '3' values
    - Track maximum '2' value seen so far
    - Check if current can be '1'
    
    Time: O(n)
    Space: O(n)
    """
    if len(nums) < 3:
        return False
    
    stack = []
    second = float('-inf')  # The '2' in 132
    
    # Scan from right to left
    for i in range(len(nums) - 1, -1, -1):
        # Found '1' where nums[i] < second < stack.top()
        if nums[i] < second:
            return True
        
        # Update second and maintain decreasing stack
        while stack and nums[i] > stack[-1]:
            second = stack.pop()
        
        stack.append(nums[i])
    
    return False

# Example usage
print(find132pattern([3,1,4,2]))     # True
print(find132pattern([1,2,3,4]))     # False
print(find132pattern([3,5,0,3,4]))   # True
```

### Alternative: Two-Pass with Min Array

```python
def find132pattern_twopass(nums):
    """
    Use min array to track minimum on left.
    Then search for 3-2 pair on right.
    
    Time: O(n²) worst case, O(n) with optimization
    Space: O(n)
    """
    if len(nums) < 3:
        return False
    
    n = len(nums)
    
    # min_left[i] = minimum in nums[0:i+1]
    min_left = [0] * n
    min_left[0] = nums[0]
    for i in range(1, n):
        min_left[i] = min(min_left[i-1], nums[i])
    
    # Use stack to find 3-2 pair
    stack = []
    for j in range(n - 1, -1, -1):
        if nums[j] > min_left[j]:
            # Remove elements <= min_left[j]
            while stack and stack[-1] <= min_left[j]:
                stack.pop()
            
            # Check if top of stack can be '2'
            if stack and stack[-1] < nums[j]:
                return True
            
            stack.append(nums[j])
    
    return False
```

### 🔍 Dry Run

```
nums = [3, 5, 0, 3, 4]

Right to left:
i=4: num=4, stack=[4], second=-∞
i=3: num=3, 3<4, pop 4, second=4, stack=[3]
i=2: num=0, 0<4? YES! Return True

Pattern found: nums[2]=0, second=4, 
where 0 < 4 and 4 < 5
132 pattern: 0 < 4 < 5 ✓
```

### ⏱️ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Stack (RTL) | O(n) | O(n) | ⭐ Optimal |
| Two-Pass | O(n) | O(n) | More intuitive |

---

## Problem 6: Valid Parenthesis String

**LeetCode 678 - Hard**

### Problem Statement
Given string `s` containing `(`, `)`, and `*`, where `*` can be `(`, `)`, or empty string, determine if `s` is valid.

```
Input: s = "(*)"
Output: true
Explanation: * can be ), making it valid
```

### 🎯 Intuition
Use **two-pass greedy** or **range tracking**:

**Key Insight**: Track range of possible open parentheses count.
- `*` can add 0, +1, or -1 to count
- Track `[low, high]` range of possible counts
- Valid if 0 is in range throughout

### 📊 Visual Representation

```
s = "(*))"

Track [low, high] range:
char | low | high | explanation
-----|-----|------|-------------
'('  | 1   | 1    | Must be 1 open
'*'  | 0   | 2    | Can be ), empty, or (
')'  | -1→0| 1    | Close one, low can't be negative
')'  | -1→0| 0    | Close one more

Final: low=0, high=0
Valid if high>=0 throughout AND low<=high
```

### Approach 1: Range Tracking (Optimal)

```python
def checkValidString(s):
    """
    Track range of possible open parentheses.
    
    Logic:
    - low: minimum possible open count
    - high: maximum possible open count
    - For '(': both increase
    - For ')': both decrease
    - For '*': low--, high++
    
    Time: O(n)
    Space: O(1)
    """
    low = 0   # Minimum possible open parens
    high = 0  # Maximum possible open parens
    
    for char in s:
        if char == '(':
            low += 1
            high += 1
        elif char == ')':
            low -= 1
            high -= 1
        else:  # char == '*'
            low -= 1   # Treat * as )
            high += 1  # Treat * as (
        
        # Too many closing parens
        if high < 0:
            return False
        
        # Can't have negative open count
        low = max(low, 0)
    
    # Valid if we can have 0 open parens
    return low == 0

# Example usage
print(checkValidString("()"))        # True
print(checkValidString("(*)"))       # True
print(checkValidString("(*))"))      # True
```

### Approach 2: Two Pass

```python
def checkValidString_twopass(s):
    """
    Check left-to-right, then right-to-left.
    
    Time: O(n)
    Space: O(1)
    """
    # Left to right: check if too many ')'
    balance = 0
    for char in s:
        if char in '(*':
            balance += 1
        else:
            balance -= 1
        if balance < 0:
            return False
    
    # Right to left: check if too many '('
    balance = 0
    for char in reversed(s):
        if char in ')*':
            balance += 1
        else:
            balance -= 1
        if balance < 0:
            return False
    
    return True
```

### Approach 3: DP (Overkill but Complete)

```python
def checkValidString_dp(s):
    """
    Use DP to check all possibilities.
    
    dp[i][j] = True if s[i:j+1] is valid
    
    Time: O(n³)
    Space: O(n²)
    """
    n = len(s)
    if n == 0:
        return True
    
    dp = [[False] * n for _ in range(n)]
    
    # Base case: single character
    for i in range(n):
        if s[i] == '*':
            dp[i][i] = True
    
    # Base case: two characters
    for i in range(n - 1):
        if (s[i] in '(*' and s[i+1] in '*)'):
            dp[i][i+1] = True
    
    # Fill DP table
    for length in range(3, n + 1):
        for i in range(n - length + 1):
            j = i + length - 1
            
            # Check if s[i] and s[j] can match
            if (s[i] in '(*' and s[j] in '*)' and
                (i + 1 > j - 1 or dp[i+1][j-1])):
                dp[i][j] = True
            
            # Check if can split
            for k in range(i, j):
                if dp[i][k] and dp[k+1][j]:
                    dp[i][j] = True
                    break
    
    return dp[0][n-1]
```

### ⏱️ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Range Track | O(n) | O(1) | ⭐ Optimal |
| Two Pass | O(n) | O(1) | Simple |
| DP | O(n³) | O(n²) | Complete |

---

## Problem 7: Longest Valid Parentheses

**LeetCode 32 - Hard**

### Problem Statement
Given string containing just `(` and `)`, find the length of the longest valid (well-formed) parentheses substring.

```
Input: s = "(()"
Output: 2
Explanation: "()" is the longest valid substring

Input: s = ")()())"
Output: 4
Explanation: "()()" is the longest valid substring
```

### 🎯 Intuition
Multiple approaches:

1. **Stack**: Track indices of unmatched parentheses
2. **DP**: `dp[i]` = length of longest valid ending at `i`
3. **Two-pass**: Count from left-to-right, then right-to-left

### 📊 Visual Representation

```
s = "()(()"

Using Stack:
         stack      matched
Init:    [-1]       
'(' (0): [-1,0]     
')' (1): [-1]       len=1-(-1)=2 ✓
'(' (2): [-1,2]     
'(' (3): [-1,2,3]   
')' (4): [-1,2]     len=4-2=2

Max valid = 2

Using DP:
Index:  0 1 2 3 4
String: ( ) ( ( )
dp:     0 2 0 0 2

At i=1: s[1]=')' and s[0]='('
  dp[1] = 2
At i=4: s[4]=')' and s[3]='('
  dp[4] = 2 (can't extend beyond)
```

### Approach 1: Stack (Cleanest)

```python
def longestValidParentheses_stack(s):
    """
    Use stack to track unmatched indices.
    
    Logic:
    - Stack stores indices of unmatched parens
    - When matched, calculate length
    - Track maximum length
    
    Time: O(n)
    Space: O(n)
    """
    stack = [-1]  # Base for calculation
    max_len = 0
    
    for i in range(len(s)):
        if s[i] == '(':
            stack.append(i)
        else:  # s[i] == ')'
            stack.pop()
            
            if not stack:
                # No matching '(' for this ')'
                stack.append(i)
            else:
                # Calculate length of valid substring
                length = i - stack[-1]
                max_len = max(max_len, length)
    
    return max_len
```

### Approach 2: Dynamic Programming

```python
def longestValidParentheses_dp(s):
    """
    DP where dp[i] = longest valid ending at i.
    
    Logic:
    - dp[i] = 0 if s[i] == '('
    - dp[i] = dp[i-2] + 2 if s[i-1:i+1] == '()'
    - dp[i] = dp[i-1] + dp[i-dp[i-1]-2] + 2 if complex
    
    Time: O(n)
    Space: O(n)
    """
    if not s:
        return 0
    
    n = len(s)
    dp = [0] * n
    max_len = 0
    
    for i in range(1, n):
        if s[i] == ')':
            if s[i-1] == '(':
                # Pattern: ...()
                dp[i] = (dp[i-2] if i >= 2 else 0) + 2
            elif i - dp[i-1] > 0 and s[i - dp[i-1] - 1] == '(':
                # Pattern: ...))
                dp[i] = dp[i-1] + 2
                if i - dp[i-1] >= 2:
                    dp[i] += dp[i - dp[i-1] - 2]
            
            max_len = max(max_len, dp[i])
    
    return max_len
```

### Approach 3: Two-Pass (Space Optimal)

```python
def longestValidParentheses_twopass(s):
    """
    Count left-to-right, then right-to-left.
    
    Time: O(n)
    Space: O(1)
    """
    max_len = 0
    
    # Left to right
    left = right = 0
    for char in s:
        if char == '(':
            left += 1
        else:
            right += 1
        
        if left == right:
            max_len = max(max_len, 2 * right)
        elif right > left:
            left = right = 0
    
    # Right to left
    left = right = 0
    for char in reversed(s):
        if char == '(':
            left += 1
        else:
            right += 1
        
        if left == right:
            max_len = max(max_len, 2 * left)
        elif left > right:
            left = right = 0
    
    return max_len
```

### 🔍 Dry Run

```
s = ")()())"

Stack approach:
i=0: ')' pop -1, stack=[], push 0
     stack=[0]

i=1: '(' push 1
     stack=[0,1]

i=2: ')' pop 1, len=2-0=2
     stack=[0]

i=3: '(' push 3
     stack=[0,3]

i=4: ')' pop 3, len=4-0=4
     stack=[0]

i=5: ')' pop 0, stack=[], push 5
     stack=[5]

Max = 4 ✓
```

### ⏱️ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Stack | O(n) | O(n) | ⭐ Clean |
| DP | O(n) | O(n) | Intuitive |
| Two-Pass | O(n) | O(1) | Space optimal |

---

## 🎯 Pattern Summary

### Core Stack Patterns Covered

1. **Monotonic Stack**
   - Problems: Largest Rectangle, Remove K Digits
   - Pattern: Maintain increasing/decreasing order
   - Use: Find next/previous greater/smaller

2. **Expression Evaluation**
   - Problems: Basic Calculator III
   - Pattern: Operator precedence + parentheses
   - Use: Parse and evaluate expressions

3. **2D to 1D Reduction**
   - Problems: Maximal Rectangle
   - Pattern: Convert 2D problem to multiple 1D
   - Use: Leverage 1D algorithms

4. **Pattern Matching**
   - Problems: 132 Pattern, Valid Parenthesis String
   - Pattern: Stack + auxiliary variable tracking
   - Use: Find complex patterns in arrays

5. **Index Tracking**
   - Problems: Longest Valid Parentheses
   - Pattern: Stack stores indices, not values
   - Use: Calculate lengths/distances

### Complexity Patterns

- **Time**: Almost all O(n) with single pass
- **Space**: O(n) for stack, sometimes O(1) optimizations
- **Key**: Each element pushed/popped at most once

### When to Use Stack

✅ **Use Stack When:**
- Need to match pairs (parentheses, tags)
- Find next/previous greater/smaller
- Evaluate expressions with precedence
- Track indices for distance calculations
- Maintain monotonic order

❌ **Don't Use Stack When:**
- Need random access to elements
- Order doesn't matter
- Can solve with two pointers more simply

### Interview Tips

1. **Identify monotonic stack**: Look for "next greater/smaller" keywords
2. **Index vs Value**: Sometimes store indices, not values
3. **Auxiliary variables**: Often need second/third variable with stack
4. **Edge cases**: Empty stack, single element, all same
5. **Optimization**: Can sometimes replace stack with variables

---

## 🎓 Key Takeaways

1. **Monotonic Stack** is most powerful pattern (40% of hard problems)
2. **Stack + Index** enables distance/length calculations
3. **Two-pass** often gives O(1) space optimization
4. **DP alternative** exists for many stack problems (but worse complexity)
5. **Expression parsing** requires precedence and parentheses handling

Practice these patterns and you'll master hard stack problems! 🚀

