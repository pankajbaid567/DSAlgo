# 🔥 Two Pointers - Hard Problems Collection

## Problem 1: [76. Minimum Window Substring](https://leetcode.com/problems/minimum-window-substring/)

### Problem Statement
Given strings `s` and `t`, return the minimum window substring of `s` such that every character in `t` (including duplicates) is included. If no such substring exists, return "".

### Intuition
**Dynamic Sliding Window**:
1. Expand window by moving right pointer
2. When valid window found, shrink from left
3. Track minimum window
4. Use two HashMaps: target counts and window counts

### Visual Representation
```
s = "ADOBECODEBANC", t = "ABC"

Window expansion:
"A" → "AD" → "ADO" → "ADOB" → "ADOBE" → "ADOBEC" ✓ (valid)

Window shrink:
"ADOBEC" → "DOBEC" (not valid, missing A)
Continue expanding...

"ODEBANC" ✓ (valid)
"DEBANC" (not valid)

Minimum: "BANC" (length 4)
```

### Solution
```python
from collections import Counter

def minWindow(s, t):
    if not s or not t:
        return ""
    
    # Count characters in t
    target_count = Counter(t)
    required = len(target_count)
    
    # Window tracking
    window_count = {}
    formed = 0  # Number of unique chars with desired frequency
    
    left = 0
    min_len = float('inf')
    min_left = 0
    
    for right in range(len(s)):
        char = s[right]
        window_count[char] = window_count.get(char, 0) + 1
        
        # Check if current char frequency matches target
        if char in target_count and window_count[char] == target_count[char]:
            formed += 1
        
        # Try to shrink window
        while formed == required and left <= right:
            # Update result
            if right - left + 1 < min_len:
                min_len = right - left + 1
                min_left = left
            
            # Shrink from left
            char = s[left]
            window_count[char] -= 1
            if char in target_count and window_count[char] < target_count[char]:
                formed -= 1
            
            left += 1
    
    return "" if min_len == float('inf') else s[min_left:min_left + min_len]
```

### Dry Run
```
s = "ADOBECODEBANC", t = "ABC"

target_count = {'A': 1, 'B': 1, 'C': 1}, required = 3

right=0: char='A', window={'A':1}, formed=1
right=1: char='D', window={'A':1,'D':1}, formed=1
right=2: char='O', window={'A':1,'D':1,'O':1}, formed=1
right=3: char='B', window={'A':1,'D':1,'O':1,'B':1}, formed=2
right=4: char='E', window={'A':1,'D':1,'O':1,'B':1,'E':1}, formed=2
right=5: char='C', window={'A':1,'D':1,'O':1,'B':1,'E':1,'C':1}, formed=3 ✓

Shrink:
left=0: min_len=6, remove 'A', formed=2 (break shrink)

right=6: char='O', continue expanding...
...eventually find "BANC" with length 4
```

**Time**: O(|S| + |T|), **Space**: O(|S| + |T|)

---

## Problem 2: [42. Trapping Rain Water](https://leetcode.com/problems/trapping-rain-water/)

### Problem Statement
Given `n` non-negative integers representing elevation map where width of each bar is 1, compute how much water can be trapped after raining.

### Intuition
**Two Pointers from both ends**:
- Water trapped at position = min(max_left, max_right) - height[i]
- Use two pointers moving towards each other
- Track max height from both sides

### Visual Representation
```
Input: [0,1,0,2,1,0,1,3,2,1,2,1]

Visual:
      █
  █   ██ █ █
 ██ ███████████
 0 1 0 2 1 0 1 3 2 1 2 1

Water trapped (shown as ~):
      █
  █~~~██~█~█
 ██~███████████

Calculation:
Position 2: min(1,3) - 0 = 1
Position 4: min(2,3) - 1 = 1
Position 5: min(2,3) - 0 = 2
Position 6: min(2,3) - 1 = 1
Position 8: min(3,2) - 2 = 0
Position 9: min(2,2) - 1 = 1
Position 10: min(2,1) - 2 = 0

Total: 6 units
```

