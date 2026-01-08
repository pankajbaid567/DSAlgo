# 🔢 Bit Manipulation - Comprehensive Cheatsheet

## 📚 Core Concept

**Bit Manipulation**: Operations on binary representation of numbers for efficient computation.

### Why Use Bit Manipulation?
✅ **Speed**: Faster than arithmetic operations  
✅ **Space**: Compact representation  
✅ **Clever solutions**: XOR tricks, bit masking  
✅ **Low-level operations**: System programming  

---

## Basic Operations

### Binary Representation
```python
# Decimal to Binary
bin(5)          # '0b101'
bin(5)[2:]      # '101'
format(5, 'b')  # '101'

# Binary to Decimal
int('101', 2)   # 5

# Check if number is power of 2
def isPowerOfTwo(n):
    return n > 0 and (n & (n - 1)) == 0
```

### Bitwise Operators

| Operator | Symbol | Example | Result |
|----------|--------|---------|--------|
| AND | & | 5 & 3 | 1 |
| OR | \| | 5 \| 3 | 7 |
| XOR | ^ | 5 ^ 3 | 6 |
| NOT | ~ | ~5 | -6 |
| Left Shift | << | 5 << 1 | 10 |
| Right Shift | >> | 5 >> 1 | 2 |

```python
# AND: Both bits must be 1
5 & 3  # 0101 & 0011 = 0001 = 1

# OR: At least one bit is 1
5 | 3  # 0101 | 0011 = 0111 = 7

# XOR: Bits are different
5 ^ 3  # 0101 ^ 0011 = 0110 = 6

# NOT: Flip all bits (two's complement)
~5     # ~0101 = 1010 (in two's complement) = -6

# Left Shift: Multiply by 2^n
5 << 1 # 0101 << 1 = 1010 = 10 (5 * 2)
5 << 2 # 0101 << 2 = 10100 = 20 (5 * 4)

# Right Shift: Divide by 2^n
5 >> 1 # 0101 >> 1 = 0010 = 2 (5 // 2)
5 >> 2 # 0101 >> 2 = 0001 = 1 (5 // 4)
```

---

## Essential Bit Tricks

### Get, Set, Clear, Toggle Bit

```python
def getBit(num, i):
    """Get i-th bit (0-indexed from right)"""
    return (num >> i) & 1

def setBit(num, i):
    """Set i-th bit to 1"""
    return num | (1 << i)

def clearBit(num, i):
    """Clear i-th bit (set to 0)"""
    mask = ~(1 << i)
    return num & mask

def toggleBit(num, i):
    """Toggle i-th bit"""
    return num ^ (1 << i)

def updateBit(num, i, bit_value):
    """Update i-th bit to bit_value (0 or 1)"""
    mask = ~(1 << i)
    return (num & mask) | (bit_value << i)

# Examples
num = 5  # 0101
print(getBit(num, 2))      # 1
print(setBit(num, 1))      # 7 (0111)
print(clearBit(num, 2))    # 1 (0001)
print(toggleBit(num, 0))   # 4 (0100)
```

### Clear Bits

```python
def clearBitsFromMSBToI(num, i):
    """Clear all bits from MSB to i (inclusive)"""
    mask = (1 << i) - 1
    return num & mask

def clearBitsFromITo0(num, i):
    """Clear all bits from i to 0 (inclusive)"""
    mask = ~((1 << (i + 1)) - 1)
    return num & mask

# Example
num = 15  # 1111
print(clearBitsFromMSBToI(num, 2))  # 3 (0011)
print(clearBitsFromITo0(num, 2))    # 8 (1000)
```

---

## Common Patterns

### Pattern 1: Single Number Problems

```python
# Single number (all others appear twice)
def singleNumber(nums):
    """XOR cancels out pairs"""
    result = 0
    for num in nums:
        result ^= num
    return result

# Single number II (all others appear thrice)
def singleNumberII(nums):
    ones = twos = 0
    
    for num in nums:
        ones = (ones ^ num) & ~twos
        twos = (twos ^ num) & ~ones
    
    return ones

# Single number III (two numbers appear once)
def singleNumberIII(nums):
    xor = 0
    for num in nums:
        xor ^= num
    
    # Find rightmost set bit
    rightmost_bit = xor & -xor
    
    # Divide into two groups
    num1 = num2 = 0
    for num in nums:
        if num & rightmost_bit:
            num1 ^= num
        else:
            num2 ^= num
    
    return [num1, num2]
```

### Pattern 2: Counting Bits

