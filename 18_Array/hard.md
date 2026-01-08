# 🔥 Array - Hard Problems Collection

A comprehensive collection of challenging Array problems with complete solutions, advanced techniques, and detailed explanations.

---

## 📚 Table of Contents

1. [First Missing Positive](#problem-1-first-missing-positive)
2. [Trapping Rain Water](#problem-2-trapping-rain-water)
3. [Median of Two Sorted Arrays](#problem-3-median-of-two-sorted-arrays)
4. [Maximum Gap](#problem-4-maximum-gap)
5. [Count of Smaller Numbers After Self](#problem-5-count-of-smaller-numbers-after-self)
6. [Largest Rectangle in Histogram](#problem-6-largest-rectangle-in-histogram)
7. [Count of Range Sum](#problem-7-count-of-range-sum)
8. [Pattern Summary](#-pattern-summary)

---

## Problem 1: First Missing Positive

**LeetCode 41 - Hard**

### Problem Statement
Find the smallest missing positive integer in unsorted array. Must run in O(n) time and O(1) space.

```
Input: nums = [3,4,-1,1]
Output: 2

Input: nums = [7,8,9,11,12]
Output: 1
```

### 🎯 Intuition
**Index as hash map approach:**
- Array of size n can only contain answer in range [1, n+1]
- Use array indices as hash map: nums[i] should contain i+1
- Place each number at its "correct" position
- First index with wrong value → answer

**Key insight:** Answer must be in [1, n+1], so we can use in-place marking.

### 📊 Visual Representation

```
nums = [3, 4, -1, 1]

Step 1: Place numbers at correct positions
  3 should be at index 2
  4 should be at index 3
  -1 is out of range, ignore
  1 should be at index 0

After rearrangement: [1, -1, 3, 4]
                      ↑   ↑
                    idx 0  idx 1 (wrong value)

Step 2: Find first index with wrong value
  nums[0] = 1 ✓
  nums[1] = -1 ✗ (should be 2)
  
Answer: 2
```

### Solution

```python
def firstMissingPositive(nums):
    """
    Cyclic sort approach with in-place marking.
    
    Logic:
    - Place each number at its correct index
    - Number k should be at index k-1
    - First missing position is answer
    
    Time: O(n)
    Space: O(1)
    """
    n = len(nums)
    
    # Place numbers at correct positions
    for i in range(n):
        while 1 <= nums[i] <= n and nums[nums[i] - 1] != nums[i]:
            # Swap nums[i] to its correct position
            correct_idx = nums[i] - 1
            nums[i], nums[correct_idx] = nums[correct_idx], nums[i]
    
    # Find first missing positive
    for i in range(n):
        if nums[i] != i + 1:
            return i + 1
    
    return n + 1

# Example usage
print(firstMissingPositive([3,4,-1,1]))  # Output: 2
print(firstMissingPositive([7,8,9,11,12]))  # Output: 1
print(firstMissingPositive([1,2,0]))  # Output: 3
```

### Alternative: Marking with Signs

```python
def firstMissingPositive_marking(nums):
    """
    Use negative signs as markers.
    
    Time: O(n)
    Space: O(1)
    """
    n = len(nums)
    
    # Step 1: Replace negatives and out-of-range with n+1
    for i in range(n):
        if nums[i] <= 0 or nums[i] > n:
            nums[i] = n + 1
    
    # Step 2: Mark presence by negating at index
    for i in range(n):
        val = abs(nums[i])
        if val <= n:
            nums[val - 1] = -abs(nums[val - 1])
    
    # Step 3: Find first positive index
    for i in range(n):
        if nums[i] > 0:
            return i + 1
    
    return n + 1
```

### 🔍 Dry Run

```
nums = [3, 4, -1, 1]

Cyclic Sort Approach:
  i=0: nums[0]=3
    3 should be at index 2
    Swap nums[0] with nums[2]
    nums = [-1, 4, 3, 1]
    
    nums[0]=-1 (out of range, stop)
  
  i=1: nums[1]=4
    4 should be at index 3
    Swap nums[1] with nums[3]
    nums = [-1, 1, 3, 4]
    
    nums[1]=1
    1 should be at index 0
    Swap nums[1] with nums[0]
    nums = [1, -1, 3, 4]
    
    nums[1]=-1 (out of range, stop)
  
  i=2: nums[2]=3 (already at correct position)
  i=3: nums[3]=4 (already at correct position)

Find missing:
  i=0: nums[0]=1 ✓
  i=1: nums[1]=-1 ≠ 2 → return 2
```

### ⏱️ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Cyclic Sort | O(n) | O(1) | ⭐ Clean logic |
| Sign Marking | O(n) | O(1) | Clever technique |

---

## Problem 2: Trapping Rain Water

**LeetCode 42 - Hard**

### Problem Statement
Calculate water trapped after raining, given elevation map.

```
Input: height = [0,1,0,2,1,0,1,3,2,1,2,1]
Output: 6
```

### 🎯 Intuition
Water at position i = min(max_left, max_right) - height[i]

**Approaches:**
1. **Two arrays:** Precompute max_left and max_right
2. **Two pointers:** Process from both ends simultaneously
3. **Stack:** Track decreasing heights

### 📊 Visual Representation

```
height = [0,1,0,2,1,0,1,3,2,1,2,1]

Visual:
       █
   █   █ █ █
 █ █ █ █ █ █
-----------------
 0 1 0 2 1 0 1 3 2 1 2 1

Water trapped:
       █
   █~~~█~█~█
 █~█~█~█~█~█
-----------------
 0 1 2 3 4 5 6 7 8 9 10 11

Water at each position:
  i=2: min(1,3) - 0 = 1
  i=4: min(2,3) - 1 = 1
  i=5: min(2,3) - 0 = 2
  i=9: min(3,2) - 1 = 1
  i=11: min(2,1) - 1 = 0
  
Total: 1+1+2+1+1 = 6
```

### Solution 1: Two Pointers

```python
def trap(height):
    """
    Two pointers approach.
    
    Logic:
    - Process from both ends
    - Move pointer with smaller max
    - Water trapped = max - current height
    
    Time: O(n)
    Space: O(1)
    """
    if not height:
        return 0
    
    left, right = 0, len(height) - 1
    left_max = right_max = 0
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

# Example usage
print(trap([0,1,0,2,1,0,1,3,2,1,2,1]))  # Output: 6
```

### Solution 2: Monotonic Stack

```python
def trap_stack(height):
    """
    Stack-based approach.
    
    Logic:
    - Maintain decreasing stack
    - When higher bar found, calculate trapped water
    
    Time: O(n)
    Space: O(n)
    """
    stack = []
    water = 0
    
    for i, h in enumerate(height):
        while stack and height[stack[-1]] < h:
            top = stack.pop()
            
            if not stack:
                break
            
            distance = i - stack[-1] - 1
            bounded_height = min(height[i], height[stack[-1]]) - height[top]
            water += distance * bounded_height
        
        stack.append(i)
    
    return water
```

### Solution 3: Precompute Arrays

```python
def trap_arrays(height):
    """
    Precompute max_left and max_right.
    
    Time: O(n)
    Space: O(n)
    """
    if not height:
        return 0
    
    n = len(height)
    left_max = [0] * n
    right_max = [0] * n
    
    # Compute left_max
    left_max[0] = height[0]
    for i in range(1, n):
        left_max[i] = max(left_max[i-1], height[i])
    
    # Compute right_max
    right_max[n-1] = height[n-1]
    for i in range(n-2, -1, -1):
        right_max[i] = max(right_max[i+1], height[i])
    
    # Calculate water
    water = 0
    for i in range(n):
        water += min(left_max[i], right_max[i]) - height[i]
    
    return water
```

### ⏱️ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Two Pointers | O(n) | O(1) | ⭐ Optimal |
| Stack | O(n) | O(n) | Intuitive |
| Arrays | O(n) | O(n) | Simple |

---

## Problem 3: Median of Two Sorted Arrays

**LeetCode 4 - Hard**

### Problem Statement
Find median of two sorted arrays in O(log(m+n)) time.

```
Input: nums1 = [1,3], nums2 = [2]
Output: 2.0

Input: nums1 = [1,2], nums2 = [3,4]
Output: 2.5
```

### 🎯 Intuition
**Binary search on partitions:**
- Partition arrays so left half has (m+n+1)//2 elements
- Ensure: max(left_part) ≤ min(right_part)
- Binary search on smaller array for correct partition

### 📊 Visual Representation

```
nums1 = [1, 3, 8, 9, 15]
nums2 = [7, 11, 18, 19, 21, 25]

Total = 11 elements, median at position 6

Partition nums1 at position 3:
  Left:  [1, 3, 8]
  Right: [9, 15]

Partition nums2 at position 3:
  Left:  [7, 11, 18]
  Right: [19, 21, 25]

Check:
  max(left1) = 8 ≤ min(right2) = 19 ✓
  max(left2) = 18 ≤ min(right1) = 9 ✗

Adjust partition...

Final partition:
  nums1: [1, 3] | [8, 9, 15]
  nums2: [7, 11, 18, 19] | [21, 25]
  
  max(left) = max(3, 19) = 19
  min(right) = min(8, 21) = 8
  
  Wait, that's wrong. Let me recalculate...

Actually for odd total:
  nums1: [1, 3, 8] | [9, 15]
  nums2: [7, 11] | [18, 19, 21, 25]
  
  Left: {1,3,8,7,11} (5 elements)
  Right: {9,15,18,19,21,25} (6 elements)
  
  Median = max(left) = max(8, 11) = 11
```

### Solution

```python
def findMedianSortedArrays(nums1, nums2):
    """
    Binary search on partitions.
    
    Logic:
    - Binary search on smaller array
    - Find partition where max(left) ≤ min(right)
    - Calculate median from partition values
    
    Time: O(log(min(m,n)))
    Space: O(1)
    """
    # Ensure nums1 is smaller
    if len(nums1) > len(nums2):
        nums1, nums2 = nums2, nums1
    
    m, n = len(nums1), len(nums2)
    left, right = 0, m
    
    while left <= right:
        partition1 = (left + right) // 2
        partition2 = (m + n + 1) // 2 - partition1
        
        # Get edge values
        max_left1 = float('-inf') if partition1 == 0 else nums1[partition1 - 1]
        min_right1 = float('inf') if partition1 == m else nums1[partition1]
        
        max_left2 = float('-inf') if partition2 == 0 else nums2[partition2 - 1]
        min_right2 = float('inf') if partition2 == n else nums2[partition2]
        
        # Check if partition is correct
        if max_left1 <= min_right2 and max_left2 <= min_right1:
            # Found correct partition
            if (m + n) % 2 == 0:
                return (max(max_left1, max_left2) + min(min_right1, min_right2)) / 2
            else:
                return max(max_left1, max_left2)
        elif max_left1 > min_right2:
            # Too many elements from nums1, go left
            right = partition1 - 1
        else:
            # Too few elements from nums1, go right
            left = partition1 + 1

# Example usage
print(findMedianSortedArrays([1,3], [2]))  # Output: 2.0
print(findMedianSortedArrays([1,2], [3,4]))  # Output: 2.5
```

### Alternative: Merge and Find (O(m+n))

```python
def findMedianSortedArrays_merge(nums1, nums2):
    """
    Merge arrays and find median.
    
    Time: O(m+n)
    Space: O(m+n)
    """
    merged = []
    i = j = 0
    
    while i < len(nums1) and j < len(nums2):
        if nums1[i] < nums2[j]:
            merged.append(nums1[i])
            i += 1
        else:
            merged.append(nums2[j])
            j += 1
    
    merged.extend(nums1[i:])
    merged.extend(nums2[j:])
    
    n = len(merged)
    if n % 2 == 0:
        return (merged[n//2 - 1] + merged[n//2]) / 2
    else:
        return merged[n//2]
```

### ⏱️ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Binary Search | O(log(min(m,n))) | O(1) | ⭐ Optimal |
| Merge | O(m+n) | O(m+n) | Simple |

---

## Problem 4: Maximum Gap

**LeetCode 164 - Hard**

### Problem Statement
Find maximum difference between successive elements in sorted form. Must run in O(n) time.

```
Input: nums = [3,6,9,1]
Output: 3
Explanation: Sorted [1,3,6,9], max gap = 6-3 = 3
```

### 🎯 Intuition
**Bucket sort approach:**
- Maximum gap ≥ (max - min) / (n - 1) (pigeonhole principle)
- Create n-1 buckets of size = ceiling(range / (n-1))
- Max gap is between buckets (not within)

### Solution

```python
def maximumGap(nums):
    """
    Bucket sort for O(n) solution.
    
    Logic:
    - Divide range into n-1 buckets
    - Track min/max in each bucket
    - Max gap between consecutive buckets
    
    Time: O(n)
    Space: O(n)
    """
    if len(nums) < 2:
        return 0
    
    min_val, max_val = min(nums), max(nums)
    
    if min_val == max_val:
        return 0
    
    n = len(nums)
    bucket_size = max(1, (max_val - min_val) // (n - 1))
    bucket_count = (max_val - min_val) // bucket_size + 1
    
    buckets = [[None, None] for _ in range(bucket_count)]
    
    # Place numbers in buckets
    for num in nums:
        idx = (num - min_val) // bucket_size
        if buckets[idx][0] is None:
            buckets[idx][0] = buckets[idx][1] = num
        else:
            buckets[idx][0] = min(buckets[idx][0], num)
            buckets[idx][1] = max(buckets[idx][1], num)
    
    # Find maximum gap
    max_gap = 0
    prev_max = min_val
    
    for bucket in buckets:
        if bucket[0] is None:
            continue
        max_gap = max(max_gap, bucket[0] - prev_max)
        prev_max = bucket[1]
    
    return max_gap

# Example usage
print(maximumGap([3,6,9,1]))  # Output: 3
```

### ⏱️ Complexity Analysis

| Metric | Complexity | Notes |
|--------|------------|-------|
| Time | O(n) | Bucket sort |
| Space | O(n) | Buckets |

---

## Problem 5: Count of Smaller Numbers After Self

**LeetCode 315 - Hard**

### Problem Statement
For each element, count how many numbers to the right are smaller.

```
Input: nums = [5,2,6,1]
Output: [2,1,1,0]
Explanation:
  5: 2,1 are smaller → count=2
  2: 1 is smaller → count=1
  6: 1 is smaller → count=1
  1: none smaller → count=0
```

### 🎯 Intuition
**Merge sort with counting:**
- During merge, count inversions
- Modified merge sort tracks indices
- When right element < left element → count inversions

### Solution 1: Merge Sort

```python
def countSmaller(nums):
    """
    Modified merge sort to count inversions.
    
    Time: O(n log n)
    Space: O(n)
    """
    def merge_sort(indices):
        if len(indices) <= 1:
            return indices
        
        mid = len(indices) // 2
        left = merge_sort(indices[:mid])
        right = merge_sort(indices[mid:])
        
        return merge(left, right)
    
    def merge(left, right):
        merged = []
        i = j = 0
        
        while i < len(left) and j < len(right):
            if nums[left[i]] <= nums[right[j]]:
                # Count elements from right that are smaller
                counts[left[i]] += j
                merged.append(left[i])
                i += 1
            else:
                merged.append(right[j])
                j += 1
        
        # Remaining left elements
        while i < len(left):
            counts[left[i]] += j
            merged.append(left[i])
            i += 1
        
        # Remaining right elements
        merged.extend(right[j:])
        
        return merged
    
    n = len(nums)
    counts = [0] * n
    indices = list(range(n))
    merge_sort(indices)
    
    return counts

# Example usage
print(countSmaller([5,2,6,1]))  # Output: [2,1,1,0]
```

### Solution 2: Binary Indexed Tree (Fenwick Tree)

```python
class BIT:
    def __init__(self, n):
        self.tree = [0] * (n + 1)
        self.n = n
    
    def update(self, i):
        i += 1
        while i <= self.n:
            self.tree[i] += 1
            i += i & (-i)
    
    def query(self, i):
        i += 1
        s = 0
        while i > 0:
            s += self.tree[i]
            i -= i & (-i)
        return s

def countSmaller_bit(nums):
    """
    BIT for range sum queries.
    
    Time: O(n log n)
    Space: O(n)
    """
    # Coordinate compression
    sorted_nums = sorted(set(nums))
    ranks = {v: i for i, v in enumerate(sorted_nums)}
    
    n = len(sorted_nums)
    bit = BIT(n)
    result = []
    
    # Process from right to left
    for num in reversed(nums):
        rank = ranks[num]
        result.append(bit.query(rank - 1) if rank > 0 else 0)
        bit.update(rank)
    
    return result[::-1]
```

### ⏱️ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Merge Sort | O(n log n) | O(n) | ⭐ Clean |
| BIT | O(n log n) | O(n) | Advanced |

---

## Problem 6: Largest Rectangle in Histogram

**LeetCode 84 - Hard**

### Problem Statement
Find largest rectangle in histogram.

```
Input: heights = [2,1,5,6,2,3]
Output: 10
```

### Solution

```python
def largestRectangleArea(heights):
    """
    Monotonic stack approach.
    
    Time: O(n)
    Space: O(n)
    """
    stack = []
    max_area = 0
    heights.append(0)
    
    for i, h in enumerate(heights):
        while stack and heights[stack[-1]] > h:
            height_idx = stack.pop()
            width = i if not stack else i - stack[-1] - 1
            max_area = max(max_area, heights[height_idx] * width)
        stack.append(i)
    
    heights.pop()
    return max_area

# Example usage
print(largestRectangleArea([2,1,5,6,2,3]))  # Output: 10
```

### ⏱️ Complexity Analysis

| Metric | Complexity |
|--------|------------|
| Time | O(n) |
| Space | O(n) |

---

## Problem 7: Count of Range Sum

**LeetCode 327 - Hard**

### Problem Statement
Count number of range sums in [lower, upper].

```
Input: nums = [-2,5,-1], lower = -2, upper = 2
Output: 3
Explanation: [0,0], [2,2], [0,2]
```

### Solution

```python
def countRangeSum(nums, lower, upper):
    """
    Merge sort with prefix sums.
    
    Time: O(n log n)
    Space: O(n)
    """
    def merge_sort(sums):
        if len(sums) <= 1:
            return 0
        
        mid = len(sums) // 2
        count = merge_sort(sums[:mid]) + merge_sort(sums[mid:])
        
        j = k = mid
        for i in range(mid):
            while j < len(sums) and sums[j] - sums[i] < lower:
                j += 1
            while k < len(sums) and sums[k] - sums[i] <= upper:
                k += 1
            count += k - j
        
        sums[:] = sorted(sums)
        return count
    
    # Compute prefix sums
    prefix = [0]
    for num in nums:
        prefix.append(prefix[-1] + num)
    
    return merge_sort(prefix)

# Example usage
print(countRangeSum([-2,5,-1], -2, 2))  # Output: 3
```

### ⏱️ Complexity Analysis

| Metric | Complexity |
|--------|------------|
| Time | O(n log n) |
| Space | O(n) |

---

## 🎯 Pattern Summary

### Core Array Patterns

1. **Cyclic Sort** - Finding missing/duplicate numbers
2. **Two Pointers** - Processing from both ends
3. **Monotonic Stack** - Next greater/smaller elements
4. **Binary Search** - Finding in sorted or partition
5. **Merge Sort** - Counting inversions
6. **Bucket Sort** - Linear time sorting with constraints
7. **Prefix Sums** - Range queries

### Problem Categories

| Category | Problems | Key Technique |
|----------|----------|---------------|
| Missing/Duplicate | First Missing Positive | Cyclic sort |
| Water Trapping | Rain Water | Two pointers/Stack |
| Binary Search | Median of Two Arrays | Partition search |
| Linear Sort | Maximum Gap | Bucket sort |
| Inversions | Count Smaller | Merge sort |
| Rectangle | Largest Rectangle | Monotonic stack |
| Range Queries | Range Sum | Prefix + Merge sort |

### When to Use Each Pattern

- **Cyclic Sort:** Array elements in range [1, n]
- **Two Pointers:** Sorted array or water problems
- **Stack:** Next greater/smaller, histogram problems
- **Binary Search:** Find partition or threshold
- **Merge Sort:** Count inversions or pairs
- **Bucket Sort:** O(n) time with range constraints

Master these array patterns for interview success! 🚀

