# 🔥 Math - Hard Problems Collection

A comprehensive collection of challenging Math problems with number theory, combinatorics, geometry, and detailed explanations.

---

## 📚 Table of Contents

1. [Pow(x, n)](#problem-1-powx-n)
2. [Divide Two Integers](#problem-2-divide-two-integers)
3. [Super Pow](#problem-3-super-pow)
4. [Count Primes (Sieve of Eratosthenes)](#problem-4-count-primes)
5. [Fraction to Recurring Decimal](#problem-5-fraction-to-recurring-decimal)
6. [Number of Digit One](#problem-6-number-of-digit-one)
7. [Nth Catalan Number](#problem-7-nth-catalan-number)
8. [Pattern Summary](#-pattern-summary)

---

## Problem 1: Pow(x, n)

**LeetCode 50 - Medium/Hard**

### Problem Statement
Calculate x raised to power n (x^n).

```
Input: x = 2.0, n = 10
Output: 1024.0

Input: x = 2.0, n = -2
Output: 0.25
```

### 🎯 Intuition
**Binary exponentiation (Fast power):**
- x^n = (x^2)^(n/2) if n is even
- x^n = x * x^(n-1) if n is odd
- Handle negative exponents: x^(-n) = 1/(x^n)

**Time:** O(log n) instead of O(n)

### 📊 Visual Representation

```
Calculate 2^10:

Naive: 2 * 2 * 2 * 2 * 2 * 2 * 2 * 2 * 2 * 2 (10 multiplications)

Fast Power:
  2^10 = (2^2)^5 = 4^5
  4^5 = 4 * 4^4 = 4 * (4^2)^2 = 4 * 16^2
  16^2 = 256
  4 * 256 = 1024

Only 5 operations!

Binary representation:
  10 = 1010 (binary)
  2^10 = 2^8 * 2^2
       = (2^1)^8 * (2^1)^2
```

### Solution 1: Recursive

```python
def myPow(x: float, n: int) -> float:
    """
    Binary exponentiation (recursive).
    
    Logic:
    - x^n = (x^2)^(n//2) if n even
    - x^n = x * x^(n-1) if n odd
    - Handle negative exponent
    
    Time: O(log n)
    Space: O(log n) recursion stack
    """
    def power(x, n):
        if n == 0:
            return 1.0
        
        half = power(x, n // 2)
        
        if n % 2 == 0:
            return half * half
        else:
            return half * half * x
    
    result = power(x, abs(n))
    return result if n >= 0 else 1 / result

# Example usage
print(myPow(2.0, 10))   # 1024.0
print(myPow(2.0, -2))   # 0.25
```

### Solution 2: Iterative

```python
def myPow_iterative(x: float, n: int) -> float:
    """
    Iterative binary exponentiation.
    
    Time: O(log n)
    Space: O(1)
    """
    if n == 0:
        return 1.0
    
    if n < 0:
        x = 1 / x
        n = -n
    
    result = 1.0
    current = x
    
    while n > 0:
        if n % 2 == 1:
            result *= current
        current *= current
        n //= 2
    
    return result
```

### 🔍 Dry Run

```
myPow(2.0, 10):

Recursive approach:
  power(2, 10)
    half = power(2, 5)
      half = power(2, 2)
        half = power(2, 1)
          half = power(2, 0) = 1
          return 1 * 1 * 2 = 2
        return 2 * 2 = 4
      return 4 * 4 * 2 = 32
    return 32 * 32 = 1024

Iterative approach (bit-by-bit):
  n = 10 = 1010 (binary)
  
  Iteration 1: n=10, bit=0, result=1, current=2
    Skip (even)
    current = 2*2 = 4, n = 5
  
  Iteration 2: n=5, bit=1, result=1, current=4
    result = 1*4 = 4
    current = 4*4 = 16, n = 2
  
  Iteration 3: n=2, bit=0, result=4, current=16
    Skip (even)
    current = 16*16 = 256, n = 1
  
  Iteration 4: n=1, bit=1, result=4, current=256
    result = 4*256 = 1024
    n = 0
  
  Return 1024
```

### ⏱️ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Recursive | O(log n) | O(log n) | Stack space |
| Iterative | O(log n) | O(1) | ⭐ Optimal |

---

## Problem 2: Divide Two Integers

**LeetCode 29 - Medium/Hard**

### Problem Statement
Divide two integers without using multiplication, division, or mod operator.

```
Input: dividend = 10, divisor = 3
Output: 3

Input: dividend = 7, divisor = -3
Output: -2
```

### Solution

```python
def divide(dividend: int, divisor: int) -> int:
    """
    Binary search with bit shifting.
    
    Logic:
    - Use bit shifting for multiplication by 2
    - Find largest multiple that fits
    - Subtract and repeat
    
    Time: O(log²n)
    Space: O(1)
    """
    # Handle overflow
    MAX_INT = 2**31 - 1
    MIN_INT = -2**31
    
    if dividend == MIN_INT and divisor == -1:
        return MAX_INT
    
    # Determine sign
    negative = (dividend < 0) != (divisor < 0)
    
    # Work with positive numbers
    dividend = abs(dividend)
    divisor = abs(divisor)
    
    result = 0
    
    while dividend >= divisor:
        temp = divisor
        multiple = 1
        
        # Find largest multiple
        while dividend >= (temp << 1):
            temp <<= 1
            multiple <<= 1
        
        dividend -= temp
        result += multiple
    
    return -result if negative else result

# Example usage
print(divide(10, 3))    # 3
print(divide(7, -3))    # -2
```

### ⏱️ Complexity Analysis

| Metric | Complexity | Notes |
|--------|------------|-------|
| Time | O(log²n) | Nested doubling |
| Space | O(1) | Constant space |

---

## Problem 3: Super Pow

**LeetCode 372 - Medium/Hard**

### Problem Statement
Calculate a^b mod 1337 where b is a large number represented as array.

```
Input: a = 2, b = [3]
Output: 8

Input: a = 2, b = [1,0]
Output: 1024

Input: a = 1, b = [4,3,3,8,5,2]
Output: 1
```

### 🎯 Intuition
**Modular arithmetic properties:**
- (a * b) mod m = ((a mod m) * (b mod m)) mod m
- a^(b₁b₂...bₙ) = (a^(b₁*10^(n-1)))^(b₂*10^(n-2))...
- Process digit by digit

### Solution

```python
def superPow(a: int, b: list) -> int:
    """
    Modular exponentiation with digit processing.
    
    Logic:
    - Process each digit from left to right
    - Use property: a^(xy) = (a^x)^y
    - Apply modulo at each step
    
    Time: O(n) where n = digits in b
    Space: O(1)
    """
    MOD = 1337
    
    def powmod(x, n):
        """Calculate x^n mod MOD."""
        result = 1
        x %= MOD
        
        while n > 0:
            if n % 2 == 1:
                result = (result * x) % MOD
            x = (x * x) % MOD
            n //= 2
        
        return result
    
    result = 1
    
    for digit in b:
        # result = result^10 * a^digit
        result = powmod(result, 10) * powmod(a, digit) % MOD
    
    return result

# Example usage
print(superPow(2, [3]))         # 8
print(superPow(2, [1,0]))       # 1024
print(superPow(1, [4,3,3,8,5,2]))  # 1
```

### ⏱️ Complexity Analysis

| Metric | Complexity | Notes |
|--------|------------|-------|
| Time | O(n) | n = digits in b |
| Space | O(1) | Constant space |

---

## Problem 4: Count Primes

**LeetCode 204 - Medium/Hard**

### Problem Statement
Count primes less than n.

```
Input: n = 10
Output: 4
Explanation: 2, 3, 5, 7
```

### 🎯 Intuition
**Sieve of Eratosthenes:**
1. Create boolean array for all numbers
2. Mark 0, 1 as not prime
3. For each prime p, mark all multiples as not prime
4. Count remaining primes

### Solution

```python
def countPrimes(n: int) -> int:
    """
    Sieve of Eratosthenes.
    
    Logic:
    - Mark multiples of each prime as composite
    - Count remaining primes
    
    Time: O(n log log n)
    Space: O(n)
    """
    if n <= 2:
        return 0
    
    is_prime = [True] * n
    is_prime[0] = is_prime[1] = False
    
    # Only need to check up to sqrt(n)
    for i in range(2, int(n**0.5) + 1):
        if is_prime[i]:
            # Mark all multiples as not prime
            for j in range(i*i, n, i):
                is_prime[j] = False
    
    return sum(is_prime)

# Example usage
print(countPrimes(10))  # 4
print(countPrimes(100)) # 25
```

### Optimized: Segmented Sieve

```python
def countPrimes_segmented(n: int) -> int:
    """
    Segmented sieve for large n.
    
    Time: O(n log log n)
    Space: O(√n)
    """
    if n <= 2:
        return 0
    
    limit = int(n**0.5) + 1
    
    # Find primes up to sqrt(n)
    is_prime = [True] * limit
    is_prime[0] = is_prime[1] = False
    
    for i in range(2, int(limit**0.5) + 1):
        if is_prime[i]:
            for j in range(i*i, limit, i):
                is_prime[j] = False
    
    primes = [i for i in range(limit) if is_prime[i]]
    count = sum(is_prime)
    
    # Process segments
    segment_size = limit
    for low in range(limit, n, segment_size):
        high = min(low + segment_size, n)
        segment = [True] * (high - low)
        
        for prime in primes:
            start = max(prime * prime, ((low + prime - 1) // prime) * prime)
            for j in range(start, high, prime):
                segment[j - low] = False
        
        count += sum(segment)
    
    return count
```

### ⏱️ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Basic Sieve | O(n log log n) | O(n) | ⭐ Standard |
| Segmented | O(n log log n) | O(√n) | Better space |

---

## Problem 5: Fraction to Recurring Decimal

**LeetCode 166 - Medium/Hard**

### Problem Statement
Convert fraction to decimal string with repeating part in parentheses.

```
Input: numerator = 1, denominator = 2
Output: "0.5"

Input: numerator = 2, denominator = 3
Output: "0.(6)"

Input: numerator = 4, denominator = 333
Output: "0.(012)"
```

### Solution

```python
def fractionToDecimal(numerator: int, denominator: int) -> str:
    """
    Long division with cycle detection.
    
    Logic:
    - Handle sign and integer part
    - Use HashMap to detect repeating remainders
    - Mark repeating part with parentheses
    
    Time: O(d) where d = denominator
    Space: O(d)
    """
    if numerator == 0:
        return "0"
    
    result = []
    
    # Handle sign
    if (numerator < 0) != (denominator < 0):
        result.append("-")
    
    numerator = abs(numerator)
    denominator = abs(denominator)
    
    # Integer part
    result.append(str(numerator // denominator))
    remainder = numerator % denominator
    
    if remainder == 0:
        return ''.join(result)
    
    result.append(".")
    
    # Fractional part
    remainder_map = {}  # remainder -> position
    
    while remainder != 0:
        if remainder in remainder_map:
            # Found repeating cycle
            result.insert(remainder_map[remainder], "(")
            result.append(")")
            break
        
        remainder_map[remainder] = len(result)
        remainder *= 10
        result.append(str(remainder // denominator))
        remainder %= denominator
    
    return ''.join(result)

# Example usage
print(fractionToDecimal(1, 2))    # "0.5"
print(fractionToDecimal(2, 3))    # "0.(6)"
print(fractionToDecimal(4, 333))  # "0.(012)"
```

### ⏱️ Complexity Analysis

| Metric | Complexity | Notes |
|--------|------------|-------|
| Time | O(d) | At most d remainders |
| Space | O(d) | HashMap + result |

---

## Problem 6: Number of Digit One

**LeetCode 233 - Hard**

### Problem Statement
Count total number of digit 1 in all numbers from 1 to n.

```
Input: n = 13
Output: 6
Explanation: 1, 10, 11, 12, 13 contain 6 ones
```

### 🎯 Intuition
**Digit DP approach:**
- Count 1s at each digit position
- For position i: count depends on higher, current, and lower digits
- Formula: (higher * base) + (if current > 1: base, elif current == 1: lower + 1, else: 0)

### Solution

```python
def countDigitOne(n: int) -> int:
    """
    Count ones digit by digit.
    
    Logic:
    - For each position, count how many times 1 appears
    - Consider higher, current, and lower digits
    
    Time: O(log n)
    Space: O(1)
    """
    count = 0
    factor = 1
    
    while factor <= n:
        higher = n // (factor * 10)
        current = (n // factor) % 10
        lower = n % factor
        
        if current == 0:
            count += higher * factor
        elif current == 1:
            count += higher * factor + lower + 1
        else:
            count += (higher + 1) * factor
        
        factor *= 10
    
    return count

# Example usage
print(countDigitOne(13))   # 6
print(countDigitOne(100))  # 21
```

### 🔍 Dry Run

```
n = 13

Position 1 (ones place, factor=1):
  higher = 13 // 10 = 1
  current = (13 // 1) % 10 = 3
  lower = 13 % 1 = 0
  current > 1: count += (1 + 1) * 1 = 2
  (Numbers: 1, 11)

Position 2 (tens place, factor=10):
  higher = 13 // 100 = 0
  current = (13 // 10) % 10 = 1
  lower = 13 % 10 = 3
  current == 1: count += 0 * 10 + 3 + 1 = 4
  (Numbers: 10, 11, 12, 13)

Total: 2 + 4 = 6
```

### ⏱️ Complexity Analysis

| Metric | Complexity | Notes |
|--------|------------|-------|
| Time | O(log n) | Process each digit |
| Space | O(1) | Constant space |

---

## Problem 7: Nth Catalan Number

**Custom Problem - Hard**

### Problem Statement
Calculate nth Catalan number.

```
Input: n = 3
Output: 5

Catalan sequence: 1, 1, 2, 5, 14, 42, 132, ...
```

### 🎯 Intuition
**Catalan formula:**
- C(n) = C(0)*C(n-1) + C(1)*C(n-2) + ... + C(n-1)*C(0)
- Or: C(n) = (2n)! / ((n+1)! * n!)
- Or: C(n) = C(n-1) * 2*(2n-1) / (n+1)

### Solution 1: DP

```python
def catalanNumber(n: int) -> int:
    """
    DP approach for Catalan numbers.
    
    Logic:
    - C(n) = sum(C(i) * C(n-1-i)) for i in 0..n-1
    
    Time: O(n²)
    Space: O(n)
    """
    if n <= 1:
        return 1
    
    catalan = [0] * (n + 1)
    catalan[0] = catalan[1] = 1
    
    for i in range(2, n + 1):
        for j in range(i):
            catalan[i] += catalan[j] * catalan[i - 1 - j]
    
    return catalan[n]

# Example usage
for i in range(6):
    print(f"C({i}) = {catalanNumber(i)}")
```

### Solution 2: Formula (Binomial Coefficient)

```python
def catalanNumber_formula(n: int) -> int:
    """
    Using binomial coefficient formula.
    
    C(n) = (2n)! / ((n+1)! * n!)
         = C(2n, n) / (n+1)
    
    Time: O(n)
    Space: O(1)
    """
    if n <= 1:
        return 1
    
    # Calculate C(2n, n)
    catalan = 1
    for i in range(n):
        catalan = catalan * (2 * n - i) // (i + 1)
    
    return catalan // (n + 1)
```

### Solution 3: Optimized DP

```python
def catalanNumber_optimized(n: int) -> int:
    """
    Optimized using recurrence.
    
    C(n) = C(n-1) * 2*(2n-1) / (n+1)
    
    Time: O(n)
    Space: O(1)
    """
    if n <= 1:
        return 1
    
    catalan = 1
    for i in range(2, n + 1):
        catalan = catalan * 2 * (2 * i - 1) // (i + 1)
    
    return catalan
```

### Applications

```python
"""
Catalan numbers appear in:
1. Number of valid parentheses combinations
2. Number of binary search trees with n nodes
3. Number of ways to triangulate a polygon
4. Number of paths in grid (not crossing diagonal)
5. Number of full binary trees with n+1 leaves
"""

def numOfBSTrees(n: int) -> int:
    """Number of unique BSTs with n nodes."""
    return catalanNumber_optimized(n)

def numOfParentheses(n: int) -> int:
    """Number of valid n-pair parentheses."""
    return catalanNumber_optimized(n)
```

### ⏱️ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| DP | O(n²) | O(n) | Clear logic |
| Formula | O(n) | O(1) | ⭐ Optimal |
| Optimized DP | O(n) | O(1) | ⭐ Simple |

---

## 🎯 Pattern Summary

### Core Math Patterns

1. **Binary Exponentiation** - Fast power in O(log n)
2. **Modular Arithmetic** - Properties for large numbers
3. **Sieve of Eratosthenes** - Prime generation
4. **Long Division** - Cycle detection with HashMap
5. **Digit DP** - Count patterns in number ranges
6. **Catalan Numbers** - Recursive counting problems

### Problem Categories

| Category | Problems | Key Technique |
|----------|----------|---------------|
| Exponentiation | Pow, Super Pow | Binary exponentiation |
| Division | Divide, Fraction | Bit shifting, long division |
| Primes | Count Primes | Sieve |
| Counting | Digit One, Catalan | DP, formulas |

### Mathematical Properties

```python
# Modular arithmetic
(a + b) % m = ((a % m) + (b % m)) % m
(a * b) % m = ((a % m) * (b % m)) % m
(a^b) % m = ((a % m)^b) % m

# Binary exponentiation
x^n = (x^2)^(n/2) if n even
    = x * x^(n-1) if n odd

# Catalan numbers
C(0) = 1
C(n) = C(0)*C(n-1) + C(1)*C(n-2) + ... + C(n-1)*C(0)
     = (2n)! / ((n+1)! * n!)
     = C(n-1) * 2*(2n-1) / (n+1)

# Sieve complexity
O(n log log n) - near linear

# Digit DP
For position i:
  count = higher * base + adjustment
  adjustment depends on current digit
```

### Common Techniques

1. **Fast Power:** Reduce O(n) to O(log n)
2. **Modulo Operations:** Prevent overflow
3. **Sieve:** Generate primes efficiently
4. **HashMap for Cycles:** Detect repetition
5. **Digit-by-Digit:** Process large numbers
6. **DP vs Formula:** Choose based on constraints

### Interview Tips

1. **Overflow:** Always check for overflow in power/multiplication
2. **Modulo:** Apply at each step, not just at end
3. **Edge Cases:** 0, 1, negative numbers
4. **Optimization:** Can you reduce O(n) to O(log n)?
5. **Mathematical Insight:** Look for patterns and formulas

Master these math patterns for technical interview success! 🚀

