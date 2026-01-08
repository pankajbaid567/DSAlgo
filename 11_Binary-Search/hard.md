# 🔥 Binary Search - Hard Problems Collection

This collection contains **8 hard problems** with complete solutions, detailed explanations, and multiple approaches.

---

## Problem 1: Median of Two Sorted Arrays

**LeetCode 4** - Hard

### Problem Statement
Find the median of two sorted arrays. The overall run time complexity should be O(log(m+n)).

**Example:**
```
Input: nums1 = [1,3], nums2 = [2]
Output: 2.0

Input: nums1 = [1,2], nums2 = [3,4]
Output: 2.5
```

### Intuition
Binary search on the smaller array to partition both arrays such that:
- Left half has (m+n+1)/2 elements
- max(left) <= min(right)

### Visual Representation
```
nums1: [1, 3, 8, 9, 15]
nums2: [7, 11, 18, 19, 21, 25]

Partition:
nums1: [1, 3, 8] | [9, 15]
nums2: [7, 11] | [18, 19, 21, 25]

Left: [1,3,7,8,11]  Right: [9,15,18,19,21,25]
max(left) = 11, min(right) = 9
Invalid! Need to adjust partition.

Final partition:
nums1: [1, 3] | [8, 9, 15]
nums2: [7, 11, 18] | [19, 21, 25]

max(left) = 11, min(right) = 8
Still invalid!

Correct partition:
nums1: [1, 3, 8] | [9, 15]
nums2: [7] | [11, 18, 19, 21, 25]

max(left) = 8, min(right) = 9
Valid! median = (8 + 9) / 2 = 8.5
```

### Solution
```python
def findMedianSortedArrays(nums1, nums2):
    """
    Binary search on smaller array
    Time: O(log(min(m, n)))
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
        
        # Edge cases
        maxLeft1 = float('-inf') if partition1 == 0 else nums1[partition1 - 1]
        minRight1 = float('inf') if partition1 == m else nums1[partition1]
        
        maxLeft2 = float('-inf') if partition2 == 0 else nums2[partition2 - 1]
        minRight2 = float('inf') if partition2 == n else nums2[partition2]
        
        # Check if partition is correct
        if maxLeft1 <= minRight2 and maxLeft2 <= minRight1:
            # Found correct partition
            if (m + n) % 2 == 0:
                return (max(maxLeft1, maxLeft2) + min(minRight1, minRight2)) / 2
            else:
                return max(maxLeft1, maxLeft2)
        elif maxLeft1 > minRight2:
            # Move partition1 left
            right = partition1 - 1
        else:
            # Move partition1 right
            left = partition1 + 1
    
    return 0.0
```

### Dry Run
```
nums1 = [1,3], nums2 = [2]

m=2, n=1, total=3
left=0, right=2

Iteration 1:
partition1 = 1, partition2 = 2 - 1 = 1
maxLeft1 = 1, minRight1 = 3
maxLeft2 = 2, minRight2 = inf

Check: 1 <= inf ✓, 2 <= 3 ✓
Odd total: return max(1, 2) = 2.0
```

### Complexity
- **Time**: O(log(min(m, n)))
- **Space**: O(1)

---

## Problem 2: Split Array Largest Sum

**LeetCode 410** - Hard

### Problem Statement
Given array and integer `k`, split array into `k` non-empty subarrays to minimize the largest sum among these subarrays.

**Example:**
```
Input: nums = [7,2,5,10,8], k = 2
Output: 18
Explanation: [7,2,5] and [10,8] -> max(14, 18) = 18
```

### Intuition
Binary search on the answer. For each candidate maximum sum, check if we can split array into k subarrays.

### Solution
```python
def splitArray(nums, k):
    """
    Binary search on answer
    Time: O(n log(sum - max))
    Space: O(1)
    """
    def canSplit(max_sum):
        """Check if we can split into k subarrays with max sum"""
        count = 1
        current_sum = 0
        
        for num in nums:
            if current_sum + num > max_sum:
                count += 1
                current_sum = num
                if count > k:
                    return False
            else:
                current_sum += num
        
        return True
    
    left = max(nums)  # At least the largest element
    right = sum(nums)  # At most the entire array
    result = right
    
    while left <= right:
        mid = (left + right) // 2
        
        if canSplit(mid):
            result = mid
            right = mid - 1  # Try smaller max sum
        else:
            left = mid + 1  # Need larger max sum
    
    return result
```

### Dry Run
```
nums = [7,2,5,10,8], k = 2

left = 10, right = 32

Iteration 1: mid = 21
canSplit(21): [7,2,5,10] [8] -> 2 subarrays ✓
result = 21, right = 20

Iteration 2: mid = 15
canSplit(15): [7,2,5] [10] [8] -> 3 subarrays ✗
left = 16

Iteration 3: mid = 18
canSplit(18): [7,2,5] [10,8] -> 2 subarrays ✓
result = 18, right = 17

Iteration 4: mid = 16
canSplit(16): [7,2,5] [10] [8] -> 3 subarrays ✗
left = 17

Iteration 5: mid = 17
canSplit(17): [7,2,5] [10] [8] -> 3 subarrays ✗
left = 18

left > right, return 18
```

