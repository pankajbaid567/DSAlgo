# 📐 Math - Comprehensive Cheatsheet

## 📚 Core Topics

### Number Theory | Geometry | Prime Numbers | Combinatorics | Mathematical Algorithms

---

## Prime Numbers

### Check if Prime
```python
def isPrime(n):
    if n <= 1:
        return False
    if n <= 3:
        return True
    if n % 2 == 0 or n % 3 == 0:
        return False
    
    # Check divisors up to √n
    i = 5
    while i * i <= n:
        if n % i == 0 or n % (i + 2) == 0:
            return False
        i += 6
    
    return True
```

### Sieve of Eratosthenes
```python
def sieveOfEratosthenes(n):
    """Find all primes up to n"""
    if n < 2:
        return []
    
    is_prime = [True] * (n + 1)
    is_prime[0] = is_prime[1] = False
    
    for i in range(2, int(n**0.5) + 1):
        if is_prime[i]:
            for j in range(i*i, n + 1, i):
                is_prime[j] = False
    
    return [i for i in range(n + 1) if is_prime[i]]

# Time: O(n log log n), Space: O(n)
```

### Prime Factorization
```python
def primeFactorization(n):
    factors = []
    
    # Check for 2
    while n % 2 == 0:
        factors.append(2)
        n //= 2
    
    # Check odd factors
    i = 3
    while i * i <= n:
        while n % i == 0:
            factors.append(i)
            n //= i
        i += 2
    
    if n > 2:
        factors.append(n)
    
    return factors

# Count of divisors
def countDivisors(n):
    # Using prime factorization
    # If n = p1^a1 * p2^a2 * ... * pk^ak
    # divisors = (a1+1) * (a2+1) * ... * (ak+1)
    count = 0
    i = 1
    while i * i <= n:
        if n % i == 0:
            count += 1 if i * i == n else 2
        i += 1
    return count
```

---

## GCD and LCM

### Greatest Common Divisor
```python
def gcd(a, b):
    """Euclidean algorithm"""
    while b:
        a, b = b, a % b
    return a

# Built-in (Python 3.5+)
import math
math.gcd(a, b)

# Extended Euclidean Algorithm
def extendedGCD(a, b):
    """Returns (gcd, x, y) where ax + by = gcd(a,b)"""
    if b == 0:
        return a, 1, 0
    
    gcd, x1, y1 = extendedGCD(b, a % b)
    x = y1
    y = x1 - (a // b) * y1
    
    return gcd, x, y
```

### Least Common Multiple
```python
def lcm(a, b):
    return (a * b) // gcd(a, b)

# For multiple numbers
from functools import reduce
def lcm_multiple(numbers):
    return reduce(lcm, numbers)
```

---

## Modular Arithmetic

### Basic Operations
```python
MOD = 10**9 + 7

# Modular addition
def mod_add(a, b, mod=MOD):
    return (a % mod + b % mod) % mod

# Modular subtraction
def mod_sub(a, b, mod=MOD):
    return ((a % mod - b % mod) + mod) % mod

# Modular multiplication
def mod_mul(a, b, mod=MOD):
    return (a % mod * b % mod) % mod

# Modular division (using modular inverse)
def mod_div(a, b, mod=MOD):
    return mod_mul(a, mod_inverse(b, mod), mod)
```

### Modular Exponentiation
```python
def power(base, exp, mod=MOD):
    """Efficient: O(log exp)"""
    result = 1
    base = base % mod
    
    while exp > 0:
        if exp & 1:
            result = (result * base) % mod
        
        exp >>= 1
        base = (base * base) % mod
    
    return result

# Built-in
pow(base, exp, mod)
```

### Modular Inverse
```python
def mod_inverse(a, mod=MOD):
    """Using Fermat's Little Theorem: a^(p-1) ≡ 1 (mod p)"""
    return power(a, mod - 2, mod)

# Using Extended Euclidean Algorithm
def mod_inverse_extended(a, mod):
    gcd, x, y = extendedGCD(a, mod)
    if gcd != 1:
        return None  # Inverse doesn't exist
    return (x % mod + mod) % mod
```

---

## Combinatorics

