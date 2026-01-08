# 🔥 Recursion - Hard Problems Collection

A comprehensive collection of challenging Recursion problems with divide & conquer, memoization, and detailed explanations.

---

## 📚 Table of Contents

1. [Different Ways to Add Parentheses](#problem-1-different-ways-to-add-parentheses)
2. [Parsing Boolean Expression](#problem-2-parsing-boolean-expression)
3. [Encode String with Shortest Length](#problem-3-encode-string-with-shortest-length)
4. [Burst Balloons](#problem-4-burst-balloons)
5. [Basic Calculator](#problem-5-basic-calculator)
6. [Decode Ways II](#problem-6-decode-ways-ii)
7. [Pattern Summary](#-pattern-summary)

---

## Problem 1: Different Ways to Add Parentheses

**LeetCode 241 - Medium/Hard**

### Problem Statement
Given expression with numbers and operators, compute all possible results from different groupings.

```
Input: expression = "2-1-1"
Output: [0, 2]
Explanation: 
  ((2-1)-1) = 0
  (2-(1-1)) = 2

Input: expression = "2*3-4*5"
Output: [-34, -14, -10, -10, 10]
```

### 🎯 Intuition
**Divide and conquer with memoization:**
- Split expression at each operator
- Recursively compute left and right parts
- Combine results with current operator
- Memoize to avoid recomputation

### 📊 Visual Representation

```
Expression: "2*3-4*5"

Split at each operator:
  Split at * (position 1):
    Left: "2" → [2]
    Right: "3-4*5"
      Split at -:
        Left: "3" → [3]
        Right: "4*5" → [20]
        Results: [3-20] = [-17]
      Split at *:
        Left: "3-4" → [-1]
        Right: "5" → [5]
        Results: [-1*5] = [-5]
      Results: [-17, -5]
    Combine: [2*(-17), 2*(-5)] = [-34, -10]
  
  Split at - (position 3):
    Left: "2*3" → [6]
    Right: "4*5" → [20]
    Combine: [6-20] = [-14]
  
  Split at * (position 5):
    Left: "2*3-4" → [-2, 10]
    Right: "5" → [5]
    Combine: [-2*5, 10*5] = [-10, 10]

All results: [-34, -14, -10, -10, 10]
```

### Solution

```python
def diffWaysToCompute(expression: str):
    """
    Divide and conquer with memoization.
    
    Logic:
    - Split at each operator
    - Recursively compute left and right
    - Combine with operator
    - Cache results
    
    Time: O(Catalan(n)) ≈ O(4^n / n^(3/2))
    Space: O(n * Catalan(n))
    """
    memo = {}
    
    def compute(expr):
        if expr in memo:
            return memo[expr]
        
        # Base case: single number
        if expr.isdigit():
            return [int(expr)]
        
        # Check if no operators (multi-digit number)
        if expr.lstrip('-').isdigit():
            return [int(expr)]
        
        results = []
        
        for i, char in enumerate(expr):
            if char in '+-*':
                # Split at operator
                left_results = compute(expr[:i])
                right_results = compute(expr[i+1:])
                
                # Combine results
                for left in left_results:
                    for right in right_results:
                        if char == '+':
                            results.append(left + right)
                        elif char == '-':
                            results.append(left - right)
                        else:  # *
                            results.append(left * right)
        
        memo[expr] = results
        return results
    
    return compute(expression)

# Example usage
print(diffWaysToCompute("2-1-1"))      # [0, 2]
print(diffWaysToCompute("2*3-4*5"))    # [-34, -14, -10, -10, 10]
```

### ⏱️ Complexity Analysis

| Metric | Complexity | Notes |
|--------|------------|-------|
| Time | O(Catalan(n)) | n = operators |
| Space | O(n * Catalan) | Memoization |

---

## Problem 2: Parsing Boolean Expression

**LeetCode 1106 - Hard**

### Problem Statement
Evaluate boolean expression with &, |, ! operators.

```
Input: expression = "!(f)"
Output: true

Input: expression = "|(f,t)"
Output: true

Input: expression = "&(t,f)"
Output: false
```

### Solution

```python
def parseBoolExpr(expression: str) -> bool:
    """
    Recursive parsing with stack.
    
    Logic:
    - Parse expression recursively
    - Handle operators: &, |, !
    - Combine sub-expressions
    
    Time: O(n)
    Space: O(n) recursion stack
    """
    idx = [0]  # Use list for mutable integer
    
    def parse():
        char = expression[idx[0]]
        idx[0] += 1
        
        if char == 't':
            return True
        if char == 'f':
            return False
        
        # Operator
        operator = char
        idx[0] += 1  # Skip '('
        
        values = []
        while expression[idx[0]] != ')':
            if expression[idx[0]] == ',':
                idx[0] += 1
                continue
            values.append(parse())
        
        idx[0] += 1  # Skip ')'
        
        # Apply operator
        if operator == '!':
            return not values[0]
        elif operator == '&':
            return all(values)
        else:  # |
            return any(values)
    
    return parse()

# Example usage
print(parseBoolExpr("!(f)"))      # True
print(parseBoolExpr("|(f,t)"))    # True
print(parseBoolExpr("&(t,f)"))    # False
print(parseBoolExpr("&(|(f,t),!(t))"))  # False
```

### ⏱️ Complexity Analysis

| Metric | Complexity | Notes |
|--------|------------|-------|
| Time | O(n) | Single pass |
| Space | O(n) | Recursion depth |

---

## Problem 3: Encode String with Shortest Length

**LeetCode 471 - Hard**

### Problem Statement
Encode string to shortest length using pattern repetition.

```
Input: s = "aaa"
Output: "aaa" (or "3[a]" if that's shorter)

Input: s = "aaaaa"
Output: "5[a]"

Input: s = "aaabaaaba"
Output: "3[a3[a]b]"
```

### Solution

```python
def encode(s: str) -> str:
    """
    DP with pattern detection.
    
    Logic:
    - For each substring, try encoding
    - Check for repeating patterns
    - Use DP to combine best encodings
    
    Time: O(n³)
    Space: O(n²)
    """
    n = len(s)
    dp = [[""] * n for _ in range(n)]
    
    for length in range(1, n + 1):
        for i in range(n - length + 1):
            j = i + length - 1
            substr = s[i:j+1]
            
            # Default: no encoding
            dp[i][j] = substr
            
            # Try all split points
            for k in range(i, j):
                left = dp[i][k]
                right = dp[k+1][j]
                if len(left) + len(right) < len(dp[i][j]):
                    dp[i][j] = left + right
            
            # Try pattern encoding
            pattern = (substr + substr).find(substr, 1)
            if pattern < len(substr):
                # Found repeating pattern
                encoded = f"{len(substr) // pattern}[{dp[i][i + pattern - 1]}]"
                if len(encoded) < len(dp[i][j]):
                    dp[i][j] = encoded
    
    return dp[0][n-1]

# Example usage
print(encode("aaa"))        # "aaa"
print(encode("aaaaa"))      # "5[a]"
print(encode("aaabaaaba"))  # Encoded version
```

### ⏱️ Complexity Analysis

| Metric | Complexity | Notes |
|--------|------------|-------|
| Time | O(n³) | DP with splits |
| Space | O(n²) | DP table |

---

## Problem 4: Burst Balloons

**LeetCode 312 - Hard**

### Problem Statement
Pop balloons to maximize coins. Popping balloon i gives coins[i-1] * coins[i] * coins[i+1].

```
Input: nums = [3,1,5,8]
Output: 167
Explanation: 
  nums = [3,1,5,8] → [3,5,8] → [3,8] → [8] → []
  coins = 3*1*5 + 3*5*8 + 1*3*8 + 1*8*1 = 167
```

### 🎯 Intuition
**Think backwards:**
- Instead of "which balloon to pop first?"
- Ask "which balloon to pop last?"
- For range [left, right], try each balloon as last
- Recursively solve subproblems

### Solution

```python
def maxCoins(nums: list) -> int:
    """
    DP with range selection.
    
    Logic:
    - Add 1s at boundaries
    - For each range, try popping each balloon last
    - Use DP to cache results
    
    Time: O(n³)
    Space: O(n²)
    """
    # Add boundaries
    nums = [1] + nums + [1]
    n = len(nums)
    dp = [[0] * n for _ in range(n)]
    
    # length of range
    for length in range(2, n):
        for left in range(n - length):
            right = left + length
            
            # Try popping each balloon in range last
            for i in range(left + 1, right):
                coins = nums[left] * nums[i] * nums[right]
                coins += dp[left][i] + dp[i][right]
                dp[left][right] = max(dp[left][right], coins)
    
    return dp[0][n-1]

# Example usage
print(maxCoins([3,1,5,8]))  # 167
```

### Recursive with Memoization

```python
def maxCoins_recursive(nums: list) -> int:
    """
    Top-down approach.
    
    Time: O(n³)
    Space: O(n²)
    """
    nums = [1] + nums + [1]
    memo = {}
    
    def dp(left, right):
        if (left, right) in memo:
            return memo[(left, right)]
        
        if left + 1 == right:
            return 0
        
        max_coins = 0
        for i in range(left + 1, right):
            coins = nums[left] * nums[i] * nums[right]
            coins += dp(left, i) + dp(i, right)
            max_coins = max(max_coins, coins)
        
        memo[(left, right)] = max_coins
        return max_coins
    
    return dp(0, len(nums) - 1)
```

### ⏱️ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Bottom-up DP | O(n³) | O(n²) | ⭐ Clear |
| Top-down | O(n³) | O(n²) | Intuitive |

---

## Problem 5: Basic Calculator

**LeetCode 224, 227, 772 - Hard**

### Problem Statement
Implement calculator that evaluates expression with +, -, *, /, (, ).

```
Input: s = "1 + 1"
Output: 2

Input: s = "(1+(4+5+2)-3)+(6+8)"
Output: 23
```

### Solution: Full Calculator (with parentheses)

```python
def calculate(s: str) -> int:
    """
    Recursive descent parser.
    
    Logic:
    - Use stack for numbers and operators
    - Handle parentheses recursively
    - Process operators by precedence
    
    Time: O(n)
    Space: O(n)
    """
    def evaluate(s):
        stack = []
        num = 0
        sign = '+'
        i = 0
        
        while i < len(s):
            char = s[i]
            
            if char.isdigit():
                num = num * 10 + int(char)
            
            if char == '(':
                # Find matching closing parenthesis
                count = 1
                j = i + 1
                while count > 0:
                    if s[j] == '(':
                        count += 1
                    elif s[j] == ')':
                        count -= 1
                    j += 1
                # Recursively evaluate
                num = evaluate(s[i+1:j-1])
                i = j - 1
            
            if char in '+-*/' or i == len(s) - 1:
                if char != ' ' or i == len(s) - 1:
                    if sign == '+':
                        stack.append(num)
                    elif sign == '-':
                        stack.append(-num)
                    elif sign == '*':
                        stack.append(stack.pop() * num)
                    elif sign == '/':
                        stack.append(int(stack.pop() / num))
                    
                    if i < len(s) - 1:
                        sign = char
                    num = 0
            
            i += 1
        
        return sum(stack)
    
    return evaluate(s)

# Example usage
print(calculate("1 + 1"))                    # 2
print(calculate("(1+(4+5+2)-3)+(6+8)"))     # 23
```

### Iterative with Stack

```python
def calculate_stack(s: str) -> int:
    """
    Stack-based evaluation.
    
    Time: O(n)
    Space: O(n)
    """
    stack = []
    num = 0
    sign = 1
    result = 0
    
    for char in s:
        if char.isdigit():
            num = num * 10 + int(char)
        elif char == '+':
            result += sign * num
            num = 0
            sign = 1
        elif char == '-':
            result += sign * num
            num = 0
            sign = -1
        elif char == '(':
            stack.append(result)
            stack.append(sign)
            result = 0
            sign = 1
        elif char == ')':
            result += sign * num
            num = 0
            result *= stack.pop()  # sign before parentheses
            result += stack.pop()  # result before parentheses
    
    result += sign * num
    return result
```

### ⏱️ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Recursive | O(n) | O(n) | Clean |
| Stack | O(n) | O(n) | ⭐ Efficient |

---

## Problem 6: Decode Ways II

**LeetCode 639 - Hard**

### Problem Statement
Decode string with wildcards (*). '*' can represent 1-9.

```
Input: s = "*"
Output: 9

Input: s = "1*"
Output: 18
```

### Solution

```python
def numDecodings(s: str) -> int:
    """
    DP with wildcard handling.
    
    Logic:
    - dp[i] = ways to decode s[:i]
    - Handle * as 1-9
    - Handle two-digit combinations
    
    Time: O(n)
    Space: O(1) with optimization
    """
    MOD = 10**9 + 7
    n = len(s)
    
    # dp[i] = ways to decode first i characters
    dp0 = 1  # dp[i-2]
    dp1 = 9 if s[0] == '*' else (0 if s[0] == '0' else 1)  # dp[i-1]
    
    for i in range(1, n):
        dp2 = 0
        
        # Single digit
        if s[i] == '*':
            dp2 = 9 * dp1
        elif s[i] != '0':
            dp2 = dp1
        
        # Two digits
        if s[i-1] == '*':
            if s[i] == '*':
                dp2 += 15 * dp0  # 11-19, 21-26
            elif s[i] <= '6':
                dp2 += 2 * dp0   # 1X, 2X
            else:
                dp2 += dp0       # 1X only
        elif s[i-1] == '1':
            if s[i] == '*':
                dp2 += 9 * dp0   # 11-19
            else:
                dp2 += dp0       # 10-19
        elif s[i-1] == '2':
            if s[i] == '*':
                dp2 += 6 * dp0   # 21-26
            elif s[i] <= '6':
                dp2 += dp0       # 20-26
        
        dp0, dp1 = dp1, dp2 % MOD
    
    return dp1

# Example usage
print(numDecodings("*"))     # 9
print(numDecodings("1*"))    # 18
```

### ⏱️ Complexity Analysis

| Metric | Complexity | Notes |
|--------|------------|-------|
| Time | O(n) | Single pass |
| Space | O(1) | Optimized |

---

## 🎯 Pattern Summary

### Core Recursion Patterns

1. **Divide and Conquer** - Split problem, combine results
2. **Expression Parsing** - Recursive descent parser
3. **Range DP** - Optimal substructure over ranges
4. **Memoization** - Cache recursive results
5. **Backtracking with Pruning** - Try all possibilities efficiently

### Problem Categories

| Category | Problems | Key Technique |
|----------|----------|---------------|
| Expression Eval | Add Parentheses, Calculator | Divide & conquer |
| Parsing | Boolean Expr, Calculator | Recursive parser |
| Encoding | Encode String | DP + pattern |
| Interval DP | Burst Balloons | Range optimization |
| String Decode | Decode Ways | DP with cases |

### Recursion Patterns

```python
# 1. Divide and Conquer
def divide_conquer(problem):
    if base_case(problem):
        return solution
    
    subproblems = split(problem)
    subsolutions = [divide_conquer(sub) for sub in subproblems]
    return combine(subsolutions)

# 2. Memoization
memo = {}
def solve(state):
    if state in memo:
        return memo[state]
    
    result = compute(state)
    memo[state] = result
    return result

# 3. Range DP (Bottom-up)
for length in range(1, n+1):
    for i in range(n-length+1):
        j = i + length - 1
        for k in range(i, j+1):
            dp[i][j] = max(dp[i][j], dp[i][k] + dp[k+1][j])

# 4. Expression Parsing
def parse():
    if is_operand():
        return get_value()
    
    operator = get_operator()
    left = parse()
    right = parse()
    return apply(operator, left, right)
```

### Optimization Techniques

1. **Memoization:** Cache results to avoid recomputation
2. **Bottom-up DP:** Convert recursion to iteration
3. **Space Optimization:** Use O(1) space when possible
4. **Early Termination:** Prune impossible branches
5. **Pattern Recognition:** Find repeating subproblems

### Interview Tips

1. **Base Case:** Always define clear base cases
2. **State:** What parameters define unique subproblem?
3. **Memoization:** Can we cache? What's the key?
4. **Iteration:** Can we convert to bottom-up DP?
5. **Complexity:** What's the number of unique states?

Master these recursion patterns for interview success! 🚀