```python
# Count set bits (Hamming Weight)
def countBits(n):
    count = 0
    while n:
        count += n & 1
        n >>= 1
    return count

# Brian Kernighan's Algorithm (faster)
def countBitsOptimized(n):
    count = 0
    while n:
        n &= n - 1  # Removes rightmost set bit
        count += 1
    return count

# Count bits for range [0, n]
def countBitsRange(n):
    result = [0] * (n + 1)
    for i in range(1, n + 1):
        result[i] = result[i >> 1] + (i & 1)
    return result

# Is power of 4
def isPowerOfFour(n):
    # Power of 2 AND only odd positions have 1
    return n > 0 and (n & (n - 1)) == 0 and (n & 0x55555555) != 0
```

### Pattern 3: Bitwise Subset Generation

```python
# All subsets using bit manipulation
def subsets(nums):
    n = len(nums)
    result = []
    
    # 2^n subsets
    for i in range(1 << n):
        subset = []
        for j in range(n):
            if i & (1 << j):
                subset.append(nums[j])
        result.append(subset)
    
    return result

# Iterate through all subsets of a mask
def iterateSubsets(mask):
    subsets = []
    subset = mask
    
    while subset > 0:
        subsets.append(subset)
        subset = (subset - 1) & mask
    
    subsets.append(0)
    return subsets

# Example: For mask = 5 (101), subsets are: 5, 4, 1, 0
```

### Pattern 4: XOR Properties

```python
# Properties of XOR:
# 1. a ^ a = 0
# 2. a ^ 0 = a
# 3. a ^ b = b ^ a (commutative)
# 4. (a ^ b) ^ c = a ^ (b ^ c) (associative)

# Missing number (0 to n)
def missingNumber(nums):
    n = len(nums)
    xor = 0
    
    for i in range(n + 1):
        xor ^= i
    
    for num in nums:
        xor ^= num
    
    return xor

# Find duplicate
def findDuplicate(nums):
    xor = 0
    for i in range(1, len(nums)):
        xor ^= i
        xor ^= nums[i]
    xor ^= nums[0]
    return xor
```

### Pattern 5: Bit Masking for DP

```python
# Traveling Salesman Problem (TSP) using bitmask
def tsp(graph, pos, visited, n):
    if visited == (1 << n) - 1:
        return graph[pos][0]  # Return to start
    
    if (pos, visited) in memo:
        return memo[(pos, visited)]
    
    ans = float('inf')
    
    for city in range(n):
        if not (visited & (1 << city)):
            new_visited = visited | (1 << city)
            ans = min(ans, graph[pos][city] + 
                     tsp(graph, city, new_visited, n))
    
    memo[(pos, visited)] = ans
    return ans

# Partition into equal sum subsets
def canPartitionKSubsets(nums, k):
    total = sum(nums)
    if total % k != 0:
        return False
    
    target = total // k
    n = len(nums)
    memo = {}
    
    def backtrack(mask, curr_sum):
        if mask == (1 << n) - 1:
            return True
        
        if mask in memo:
            return memo[mask]
        
        if curr_sum == target:
            curr_sum = 0
        
        for i in range(n):
            if not (mask & (1 << i)) and curr_sum + nums[i] <= target:
                if backtrack(mask | (1 << i), curr_sum + nums[i]):
                    memo[mask] = True
                    return True
        
        memo[mask] = False
        return False
    
    return backtrack(0, 0)
```

### Pattern 6: Reverse Bits

```python
def reverseBits(n):
    result = 0
    
    for i in range(32):
        # Get rightmost bit
        bit = n & 1
        # Shift result left and add bit
        result = (result << 1) | bit
        # Shift n right
        n >>= 1
    
    return result

# Optimized using lookup table
def reverseBitsOptimized(n):
    result = 0
    for i in range(4):
        result <<= 8
        result |= reverse_byte(n & 0xFF)
        n >>= 8
    return result
```

### Pattern 7: Addition Without + Operator

```python
def getSum(a, b):
    # 32-bit mask
    mask = 0xFFFFFFFF
    
    while b != 0:
        # Carry
        carry = (a & b) << 1
        # Sum without carry
        a = (a ^ b) & mask
        b = carry & mask
    
    # Handle negative numbers
    return a if a <= 0x7FFFFFFF else ~(a ^ mask)

def multiply(a, b):
    """Multiply using bit manipulation"""
    result = 0
    
    while b:
        if b & 1:
            result += a
        a <<= 1
        b >>= 1
    
    return result

def divide(dividend, divisor):
    """Divide using bit manipulation"""
    if dividend == -2**31 and divisor == -1:
        return 2**31 - 1
    
    negative = (dividend < 0) != (divisor < 0)
    dividend, divisor = abs(dividend), abs(divisor)
    
    result = 0
    
    for i in range(31, -1, -1):
        if (dividend >> i) >= divisor:
            result += 1 << i
            dividend -= divisor << i
    
    return -result if negative else result
```

### Pattern 8: UTF-8 Validation