---

## Problem 3: Find K-th Smallest Pair Distance

**LeetCode 719** - Hard

### Problem Statement
Given integer array `nums` and integer `k`, return the k-th smallest distance among all pairs.

**Example:**
```
Input: nums = [1,3,1], k = 1
Output: 0
Explanation: Pairs: (1,3)=2, (1,1)=0, (3,1)=2. Sorted: [0,2,2]
```

### Solution
```python
def smallestDistancePair(nums, k):
    """
    Binary search on distance + sliding window
    Time: O(n log n + n log(max-min))
    Space: O(1)
    """
    nums.sort()
    n = len(nums)
    
    def countPairs(max_distance):
        """Count pairs with distance <= max_distance"""
        count = 0
        left = 0
        
        for right in range(n):
            while nums[right] - nums[left] > max_distance:
                left += 1
            count += right - left
        
        return count
    
    left = 0
    right = nums[-1] - nums[0]
    
    while left < right:
        mid = (left + right) // 2
        
        if countPairs(mid) < k:
            left = mid + 1
        else:
            right = mid
    
    return left
```

### Complexity
- **Time**: O(n log n + n log(max-min))
- **Space**: O(1)

---

## Problem 4: Koko Eating Bananas

**LeetCode 875** - Medium/Hard

### Problem Statement
Koko can eat bananas at speed `k` per hour. Return minimum `k` such that she can eat all bananas within `h` hours.

**Example:**
```
Input: piles = [3,6,7,11], h = 8
Output: 4
```

### Solution
```python
import math

def minEatingSpeed(piles, h):
    """
    Binary search on speed
    Time: O(n log m) where m is max pile
    Space: O(1)
    """
    def canFinish(speed):
        """Check if can finish in h hours at this speed"""
        hours = 0
        for pile in piles:
            hours += math.ceil(pile / speed)
        return hours <= h
    
    left = 1
    right = max(piles)
    result = right
    
    while left <= right:
        mid = (left + right) // 2
        
        if canFinish(mid):
            result = mid
            right = mid - 1
        else:
            left = mid + 1
    
    return result
```

### Dry Run
```
piles = [3,6,7,11], h = 8

left=1, right=11

mid=6: hours = ceil(3/6)+ceil(6/6)+ceil(7/6)+ceil(11/6)
     = 1+1+2+2 = 6 ≤ 8 ✓
result=6, right=5

mid=3: hours = 1+2+3+4 = 10 > 8 ✗
left=4

mid=4: hours = 1+2+2+3 = 8 ≤ 8 ✓
result=4, right=3

left > right, return 4
```

---

## Problem 5: Minimize Max Distance to Gas Station

**LeetCode 774** - Hard

### Problem Statement
Add `k` gas stations to minimize the maximum distance between adjacent stations.

**Example:**
```
Input: stations = [1,2,3,4,5,6,7,8,9,10], k = 9
Output: 0.5
```

### Solution
```python
def minmaxGasDist(stations, k):
    """
    Binary search on distance
    Time: O(n log(max_distance/precision))
    Space: O(1)
    """
    def possible(max_dist):
        """Check if we can achieve max_dist with k stations"""
        count = 0
        for i in range(len(stations) - 1):
            gap = stations[i + 1] - stations[i]
            count += int(gap / max_dist)
        return count <= k
    
    left = 0
    right = stations[-1] - stations[0]
    
    while right - left > 1e-6:
        mid = (left + right) / 2
        
        if possible(mid):
            right = mid
        else:
            left = mid
    
    return left
```

### Complexity
- **Time**: O(n log(max_distance/ε))
- **Space**: O(1)

---

## Problem 6: Swim in Rising Water

**LeetCode 778** - Hard

### Problem Statement
In `n x n` grid, find minimum time to swim from (0,0) to (n-1,n-1). Water level rises by 1 per second.

**Example:**
```
Input: grid = [[0,2],[1,3]]
Output: 3
```

### Solution
```python
def swimInWater(grid):
    """
    Binary search + BFS
    Time: O(n^2 log n)
    Space: O(n^2)
    """
    n = len(grid)
    
    def canReach(time):
        """Check if can reach destination with water level = time"""
        if grid[0][0] > time:
            return False
        
        visited = [[False] * n for _ in range(n)]
        queue = [(0, 0)]
        visited[0][0] = True
        
        directions = [(0,1), (0,-1), (1,0), (-1,0)]
        
        while queue:
            x, y = queue.pop(0)
            
            if x == n-1 and y == n-1:
                return True
            
            for dx, dy in directions:
                nx, ny = x + dx, y + dy
                
                if (0 <= nx < n and 0 <= ny < n and 
                    not visited[nx][ny] and grid[nx][ny] <= time):
                    visited[nx][ny] = True
                    queue.append((nx, ny))
        
        return False
    
    left = grid[0][0]
    right = n * n - 1
    
    while left < right:
        mid = (left + right) // 2
        
        if canReach(mid):
            right = mid
        else:
            left = mid + 1
    
    return left
```

