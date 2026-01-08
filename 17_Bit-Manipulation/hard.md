# 🔥 Bit Manipulation - Hard Problems Collection

A comprehensive collection of challenging Bit Manipulation problems with complete solutions, XOR tricks, bitmask DP, and detailed explanations.

---

## 📚 Table of Contents

1. [Maximum XOR of Two Numbers in Array](#problem-1-maximum-xor-of-two-numbers-in-array)
2. [Maximum XOR With an Element From Array](#problem-2-maximum-xor-with-an-element-from-array)
3. [Smallest Sufficient Team](#problem-3-smallest-sufficient-team-bitmask-dp)
4. [Number of Valid Words](#problem-4-number-of-valid-words-for-each-puzzle)
5. [Minimum XOR Sum](#problem-5-minimum-xor-sum-of-two-arrays)
6. [Gray Code](#problem-6-gray-code)
7. [Reverse Bits Advanced](#problem-7-reverse-bits-advanced-problems)
8. [Pattern Summary](#-pattern-summary)

---

## Problem 1: Maximum XOR of Two Numbers in Array

**LeetCode 421 - Medium/Hard**

### Problem Statement
Find maximum XOR of two numbers in array.

```
Input: nums = [3,10,5,25,2,8]
Output: 28
Explanation: 5 XOR 25 = 28
```

### 🎯 Intuition
**Trie (prefix tree) approach:**
- Build binary trie of all numbers
- For each number, find best match by:
  - Trying opposite bit at each level
  - Maximizes XOR result

**Key insight:** To maximize XOR, choose opposite bit when possible.

### 📊 Visual Representation

```
nums = [3, 10, 5, 25]
Binary (5-bit):
  3:  00011
  10: 01010
  5:  00101
  25: 11001

Trie structure:
           root
          /    \
         0      1
        / \      \
       0   1      1
      / \ / \      \
     0  1 0  1      0
    /  / | \  \      \
   1  1  1  0  0      0
      |     |          \
      1     1           1

Finding max XOR for 5 (00101):
  Start at root, want opposite of each bit:
    Bit 4 (0): Try 1 → found! Go to 1
    Bit 3 (0): Try 1 → found! Go to 1
    Bit 2 (1): Try 0 → found! Go to 0
    Bit 1 (0): Try 1 → not found, go to 0
    Bit 0 (1): Try 0 → not found, go to 1
  
  Found: 11001 (25)
  5 XOR 25 = 00101 XOR 11001 = 11100 = 28
```

### Solution 1: Trie

```python
class TrieNode:
    def __init__(self):
        self.children = {}

def findMaximumXOR(nums):
    """
    Trie-based XOR maximization.
    
    Logic:
    - Build binary trie from all numbers
    - For each number, find best match
    - At each bit, try opposite direction
    
    Time: O(n * 32) = O(n)
    Space: O(n * 32) = O(n)
    """
    root = TrieNode()
    
    # Build trie
    for num in nums:
        node = root
        for i in range(31, -1, -1):
            bit = (num >> i) & 1
            if bit not in node.children:
                node.children[bit] = TrieNode()
            node = node.children[bit]
    
    max_xor = 0
    
    # Find maximum XOR for each number
    for num in nums:
        node = root
        current_xor = 0
        
        for i in range(31, -1, -1):
            bit = (num >> i) & 1
            toggle_bit = 1 - bit
            
            if toggle_bit in node.children:
                current_xor |= (1 << i)
                node = node.children[toggle_bit]
            else:
                node = node.children[bit]
        
        max_xor = max(max_xor, current_xor)
    
    return max_xor

# Example usage
print(findMaximumXOR([3,10,5,25,2,8]))  # Output: 28
```

### Solution 2: Greedy with HashSet

```python
def findMaximumXOR_hash(nums):
    """
    Greedy bit-by-bit construction.
    
    Logic:
    - Build answer from MSB to LSB
    - At each bit position, try to set it to 1
    - Use property: a XOR b = c → a XOR c = b
    
    Time: O(n * 32) = O(n)
    Space: O(n)
    """
    max_xor = 0
    mask = 0
    
    for i in range(31, -1, -1):
        mask |= (1 << i)
        prefixes = {num & mask for num in nums}
        
        # Try to set current bit to 1
        temp = max_xor | (1 << i)
        
        for prefix in prefixes:
            if temp ^ prefix in prefixes:
                max_xor = temp
                break
    
    return max_xor
```

### 🔍 Dry Run (Trie)

```
nums = [3, 10, 5]

Build Trie:
  3 = 00011:  0→0→0→1→1
  10 = 01010: 0→1→0→1→0
  5 = 00101:  0→0→1→0→1

Find max XOR for 5 (00101):
  Bit 4 (0): Want 1? No children with 1, use 0
  Bit 3 (0): Want 1? Yes! Use 1, XOR |= 8
  Bit 2 (1): Want 0? Yes! Use 0, XOR |= 4
  Bit 1 (0): Want 1? Yes! Use 1, XOR |= 2
  Bit 0 (1): Want 0? Yes! Use 0, XOR |= 0
  
  current_xor = 8 + 4 + 2 = 14
  Found number: 01010 (10)
  5 XOR 10 = 15

Continue for all numbers...
Max XOR = 15
```

### ⏱️ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Trie | O(n) | O(n) | ⭐ Clear logic |
| HashSet | O(n) | O(n) | Clever greedy |

---

## Problem 2: Maximum XOR With an Element From Array

**LeetCode 1707 - Hard**

### Problem Statement
Answer queries: maximize nums[j] XOR x where nums[j] ≤ m.

```
Input: nums = [0,1,2,3,4], queries = [[3,1],[1,3],[5,6]]
Output: [3,3,7]
```

### Solution

```python
class TrieNode:
    def __init__(self):
        self.children = {}

def maximizeXor(nums, queries):
    """
    Offline queries with sorted processing.
    
    Logic:
    - Sort nums and queries by limit
    - Process queries in order
    - Add numbers to trie as limits increase
    
    Time: O((n + q) * 32)
    Space: O(n * 32)
    """
    nums.sort()
    
    # Add indices to queries and sort by m
    indexed_queries = [(m, x, i) for i, (x, m) in enumerate(queries)]
    indexed_queries.sort()
    
    root = TrieNode()
    result = [-1] * len(queries)
    nums_idx = 0
    
    def insert(num):
        node = root
        for i in range(31, -1, -1):
            bit = (num >> i) & 1
            if bit not in node.children:
                node.children[bit] = TrieNode()
            node = node.children[bit]
    
    def find_max_xor(x):
        if not root.children:
            return -1
        
        node = root
        max_xor = 0
        
        for i in range(31, -1, -1):
            bit = (x >> i) & 1
            toggle = 1 - bit
            
            if toggle in node.children:
                max_xor |= (1 << i)
                node = node.children[toggle]
            elif bit in node.children:
                node = node.children[bit]
            else:
                return -1
        
        return max_xor
    
    for m, x, idx in indexed_queries:
        # Add all numbers <= m to trie
        while nums_idx < len(nums) and nums[nums_idx] <= m:
            insert(nums[nums_idx])
            nums_idx += 1
        
        result[idx] = find_max_xor(x)
    
    return result

# Example usage
print(maximizeXor([0,1,2,3,4], [[3,1],[1,3],[5,6]]))
# Output: [3,3,7]
```

### ⏱️ Complexity Analysis

| Metric | Complexity | Notes |
|--------|------------|-------|
| Time | O((n+q)*32) | Process queries |
| Space | O(n*32) | Trie |

---

## Problem 3: Smallest Sufficient Team (Bitmask DP)

**LeetCode 1125 - Hard**

### Problem Statement
Find smallest team with all required skills.

```
Input: req_skills = ["java","nodejs","reactjs"]
       people = [["java"],["nodejs"],["nodejs","reactjs"]]
Output: [0,2]
```

### 🎯 Intuition
**Bitmask DP:**
- State: bitmask of skills covered
- DP[mask] = smallest team for skills in mask
- Transition: try adding each person

### Solution

```python
def smallestSufficientTeam(req_skills, people):
    """
    Bitmask DP for subset selection.
    
    Logic:
    - Each skill → bit position
    - Each person → bitmask of skills
    - DP[mask] = min team for that skill set
    
    Time: O(2^n * m) where n=skills, m=people
    Space: O(2^n)
    """
    n = len(req_skills)
    skill_to_id = {skill: i for i, skill in enumerate(req_skills)}
    
    # Convert people to bitmasks
    people_masks = []
    for person in people:
        mask = 0
        for skill in person:
            if skill in skill_to_id:
                mask |= (1 << skill_to_id[skill])
        people_masks.append(mask)
    
    # DP[mask] = (team_size, team_indices)
    target = (1 << n) - 1
    dp = {0: (0, [])}
    
    for i, person_mask in enumerate(people_masks):
        if person_mask == 0:
            continue
        
        for prev_mask in list(dp.keys()):
            new_mask = prev_mask | person_mask
            
            if new_mask == prev_mask:
                continue
            
            new_team = dp[prev_mask][1] + [i]
            
            if new_mask not in dp or len(new_team) < dp[new_mask][0]:
                dp[new_mask] = (len(new_team), new_team)
    
    return dp[target][1] if target in dp else []

# Example usage
print(smallestSufficientTeam(
    ["java","nodejs","reactjs"],
    [["java"],["nodejs"],["nodejs","reactjs"]]
))
# Output: [0,2]
```

### ⏱️ Complexity Analysis

| Metric | Complexity | Notes |
|--------|------------|-------|
| Time | O(2^n * m) | n≤16 typically |
| Space | O(2^n) | DP states |

---

## Problem 4: Number of Valid Words for Each Puzzle

**LeetCode 1178 - Hard**

### Problem Statement
Count words matching puzzle criteria (first letter + subset of other letters).

```
Input: words = ["aaaa","asas","able","ability","actt","actor","access"]
       puzzles = ["aboveyz","abrodyz","abslute","absoryz","actresz","gaswxyz"]
Output: [1,1,3,2,4,0]
```

### Solution

```python
from collections import defaultdict

def findNumOfValidWords(words, puzzles):
    """
    Bitmask with enumeration.
    
    Logic:
    - Convert words to bitmask
    - For each puzzle, enumerate subsets
    - Check if first letter matches
    
    Time: O(W + P * 2^7)
    Space: O(W)
    """
    # Count words by bitmask
    word_masks = defaultdict(int)
    
    for word in words:
        mask = 0
        for char in word:
            mask |= 1 << (ord(char) - ord('a'))
        # Only count if ≤ 7 distinct letters
        if bin(mask).count('1') <= 7:
            word_masks[mask] += 1
    
    result = []
    
    for puzzle in puzzles:
        # Create puzzle mask
        puzzle_mask = 0
        for char in puzzle:
            puzzle_mask |= 1 << (ord(char) - ord('a'))
        
        first_bit = 1 << (ord(puzzle[0]) - ord('a'))
        count = 0
        
        # Enumerate all subsets of puzzle
        submask = puzzle_mask
        while submask:
            # Check if first letter present
            if submask & first_bit:
                count += word_masks[submask]
            submask = (submask - 1) & puzzle_mask
        
        result.append(count)
    
    return result

# Example usage
words = ["aaaa","asas","able","ability","actt","actor","access"]
puzzles = ["aboveyz","abrodyz","abslute","absoryz","actresz","gaswxyz"]
print(findNumOfValidWords(words, puzzles))
```

### ⏱️ Complexity Analysis

| Metric | Complexity | Notes |
|--------|------------|-------|
| Time | O(W + P*2^7) | 2^7 = 128 |
| Space | O(W) | HashMap |

---

## Problem 5: Minimum XOR Sum of Two Arrays

**LeetCode 1879 - Hard**

### Problem Statement
Rearrange nums2 to minimize sum of (nums1[i] XOR nums2[i]).

```
Input: nums1 = [1,2], nums2 = [2,3]
Output: 2
Explanation: Arrange as [2,3]: (1^2)+(2^3) = 3+1 = 4
            Arrange as [3,2]: (1^3)+(2^2) = 2+0 = 2
```

### Solution

```python
def minimumXORSum(nums1, nums2):
    """
    Bitmask DP for assignment.
    
    Logic:
    - State: which nums2 elements used
    - DP[mask] = min sum using elements in mask
    - Try assigning each remaining element
    
    Time: O(2^n * n)
    Space: O(2^n)
    """
    n = len(nums1)
    dp = [float('inf')] * (1 << n)
    dp[0] = 0
    
    for mask in range(1 << n):
        if dp[mask] == float('inf'):
            continue
        
        # Number of elements assigned so far
        i = bin(mask).count('1')
        
        if i == n:
            continue
        
        # Try assigning each unused element from nums2
        for j in range(n):
            if mask & (1 << j):
                continue
            
            new_mask = mask | (1 << j)
            dp[new_mask] = min(dp[new_mask], dp[mask] + (nums1[i] ^ nums2[j]))
    
    return dp[(1 << n) - 1]

# Example usage
print(minimumXORSum([1,2], [2,3]))  # Output: 2
```

### ⏱️ Complexity Analysis

| Metric | Complexity | Notes |
|--------|------------|-------|
| Time | O(2^n * n) | n≤14 typically |
| Space | O(2^n) | DP array |

---

## Problem 6: Gray Code

**LeetCode 89 - Medium/Hard**

### Problem Statement
Generate n-bit Gray code sequence.

```
Input: n = 2
Output: [0,1,3,2]
Explanation: 
  00 → 0
  01 → 1
  11 → 3
  10 → 2
```

### 🎯 Intuition
**Gray code property:** Adjacent codes differ by 1 bit.

**Construction methods:**
1. **Recursive:** G(n) = [0 + G(n-1)] + [1 + reverse(G(n-1))]
2. **Formula:** gray(i) = i XOR (i >> 1)

### Solution 1: Recursive

```python
def grayCode(n):
    """
    Recursive Gray code generation.
    
    Logic:
    - Base: G(1) = [0, 1]
    - Recursive: Prepend 0 to G(n-1), prepend 1 to reverse(G(n-1))
    
    Time: O(2^n)
    Space: O(2^n)
    """
    if n == 0:
        return [0]
    
    if n == 1:
        return [0, 1]
    
    prev = grayCode(n - 1)
    result = prev[:]
    
    for code in reversed(prev):
        result.append(code | (1 << (n - 1)))
    
    return result

# Example usage
print(grayCode(2))  # [0, 1, 3, 2]
print(grayCode(3))  # [0,1,3,2,6,7,5,4]
```

### Solution 2: Formula

```python
def grayCode_formula(n):
    """
    Direct formula: G(i) = i XOR (i >> 1)
    
    Time: O(2^n)
    Space: O(2^n)
    """
    return [i ^ (i >> 1) for i in range(1 << n)]
```

### 🔍 Dry Run

```
n = 3

Recursive approach:
  G(1) = [0, 1]
  
  G(2):
    prev = [0, 1]
    Add 0-prefix: [0, 1]
    Add 1-prefix to reverse: [11, 10] = [3, 2]
    Result: [0, 1, 3, 2]
  
  G(3):
    prev = [0, 1, 3, 2]
    Add 0-prefix: [0, 1, 3, 2]
    Add 1-prefix to reverse: [110, 111, 101, 100] = [6, 7, 5, 4]
    Result: [0, 1, 3, 2, 6, 7, 5, 4]

Formula approach:
  i=0: 0 XOR 0 = 0
  i=1: 1 XOR 0 = 1
  i=2: 2 XOR 1 = 10 XOR 01 = 11 = 3
  i=3: 3 XOR 1 = 11 XOR 01 = 10 = 2
  i=4: 4 XOR 2 = 100 XOR 010 = 110 = 6
  i=5: 5 XOR 2 = 101 XOR 010 = 111 = 7
  i=6: 6 XOR 3 = 110 XOR 011 = 101 = 5
  i=7: 7 XOR 3 = 111 XOR 011 = 100 = 4
```

### ⏱️ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Recursive | O(2^n) | O(2^n) | Clear pattern |
| Formula | O(2^n) | O(2^n) | ⭐ Direct |

---

## Problem 7: Reverse Bits Advanced Problems

**LeetCode 190 + Variations**

### Problem Statement
Reverse bits of 32-bit unsigned integer.

```
Input: n = 00000010100101000001111010011100
Output:    964176192 (00111001011110000010100101000000)
```

### Solution 1: Bit by Bit

```python
def reverseBits(n):
    """
    Reverse bits one by one.
    
    Time: O(32) = O(1)
    Space: O(1)
    """
    result = 0
    for i in range(32):
        result <<= 1
        result |= (n & 1)
        n >>= 1
    return result

# Example usage
print(reverseBits(43261596))  # 964176192
```

### Solution 2: Divide and Conquer

```python
def reverseBits_dc(n):
    """
    Divide and conquer bit reversal.
    
    Time: O(log 32) = O(1)
    Space: O(1)
    """
    # Swap adjacent bits
    n = ((n & 0xAAAAAAAA) >> 1) | ((n & 0x55555555) << 1)
    # Swap adjacent 2-bit groups
    n = ((n & 0xCCCCCCCC) >> 2) | ((n & 0x33333333) << 2)
    # Swap adjacent 4-bit groups
    n = ((n & 0xF0F0F0F0) >> 4) | ((n & 0x0F0F0F0F) << 4)
    # Swap adjacent bytes
    n = ((n & 0xFF00FF00) >> 8) | ((n & 0x00FF00FF) << 8)
    # Swap halves
    n = (n >> 16) | (n << 16)
    
    return n & 0xFFFFFFFF
```

### Solution 3: With Memoization

```python
def reverseBits_memo(n):
    """
    Cache reversed bytes for O(1) lookup.
    
    Time: O(1) after preprocessing
    Space: O(256)
    """
    # Precompute reversed bytes
    if not hasattr(reverseBits_memo, 'cache'):
        cache = {}
        for i in range(256):
            rev = 0
            for j in range(8):
                rev <<= 1
                rev |= (i & 1)
                i >>= 1
            cache[i] = rev
        reverseBits_memo.cache = cache
    
    cache = reverseBits_memo.cache
    
    # Reverse each byte and combine
    result = 0
    for i in range(4):
        result <<= 8
        result |= cache[n & 0xFF]
        n >>= 8
    
    return result
```

### ⏱️ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Bit by Bit | O(1) | O(1) | Simple |
| Divide & Conquer | O(1) | O(1) | ⭐ Fastest |
| Memoization | O(1) | O(256) | Good for multiple calls |

---

## 🎯 Pattern Summary

### Core Bit Manipulation Patterns

1. **XOR Maximization** - Use Trie for greedy bit selection
2. **Bitmask DP** - State compression for subsets
3. **Subset Enumeration** - Iterate through all submasks
4. **Gray Code** - Recursive or formula (i XOR (i>>1))
5. **Bit Reversal** - Divide and conquer swapping

### Key Techniques

| Technique | Use Case | Complexity |
|-----------|----------|------------|
| Trie | XOR maximization | O(n * 32) |
| Bitmask DP | Subset selection | O(2^n * n) |
| Submask iteration | Subset matching | O(3^n) total |
| XOR properties | Pairing, cancellation | O(n) |
| Bit tricks | Fast operations | O(1) |

### Useful Bit Tricks

```python
# Check if power of 2
n & (n - 1) == 0

# Count set bits
bin(n).count('1')
# Or: popcount

# Get rightmost set bit
n & (-n)

# Clear rightmost set bit
n & (n - 1)

# Set ith bit
n | (1 << i)

# Clear ith bit
n & ~(1 << i)

# Toggle ith bit
n ^ (1 << i)

# Check if ith bit set
(n >> i) & 1

# Iterate through submasks of mask
submask = mask
while submask:
    # Process submask
    submask = (submask - 1) & mask
```

### Problem-Solving Checklist

1. **XOR Problems:**
   - Look for pairing/cancellation
   - Consider Trie for maximization
   - Use prefix XOR for ranges

2. **Subset Problems:**
   - Use bitmask if n ≤ 20
   - Consider DP on subsets
   - Enumerate submasks when needed

3. **Bit Reversal/Manipulation:**
   - Divide and conquer for speed
   - Memoize if multiple calls
   - Use lookup tables for bytes

4. **Optimization:**
   - Offline queries with sorting
   - Trie for prefix operations
   - DP for subset problems

Master these bit manipulation patterns for interview success! 🚀