```python
def validUtf8(data):
    n_bytes = 0
    
    for num in data:
        if n_bytes == 0:
            # Count leading 1s
            if (num >> 5) == 0b110:
                n_bytes = 1
            elif (num >> 4) == 0b1110:
                n_bytes = 2
            elif (num >> 3) == 0b11110:
                n_bytes = 3
            elif (num >> 7):
                return False
        else:
            # Check if it starts with 10
            if (num >> 6) != 0b10:
                return False
            n_bytes -= 1
    
    return n_bytes == 0
```

### Pattern 9: Gray Code

```python
def grayCode(n):
    """Generate Gray code sequence"""
    result = []
    
    for i in range(1 << n):
        # Gray code: i ^ (i >> 1)
        result.append(i ^ (i >> 1))
    
    return result

# Example: n=2
# Binary: 00, 01, 10, 11
# Gray:   00, 01, 11, 10
```

### Pattern 10: Maximum XOR

```python
def findMaximumXOR(nums):
    """Maximum XOR of two numbers"""
    max_xor = 0
    mask = 0
    
    # Build mask bit by bit from left
    for i in range(31, -1, -1):
        mask |= (1 << i)
        prefixes = {num & mask for num in nums}
        
        temp = max_xor | (1 << i)
        
        # Check if temp can be achieved
        for prefix in prefixes:
            if temp ^ prefix in prefixes:
                max_xor = temp
                break
    
    return max_xor
```

---

## 🎨 Dry Run Example

### Single Number II (Three Times)

```
Input: [2, 2, 3, 2]
Goal: Find number appearing once (3)

Binary representation:
2: 010
2: 010
3: 011
2: 010

Step-by-step (using ones and twos):
Initial: ones=000, twos=000

Process 2 (010):
  ones = (000 ^ 010) & ~000 = 010
  twos = (000 ^ 010) & ~010 = 000

Process 2 (010):
  ones = (010 ^ 010) & ~000 = 000
  twos = (000 ^ 010) & ~000 = 010

Process 3 (011):
  ones = (000 ^ 011) & ~010 = 001
  twos = (010 ^ 011) & ~001 = 000

Process 2 (010):
  ones = (001 ^ 010) & ~000 = 011
  twos = (000 ^ 010) & ~011 = 010
  Wait! After processing thrice, bits reset

Final: ones = 011 = 3 ✓
```

---

## ⏱️ Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Get/Set/Clear Bit | O(1) | O(1) |
| Count Bits | O(log n) | O(1) |
| Generate Subsets | O(2^n * n) | O(1) |
| XOR Operations | O(n) | O(1) |
| Reverse Bits | O(log n) | O(1) |

---

## 🎯 Must-Know Problems

### Easy
- [136. Single Number](https://leetcode.com/problems/single-number/)
- [191. Number of 1 Bits](https://leetcode.com/problems/number-of-1-bits/)
- [231. Power of Two](https://leetcode.com/problems/power-of-two/)
- [268. Missing Number](https://leetcode.com/problems/missing-number/)
- [338. Counting Bits](https://leetcode.com/problems/counting-bits/)
- [389. Find the Difference](https://leetcode.com/problems/find-the-difference/)

### Medium
- [137. Single Number II](https://leetcode.com/problems/single-number-ii/)
- [260. Single Number III](https://leetcode.com/problems/single-number-iii/)
- [371. Sum of Two Integers](https://leetcode.com/problems/sum-of-two-integers/)
- [421. Maximum XOR of Two Numbers](https://leetcode.com/problems/maximum-xor-of-two-numbers-in-an-array/)
- [78. Subsets](https://leetcode.com/problems/subsets/)
- [89. Gray Code](https://leetcode.com/problems/gray-code/)

### Hard
- [41. First Missing Positive](https://leetcode.com/problems/first-missing-positive/)
- [318. Maximum Product of Word Lengths](https://leetcode.com/problems/maximum-product-of-word-lengths/)

---

## 💡 Pro Tips

1. **XOR for pairs**: Use XOR to find single elements
2. **n & (n-1)**: Removes rightmost set bit
3. **n & -n**: Isolates rightmost set bit
4. **Bit masking**: Represent subsets/states
5. **Left shift = *2, Right shift = /2**
6. **Use 0x55555555 for odd bits, 0xAAAAAAAA for even bits**

---

## 🔥 Common Bit Tricks

```python
# Check if odd
n & 1 == 1

# Multiply by 2^k
n << k

# Divide by 2^k
n >> k

# Check if power of 2
n > 0 and (n & (n - 1)) == 0

# Get rightmost set bit
n & -n

# Remove rightmost set bit
n & (n - 1)

# Get all 1s
~0

# Swap without temp
a ^= b
b ^= a
a ^= b

# Check if two numbers have opposite signs
(a ^ b) < 0
```

---

**Bit manipulation is powerful - master these tricks! ⚡**