### Factorial and Combinations
```python
def factorial(n):
    if n <= 1:
        return 1
    return n * factorial(n - 1)

def factorial_iterative(n):
    result = 1
    for i in range(2, n + 1):
        result *= i
    return result

# Combination: C(n, r) = n! / (r! * (n-r)!)
def nCr(n, r):
    if r > n or r < 0:
        return 0
    if r == 0 or r == n:
        return 1
    
    # Optimize: C(n, r) = C(n, n-r)
    r = min(r, n - r)
    
    numerator = 1
    denominator = 1
    
    for i in range(r):
        numerator *= (n - i)
        denominator *= (i + 1)
    
    return numerator // denominator

# Permutation: P(n, r) = n! / (n-r)!
def nPr(n, r):
    if r > n or r < 0:
        return 0
    
    result = 1
    for i in range(n, n - r, -1):
        result *= i
    
    return result

# Pascal's Triangle for computing nCr
def pascalTriangle(n):
    triangle = [[1]]
    
    for i in range(1, n):
        row = [1]
        for j in range(1, i):
            row.append(triangle[i-1][j-1] + triangle[i-1][j])
        row.append(1)
        triangle.append(row)
    
    return triangle
```

### Catalan Numbers
```python
def catalan(n):
    """Cn = C(2n, n) / (n+1)"""
    if n <= 1:
        return 1
    
    catalan = [0] * (n + 1)
    catalan[0] = catalan[1] = 1
    
    for i in range(2, n + 1):
        for j in range(i):
            catalan[i] += catalan[j] * catalan[i-1-j]
    
    return catalan[n]

# Applications:
# - Number of valid parentheses combinations
# - Number of BSTs with n nodes
# - Number of ways to triangulate polygon
```

---

## Fast Exponentiation & Multiplication

### Matrix Exponentiation
```python
def matrix_multiply(A, B):
    """Multiply two 2x2 matrices"""
    return [
        [A[0][0]*B[0][0] + A[0][1]*B[1][0], A[0][0]*B[0][1] + A[0][1]*B[1][1]],
        [A[1][0]*B[0][0] + A[1][1]*B[1][0], A[1][0]*B[0][1] + A[1][1]*B[1][1]]
    ]

def matrix_power(M, n):
    """Compute M^n using fast exponentiation"""
    if n == 1:
        return M
    
    result = [[1, 0], [0, 1]]  # Identity matrix
    
    while n > 0:
        if n & 1:
            result = matrix_multiply(result, M)
        M = matrix_multiply(M, M)
        n >>= 1
    
    return result

# Fibonacci using matrix exponentiation: O(log n)
def fibonacci(n):
    if n <= 1:
        return n
    
    M = [[1, 1], [1, 0]]
    result = matrix_power(M, n - 1)
    return result[0][0]
```

---

## Geometry

### Distance and Area
```python
import math

# Distance between two points
def distance(x1, y1, x2, y2):
    return math.sqrt((x2 - x1)**2 + (y2 - y1)**2)

# Area of triangle (using coordinates)
def triangleArea(x1, y1, x2, y2, x3, y3):
    return abs((x1*(y2-y3) + x2*(y3-y1) + x3*(y1-y2)) / 2)

# Check if three points are collinear
def areCollinear(x1, y1, x2, y2, x3, y3):
    area = triangleArea(x1, y1, x2, y2, x3, y3)
    return area == 0

# Slope of line
def slope(x1, y1, x2, y2):
    if x2 == x1:
        return float('inf')  # Vertical line
    return (y2 - y1) / (x2 - x1)
```

### Convex Hull (Graham Scan)
```python
def orientation(p, q, r):
    """
    Find orientation of ordered triplet (p, q, r)
    Returns:
    0 -> Collinear
    1 -> Clockwise
    2 -> Counterclockwise
    """
    val = (q[1] - p[1]) * (r[0] - q[0]) - (q[0] - p[0]) * (r[1] - q[1])
    
    if val == 0:
        return 0
    return 1 if val > 0 else 2

def convexHull(points):
    n = len(points)
    if n < 3:
        return points
    
    # Find bottom-most point
    l = 0
    for i in range(1, n):
        if points[i][1] < points[l][1]:
            l = i
        elif points[i][1] == points[l][1] and points[i][0] < points[l][0]:
            l = i
    
    hull = []
    p = l
    
    while True:
        hull.append(points[p])
        q = (p + 1) % n
        
        for i in range(n):
            if orientation(points[p], points[i], points[q]) == 2:
                q = i
        
        p = q
        if p == l:
            break
    
    return hull
```

---

## Special Numbers

