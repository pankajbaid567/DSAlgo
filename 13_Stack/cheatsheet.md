# 📚 Stack - Comprehensive Cheatsheet

## Core Concepts

### What is a Stack?
LIFO (Last In, First Out) data structure.

```python
# Python list as stack
stack = []
stack.append(1)    # push
stack.append(2)
top = stack[-1]    # peek
val = stack.pop()  # pop

# Using collections.deque (more efficient)
from collections import deque
stack = deque()
stack.append(1)
stack.pop()
```

---

## Essential Patterns

### Pattern 1: Next Greater Element

```python
def next_greater_element(arr):
    n = len(arr)
    result = [-1] * n
    stack = []
    
    for i in range(n - 1, -1, -1):
        while stack and stack[-1] <= arr[i]:
            stack.pop()
        
        if stack:
            result[i] = stack[-1]
        
        stack.append(arr[i])
    
    return result

# Circular array variant
def next_greater_circular(arr):
    n = len(arr)
    result = [-1] * n
    stack = []
    
    for i in range(2 * n - 1, -1, -1):
        while stack and stack[-1] <= arr[i % n]:
            stack.pop()
        
        if i < n and stack:
            result[i] = stack[-1]
        
        stack.append(arr[i % n])
    
    return result
```

### Pattern 2: Valid Parentheses

```python
def is_valid(s):
    stack = []
    mapping = {')': '(', '}': '{', ']': '['}
    
    for char in s:
        if char in mapping:
            top = stack.pop() if stack else '#'
            if mapping[char] != top:
                return False
        else:
            stack.append(char)
    
    return not stack
```

### Pattern 3: Monotonic Stack

```python
# Largest Rectangle in Histogram
def largest_rectangle_area(heights):
    stack = []
    max_area = 0
    heights.append(0)
    
    for i, h in enumerate(heights):
        while stack and heights[stack[-1]] > h:
            height = heights[stack.pop()]
            width = i if not stack else i - stack[-1] - 1
            max_area = max(max_area, height * width)
        stack.append(i)
    
    heights.pop()
    return max_area
```

### Pattern 4: Calculator

```python
def calculate(s):
    stack = []
    num = 0
    sign = '+'
    
    for i, char in enumerate(s):
        if char.isdigit():
            num = num * 10 + int(char)
        
        if char in '+-*/' or i == len(s) - 1:
            if sign == '+':
                stack.append(num)
            elif sign == '-':
                stack.append(-num)
            elif sign == '*':
                stack.append(stack.pop() * num)
            elif sign == '/':
                stack.append(int(stack.pop() / num))
            
            sign = char
            num = 0
    
    return sum(stack)
```

---

## 🎯 Must-Know Problems

### Easy
- [20. Valid Parentheses](https://leetcode.com/problems/valid-parentheses/)
- [155. Min Stack](https://leetcode.com/problems/min-stack/)
- [232. Implement Queue using Stacks](https://leetcode.com/problems/implement-queue-using-stacks/)

### Medium
- [739. Daily Temperatures](https://leetcode.com/problems/daily-temperatures/)
- [503. Next Greater Element II](https://leetcode.com/problems/next-greater-element-ii/)
- [84. Largest Rectangle in Histogram](https://leetcode.com/problems/largest-rectangle-in-histogram/)
- [150. Evaluate Reverse Polish Notation](https://leetcode.com/problems/evaluate-reverse-polish-notation/)
- [394. Decode String](https://leetcode.com/problems/decode-string/)

### Hard
- [85. Maximal Rectangle](https://leetcode.com/problems/maximal-rectangle/)
- [224. Basic Calculator](https://leetcode.com/problems/basic-calculator/)
- [32. Longest Valid Parentheses](https://leetcode.com/problems/longest-valid-parentheses/)
- [42. Trapping Rain Water](https://leetcode.com/problems/trapping-rain-water/)

---

## Pro Tips

1. **Monotonic stack**: Keep stack in increasing/decreasing order
2. **Look for**: Next/previous greater/smaller elements
3. **Parentheses**: Almost always uses stack
4. **Calculator**: Use stack for operations

---

**Stack is the key to many elegant solutions! 📚**