### Complexity
- **Time**: O(n² log n)
- **Space**: O(n²)

---

## Problem 7: Count of Range Sum

**LeetCode 327** - Hard

### Problem Statement
Count number of range sums in [lower, upper].

**Example:**
```
Input: nums = [-2,5,-1], lower = -2, upper = 2
Output: 3
Explanation: [0,0], [2,2], [0,2]
```

### Solution
```python
def countRangeSum(nums, lower, upper):
    """
    Merge sort + prefix sum
    Time: O(n log n)
    Space: O(n)
    """
    def mergeCount(sums, start, end):
        if end - start <= 1:
            return 0
        
        mid = (start + end) // 2
        count = mergeCount(sums, start, mid) + mergeCount(sums, mid, end)
        
        # Count range sums
        j = k = t = mid
        temp = []
        
        for i in range(start, mid):
            # Count valid ranges
            while k < end and sums[k] - sums[i] < lower:
                k += 1
            while j < end and sums[j] - sums[i] <= upper:
                j += 1
            
            count += j - k
            
            # Merge
            while t < end and sums[t] < sums[i]:
                temp.append(sums[t])
                t += 1
            temp.append(sums[i])
        
        sums[start:start+len(temp)] = temp
        return count
    
    # Calculate prefix sums
    n = len(nums)
    prefix = [0] * (n + 1)
    for i in range(n):
        prefix[i + 1] = prefix[i] + nums[i]
    
    return mergeCount(prefix, 0, n + 1)
```

### Complexity
- **Time**: O(n log n)
- **Space**: O(n)

---

## Problem 8: Find in Mountain Array

**LeetCode 1095** - Hard

### Problem Statement
Find target in mountain array (increases then decreases) using at most 100 queries.

**Example:**
```
Input: array = [1,2,3,4,5,3,1], target = 3
Output: 2
```

### Solution
```python
def findInMountainArray(target, mountain_arr):
    """
    Three binary searches
    Time: O(log n)
    Space: O(1)
    """
    n = mountain_arr.length()
    
    # 1. Find peak
    left, right = 0, n - 1
    while left < right:
        mid = (left + right) // 2
        if mountain_arr.get(mid) < mountain_arr.get(mid + 1):
            left = mid + 1
        else:
            right = mid
    peak = left
    
    # 2. Search in increasing part
    left, right = 0, peak
    while left <= right:
        mid = (left + right) // 2
        val = mountain_arr.get(mid)
        
        if val == target:
            return mid
        elif val < target:
            left = mid + 1
        else:
            right = mid - 1
    
    # 3. Search in decreasing part
    left, right = peak, n - 1
    while left <= right:
        mid = (left + right) // 2
        val = mountain_arr.get(mid)
        
        if val == target:
            return mid
        elif val > target:  # Decreasing order
            left = mid + 1
        else:
            right = mid - 1
    
    return -1
```

### Complexity
- **Time**: O(log n)
- **Space**: O(1)

---

## 🎯 Summary Table

| Problem | Difficulty | Key Technique | Time | Space |
|---------|-----------|---------------|------|-------|
| Median Two Sorted Arrays | Hard | Binary Search on Partition | O(log min(m,n)) | O(1) |
| Split Array Largest Sum | Hard | Binary Search on Answer | O(n log S) | O(1) |
| K-th Smallest Pair Distance | Hard | Binary Search + Sliding Window | O(n log n + n log D) | O(1) |
| Koko Eating Bananas | Medium | Binary Search on Speed | O(n log m) | O(1) |
| Minimize Gas Station Distance | Hard | Binary Search on Distance | O(n log D) | O(1) |
| Swim in Rising Water | Hard | Binary Search + BFS | O(n² log n) | O(n²) |
| Count of Range Sum | Hard | Merge Sort + Prefix Sum | O(n log n) | O(n) |
| Find in Mountain Array | Hard | Three Binary Searches | O(log n) | O(1) |

---

## 💡 Key Patterns Learned

1. **Binary Search on Answer**: When answer has monotonic property
2. **Binary Search on Partition**: Median of two sorted arrays
3. **Binary Search + Helper**: Check feasibility with another function
4. **Precision Binary Search**: For floating point answers
5. **Multiple Binary Searches**: Peak finding + regular search
6. **Binary Search + BFS/DFS**: Combine for graph problems
7. **Merge Sort with Counting**: Advanced counting problems

---

**Master these patterns and conquer any binary search problem! 🎯**