### Solution
```python
def trap(height):
    if not height:
        return 0
    
    left, right = 0, len(height) - 1
    left_max, right_max = height[left], height[right]
    water = 0
    
    while left < right:
        if height[left] < height[right]:
            if height[left] >= left_max:
                left_max = height[left]
            else:
                water += left_max - height[left]
            left += 1
        else:
            if height[right] >= right_max:
                right_max = height[right]
            else:
                water += right_max - height[right]
            right -= 1
    
    return water
```

**Time**: O(n), **Space**: O(1)

---

## Problem 3: [632. Smallest Range Covering Elements from K Lists](https://leetcode.com/problems/smallest-range-covering-elements-from-k-lists/)

### Problem Statement
Given `k` sorted lists, find the smallest range that includes at least one number from each list.

### Intuition
**Min Heap + Sliding Window**:
1. Use heap to track smallest element from each list
2. Track maximum element in current window
3. Update minimum range when all lists represented
4. Move forward the list with smallest element

### Solution
```python
import heapq

def smallestRange(nums):
    # Min heap: (value, list_idx, element_idx)
    heap = []
    max_val = float('-inf')
    
    # Initialize heap with first element from each list
    for i in range(len(nums)):
        heapq.heappush(heap, (nums[i][0], i, 0))
        max_val = max(max_val, nums[i][0])
    
    min_range = [float('-inf'), float('inf')]
    
    while heap:
        min_val, list_idx, elem_idx = heapq.heappop(heap)
        
        # Update minimum range
        if max_val - min_val < min_range[1] - min_range[0]:
            min_range = [min_val, max_val]
        
        # Move to next element in the same list
        if elem_idx + 1 < len(nums[list_idx]):
            next_val = nums[list_idx][elem_idx + 1]
            heapq.heappush(heap, (next_val, list_idx, elem_idx + 1))
            max_val = max(max_val, next_val)
        else:
            # Can't cover all lists anymore
            break
    
    return min_range
```

**Time**: O(n log k), **Space**: O(k)

---

## Problem 4: [30. Substring with Concatenation of All Words](https://leetcode.com/problems/substring-with-concatenation-of-all-words/)

### Problem Statement
Given a string `s` and array of words, find all starting indices of concatenated substrings in `s` that is a concatenation of each word exactly once.

### Intuition
**Sliding Window with Word Counting**:
1. All words have same length
2. Use sliding window of size = len(words) * word_length
3. Count words in window, compare with target
4. Slide by one word length

### Solution
```python
from collections import Counter

def findSubstring(s, words):
    if not s or not words:
        return []
    
    word_len = len(words[0])
    word_count = len(words)
    total_len = word_len * word_count
    
    word_freq = Counter(words)
    result = []
    
    # Try all possible starting positions
    for i in range(word_len):
        left = i
        window_count = {}
        count = 0
        
        for right in range(i, len(s) - word_len + 1, word_len):
            word = s[right:right + word_len]
            
            if word in word_freq:
                window_count[word] = window_count.get(word, 0) + 1
                count += 1
                
                # Shrink window if word count exceeds
                while window_count[word] > word_freq[word]:
                    left_word = s[left:left + word_len]
                    window_count[left_word] -= 1
                    count -= 1
                    left += word_len
                
                # Check if valid window
                if count == word_count:
                    result.append(left)
                    
                    # Shrink window
                    left_word = s[left:left + word_len]
                    window_count[left_word] -= 1
                    count -= 1
                    left += word_len
            else:
                # Reset
                window_count.clear()
                count = 0
                left = right + word_len
    
    return result
```

**Time**: O(n * word_len), **Space**: O(m * word_len)

---

## Problem 5: [1074. Number of Submatrices That Sum to Target](https://leetcode.com/problems/number-of-submatrices-that-sum-to-target/)