### Perfect Numbers
```python
def isPerfect(n):
    """Sum of divisors equals n"""
    if n <= 1:
        return False
    
    sum_divisors = 1
    i = 2
    while i * i <= n:
        if n % i == 0:
            sum_divisors += i
            if i != n // i:
                sum_divisors += n // i
        i += 1
    
    return sum_divisors == n
```

### Armstrong Numbers
```python
def isArmstrong(n):
    """Sum of digits raised to power of number of digits equals n"""
    digits = [int(d) for d in str(n)]
    power = len(digits)
    total = sum(d ** power for d in digits)
    return total == n
```

### Happy Numbers
```python
def isHappy(n):
    def get_next(num):
        total = 0
        while num > 0:
            digit = num % 10
            total += digit * digit
            num //= 10
        return total
    
    seen = set()
    
    while n != 1 and n not in seen:
        seen.add(n)
        n = get_next(n)
    
    return n == 1
```

---

## Number Patterns

### Palindrome Number
```python
def isPalindrome(n):
    if n < 0:
        return False
    
    reversed_num = 0
    original = n
    
    while n > 0:
        reversed_num = reversed_num * 10 + n % 10
        n //= 10
    
    return reversed_num == original
```

### Reverse Integer
```python
def reverse(x):
    sign = -1 if x < 0 else 1
    x = abs(x)
    
    reversed_num = 0
    while x:
        reversed_num = reversed_num * 10 + x % 10
        x //= 10
    
    # Check for 32-bit overflow
    if reversed_num > 2**31 - 1:
        return 0
    
    return sign * reversed_num
```

---

## 🎨 Dry Run Examples

### Sieve of Eratosthenes (n=10)
```
Initial: [T, T, T, T, T, T, T, T, T, T, T]
         [0, 1, 2, 3, 4, 5, 6, 7, 8, 9,10]

Mark 0, 1: [F, F, T, T, T, T, T, T, T, T, T]

i=2: Mark 4,6,8,10
     [F, F, T, T, F, T, F, T, F, T, F]

i=3: Mark 9
     [F, F, T, T, F, T, F, T, F, F, F]

Primes: [2, 3, 5, 7]
```

### Fast Exponentiation (3^13)
```
13 in binary: 1101

result = 1, base = 3

Step 1: 13 & 1 = 1 → result = 3, base = 9, exp = 6
Step 2: 6 & 1 = 0 → base = 81, exp = 3
Step 3: 3 & 1 = 1 → result = 243, base = 6561, exp = 1
Step 4: 1 & 1 = 1 → result = 1594323, exp = 0

Result: 1594323 (3^13)
```

---

## ⏱️ Complexity Analysis

| Algorithm | Time | Space |
|-----------|------|-------|
| Prime Check | O(√n) | O(1) |
| Sieve | O(n log log n) | O(n) |
| GCD (Euclidean) | O(log min(a,b)) | O(1) |
| Modular Exponentiation | O(log exp) | O(1) |
| Combination nCr | O(r) | O(1) |
| Matrix Power | O(log n) | O(1) |

---

## 🎯 Must-Know Problems

### Easy
- [7. Reverse Integer](https://leetcode.com/problems/reverse-integer/)
- [9. Palindrome Number](https://leetcode.com/problems/palindrome-number/)
- [202. Happy Number](https://leetcode.com/problems/happy-number/)
- [231. Power of Two](https://leetcode.com/problems/power-of-two/)
- [326. Power of Three](https://leetcode.com/problems/power-of-three/)

### Medium
- [50. Pow(x, n)](https://leetcode.com/problems/powx-n/)
- [62. Unique Paths](https://leetcode.com/problems/unique-paths/)
- [204. Count Primes](https://leetcode.com/problems/count-primes/)
- [279. Perfect Squares](https://leetcode.com/problems/perfect-squares/)
- [365. Water and Jug Problem](https://leetcode.com/problems/water-and-jug-problem/)

### Hard
- [149. Max Points on a Line](https://leetcode.com/problems/max-points-on-a-line/)
- [587. Erect the Fence](https://leetcode.com/problems/erect-the-fence/)

---

## 💡 Pro Tips

1. **Use built-in functions**: `math.gcd()`, `pow()`
2. **Modular arithmetic**: Always take mod at each step
3. **Fast exponentiation**: Essential for large powers
4. **Sieve for multiple primes**: Precompute when needed
5. **Matrix exponentiation**: For linear recurrences (Fibonacci)

---

**Math is fundamental - master these concepts! 🔢**