### Problem Statement
Given a matrix and a target, return the number of non-empty submatrices that sum to target.

### Intuition
**Prefix Sum + HashMap**:
1. Convert 2D problem to 1D (fix top and bottom rows)
2. For each row pair, compute column prefix sums
3. Use HashMap to count subarrays with target sum
4. Similar to "Subarray Sum Equals K"

### Solution
```python
def numSubmatrixSumTarget(matrix, target):
    rows, cols = len(matrix), len(matrix[0])
    
    # Compute prefix sums for each row
    for row in matrix:
        for c in range(1, cols):
            row[c] += row[c-1]
    
    count = 0
    
    # Fix left and right columns
    for left in range(cols):
        for right in range(left, cols):
            # Compute sum for each row in [left, right]
            prefix_sum = {0: 1}
            current_sum = 0
            
            for row in range(rows):
                # Sum of row in column range [left, right]
                col_sum = matrix[row][right]
                if left > 0:
                    col_sum -= matrix[row][left-1]
                
                current_sum += col_sum
                
                # Check if (current_sum - target) exists
                count += prefix_sum.get(current_sum - target, 0)
                prefix_sum[current_sum] = prefix_sum.get(current_sum, 0) + 1
    
    return count
```

**Time**: O(rows * cols²), **Space**: O(rows)

---

## Problem 6: [904. Fruit Into Baskets](https://leetcode.com/problems/fruit-into-baskets/)

### Problem Statement
Given an array `fruits`, collect maximum fruits with at most 2 types of fruits (sliding window of size with 2 unique elements).

### Intuition
**Variable Sliding Window**:
1. Expand window while ≤ 2 types
2. Shrink when > 2 types
3. Track maximum length

### Solution
```python
def totalFruit(fruits):
    count = {}
    left = 0
    max_fruits = 0
    
    for right in range(len(fruits)):
        count[fruits[right]] = count.get(fruits[right], 0) + 1
        
        # Shrink window if more than 2 types
        while len(count) > 2:
            count[fruits[left]] -= 1
            if count[fruits[left]] == 0:
                del count[fruits[left]]
            left += 1
        
        max_fruits = max(max_fruits, right - left + 1)
    
    return max_fruits
```

**Time**: O(n), **Space**: O(1)

---

## Problem 7: [992. Subarrays with K Different Integers](https://leetcode.com/problems/subarrays-with-k-different-integers/)

### Problem Statement
Given array and integer `k`, return number of good subarrays (subarrays with exactly `k` different integers).

### Intuition
**Transform Problem**:
- exactly(K) = atMost(K) - atMost(K-1)
- Use sliding window to count atMost(K)

### Solution
```python
def subarraysWithKDistinct(nums, k):
    def atMostK(k):
        count = {}
        left = 0
        result = 0
        
        for right in range(len(nums)):
            count[nums[right]] = count.get(nums[right], 0) + 1
            
            while len(count) > k:
                count[nums[left]] -= 1
                if count[nums[left]] == 0:
                    del count[nums[left]]
                left += 1
            
            result += right - left + 1
        
        return result
    
    return atMostK(k) - atMostK(k - 1)
```

**Time**: O(n), **Space**: O(k)

---

## Summary Table

| Problem | Pattern | Key Technique | Difficulty |
|---------|---------|---------------|------------|
| Minimum Window Substring | Dynamic Window | Counter + formed tracking | Hard |
| Trapping Rain Water | Two Pointers | Max from both sides | Hard |
| Smallest Range K Lists | Heap + Window | Min heap tracking | Hard |
| Substring Concatenation | Fixed Window | Word-level sliding | Hard |
| Submatrix Sum | 2D Prefix Sum | Reduce to 1D | Hard |
| Subarrays K Distinct | AtMost Transform | Sliding window trick | Hard |

**Master sliding windows and two pointers to dominate array problems! 🚀**
