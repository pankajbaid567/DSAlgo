# 📊 Array Algorithms - Comprehensive Cheatsheet

## 📚 Core Concepts

**Arrays**: Contiguous memory locations storing elements of same type.

### Common Patterns
1. Two Pointers
2. Sliding Window
3. Prefix Sum
4. Kadane's Algorithm
5. Dutch National Flag
6. Binary Search
7. In-place Operations

---

## Pattern 1: Two Pointers

### Remove Element
```python
def removeElement(nums, val):
    """Remove all occurrences of val in-place"""
    k = 0  # Position to place next valid element
    
    for i in range(len(nums)):
        if nums[i] != val:
            nums[k] = nums[i]
            k += 1
    
    return k
```

### Remove Duplicates
```python
def removeDuplicates(nums):
    """Remove duplicates from sorted array"""
    if not nums:
        return 0
    
    k = 1  # First element is always unique
    
    for i in range(1, len(nums)):
        if nums[i] != nums[i-1]:
            nums[k] = nums[i]
            k += 1
    
    return k

def removeDuplicates_atmost_twice(nums):
    """Allow at most 2 duplicates"""
    if len(nums) <= 2:
        return len(nums)
    
    k = 2
    
    for i in range(2, len(nums)):
        if nums[i] != nums[k-2]:
            nums[k] = nums[i]
            k += 1
    
    return k
```

### Move Zeroes
```python
def moveZeroes(nums):
    """Move all zeros to end"""
    k = 0  # Position for next non-zero
    
    # Move non-zeros forward
    for i in range(len(nums)):
        if nums[i] != 0:
            nums[k] = nums[i]
            k += 1
    
    # Fill rest with zeros
    for i in range(k, len(nums)):
        nums[i] = 0

# Optimized: Swap approach
def moveZeroes_swap(nums):
    k = 0
    
    for i in range(len(nums)):
        if nums[i] != 0:
            nums[k], nums[i] = nums[i], nums[k]
            k += 1
```

---

## Pattern 2: Prefix Sum

### Range Sum Query
```python
class NumArray:
    def __init__(self, nums):
        self.prefix = [0]
        
        for num in nums:
            self.prefix.append(self.prefix[-1] + num)
    
    def sumRange(self, left, right):
        return self.prefix[right + 1] - self.prefix[left]
```

### Subarray Sum Equals K
```python
def subarraySum(nums, k):
    """Count subarrays with sum = k"""
    count = 0
    prefix_sum = 0
    sum_count = {0: 1}
    
    for num in nums:
        prefix_sum += num
        
        # Check if (prefix_sum - k) exists
        if prefix_sum - k in sum_count:
            count += sum_count[prefix_sum - k]
        
        sum_count[prefix_sum] = sum_count.get(prefix_sum, 0) + 1
    
    return count
```

### Product of Array Except Self
```python
def productExceptSelf(nums):
    """O(n) time, O(1) extra space"""
    n = len(nums)
    result = [1] * n
    
    # Left products
    left_product = 1
    for i in range(n):
        result[i] = left_product
        left_product *= nums[i]
    
    # Right products
    right_product = 1
    for i in range(n - 1, -1, -1):
        result[i] *= right_product
        right_product *= nums[i]
    
    return result
```

---

## Pattern 3: Kadane's Algorithm

### Maximum Subarray Sum
```python
def maxSubArray(nums):
    """Find maximum sum subarray"""
    max_sum = float('-inf')
    current_sum = 0
    
    for num in nums:
        current_sum = max(num, current_sum + num)
        max_sum = max(max_sum, current_sum)
    
    return max_sum

def maxSubArray_indices(nums):
    """Return max sum and indices"""
    max_sum = float('-inf')
    current_sum = 0
    start = 0
    end = 0
    temp_start = 0
    
    for i in range(len(nums)):
        if current_sum + nums[i] < nums[i]:
            current_sum = nums[i]
            temp_start = i
        else:
            current_sum += nums[i]
        
        if current_sum > max_sum:
            max_sum = current_sum
            start = temp_start
            end = i
    
    return max_sum, start, end
```

### Maximum Product Subarray
```python
def maxProduct(nums):
    """Maximum product of contiguous subarray"""
    if not nums:
        return 0
    
    max_prod = min_prod = result = nums[0]
    
    for i in range(1, len(nums)):
        num = nums[i]
        
        # When num is negative, swap max and min
        if num < 0:
            max_prod, min_prod = min_prod, max_prod
        
        max_prod = max(num, max_prod * num)
        min_prod = min(num, min_prod * num)
        
        result = max(result, max_prod)
    
    return result
```

---

## Pattern 4: Dutch National Flag

### Sort Colors (3-way partitioning)
```python
def sortColors(nums):
    """Sort 0s, 1s, 2s in-place"""
    low = 0      # Boundary for 0s
    mid = 0      # Current element
    high = len(nums) - 1  # Boundary for 2s
    
    while mid <= high:
        if nums[mid] == 0:
            nums[low], nums[mid] = nums[mid], nums[low]
            low += 1
            mid += 1
        elif nums[mid] == 1:
            mid += 1
        else:  # nums[mid] == 2
            nums[mid], nums[high] = nums[high], nums[mid]
            high -= 1
```

### Partition Array
```python
def partition(nums, pivot):
    """Partition around pivot"""
    left = 0
    right = len(nums) - 1
    i = 0
    
    while i <= right:
        if nums[i] < pivot:
            nums[left], nums[i] = nums[i], nums[left]
            left += 1
            i += 1
        elif nums[i] > pivot:
            nums[i], nums[right] = nums[right], nums[i]
            right -= 1
        else:
            i += 1
    
    return left, right
```

---

## Pattern 5: Sliding Window Maximum

### Monotonic Deque
```python
from collections import deque

def maxSlidingWindow(nums, k):
    """Maximum in each sliding window of size k"""
    dq = deque()  # Store indices
    result = []
    
    for i in range(len(nums)):
        # Remove indices outside window
        while dq and dq[0] <= i - k:
            dq.popleft()
        
        # Remove smaller elements
        while dq and nums[dq[-1]] < nums[i]:
            dq.pop()
        
        dq.append(i)
        
        # Add to result when window complete
        if i >= k - 1:
            result.append(nums[dq[0]])
    
    return result
```

---

## Pattern 6: Cyclic Sort

### Find Missing Number
```python
def missingNumber(nums):
    """Numbers from 0 to n, one missing"""
    n = len(nums)
    
    # Cyclic sort
    i = 0
    while i < n:
        correct_pos = nums[i]
        
        if correct_pos < n and nums[i] != nums[correct_pos]:
            nums[i], nums[correct_pos] = nums[correct_pos], nums[i]
        else:
            i += 1
    
    # Find missing
    for i in range(n):
        if nums[i] != i:
            return i
    
    return n

# Alternative: XOR approach
def missingNumber_xor(nums):
    xor = 0
    
    for i in range(len(nums) + 1):
        xor ^= i
    
    for num in nums:
        xor ^= num
    
    return xor
```

### Find All Duplicates
```python
def findDuplicates(nums):
    """Find all duplicates (1 to n)"""
    result = []
    
    for num in nums:
        index = abs(num) - 1
        
        if nums[index] < 0:
            result.append(abs(num))
        else:
            nums[index] = -nums[index]
    
    return result
```

---

## Pattern 7: Intervals

### Merge Intervals
```python
def merge(intervals):
    """Merge overlapping intervals"""
    if not intervals:
        return []
    
    intervals.sort(key=lambda x: x[0])
    merged = [intervals[0]]
    
    for current in intervals[1:]:
        if current[0] <= merged[-1][1]:
            # Overlapping, merge
            merged[-1][1] = max(merged[-1][1], current[1])
        else:
            merged.append(current)
    
    return merged
```

### Insert Interval
```python
def insert(intervals, newInterval):
    """Insert and merge interval"""
    result = []
    i = 0
    n = len(intervals)
    
    # Add all intervals before newInterval
    while i < n and intervals[i][1] < newInterval[0]:
        result.append(intervals[i])
        i += 1
    
    # Merge overlapping intervals
    while i < n and intervals[i][0] <= newInterval[1]:
        newInterval[0] = min(newInterval[0], intervals[i][0])
        newInterval[1] = max(newInterval[1], intervals[i][1])
        i += 1
    
    result.append(newInterval)
    
    # Add remaining intervals
    while i < n:
        result.append(intervals[i])
        i += 1
    
    return result
```

---

## Pattern 8: Rotation

### Rotate Array
```python
def rotate(nums, k):
    """Rotate array k steps to right"""
    n = len(nums)
    k = k % n
    
    # Reverse entire array
    nums.reverse()
    
    # Reverse first k elements
    nums[:k] = reversed(nums[:k])
    
    # Reverse remaining
    nums[k:] = reversed(nums[k:])
```

### Search in Rotated Sorted Array
```python
def search(nums, target):
    """Binary search in rotated array"""
    left, right = 0, len(nums) - 1
    
    while left <= right:
        mid = (left + right) // 2
        
        if nums[mid] == target:
            return mid
        
        # Determine which half is sorted
        if nums[left] <= nums[mid]:
            # Left half is sorted
            if nums[left] <= target < nums[mid]:
                right = mid - 1
            else:
                left = mid + 1
        else:
            # Right half is sorted
            if nums[mid] < target <= nums[right]:
                left = mid + 1
            else:
                right = mid - 1
    
    return -1
```

---

## Pattern 9: Next Greater/Smaller Element

### Next Greater Element
```python
def nextGreaterElements(nums):
    """Circular array"""
    n = len(nums)
    result = [-1] * n
    stack = []
    
    # Process array twice for circular
    for i in range(2 * n):
        while stack and nums[stack[-1]] < nums[i % n]:
            result[stack.pop()] = nums[i % n]
        
        if i < n:
            stack.append(i)
    
    return result
```

---

## Pattern 10: Stock Problems

### Best Time to Buy and Sell Stock
```python
# One transaction
def maxProfit(prices):
    min_price = float('inf')
    max_profit = 0
    
    for price in prices:
        min_price = min(min_price, price)
        max_profit = max(max_profit, price - min_price)
    
    return max_profit

# Unlimited transactions
def maxProfit_unlimited(prices):
    profit = 0
    
    for i in range(1, len(prices)):
        if prices[i] > prices[i-1]:
            profit += prices[i] - prices[i-1]
    
    return profit

# Two transactions
def maxProfit_two(prices):
    if not prices:
        return 0
    
    # Track profit after first and second transaction
    buy1 = buy2 = float('-inf')
    sell1 = sell2 = 0
    
    for price in prices:
        buy1 = max(buy1, -price)
        sell1 = max(sell1, buy1 + price)
        buy2 = max(buy2, sell1 - price)
        sell2 = max(sell2, buy2 + price)
    
    return sell2
```

---

## 🎨 Dry Run Example

### Kadane's Algorithm

```
Input: [-2, 1, -3, 4, -1, 2, 1, -5, 4]

Step-by-step:
i=0: num=-2, current_sum=max(-2, 0-2)=-2, max_sum=-2
i=1: num=1,  current_sum=max(1, -2+1)=1, max_sum=1
i=2: num=-3, current_sum=max(-3, 1-3)=-2, max_sum=1
i=3: num=4,  current_sum=max(4, -2+4)=4, max_sum=4
i=4: num=-1, current_sum=max(-1, 4-1)=3, max_sum=4
i=5: num=2,  current_sum=max(2, 3+2)=5, max_sum=5
i=6: num=1,  current_sum=max(1, 5+1)=6, max_sum=6
i=7: num=-5, current_sum=max(-5, 6-5)=1, max_sum=6
i=8: num=4,  current_sum=max(4, 1+4)=5, max_sum=6

Result: 6 (subarray [4,-1,2,1])
```

---

## ⏱️ Complexity Analysis

| Pattern | Time | Space |
|---------|------|-------|
| Two Pointers | O(n) | O(1) |
| Prefix Sum | O(n) | O(n) |
| Kadane's | O(n) | O(1) |
| Dutch Flag | O(n) | O(1) |
| Sliding Window Max | O(n) | O(k) |
| Merge Intervals | O(n log n) | O(1) |

---

## 🎯 Must-Know Problems

### Easy
- [26. Remove Duplicates](https://leetcode.com/problems/remove-duplicates-from-sorted-array/)
- [27. Remove Element](https://leetcode.com/problems/remove-element/)
- [53. Maximum Subarray](https://leetcode.com/problems/maximum-subarray/)
- [121. Best Time to Buy and Sell Stock](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/)
- [283. Move Zeroes](https://leetcode.com/problems/move-zeroes/)

### Medium
- [15. 3Sum](https://leetcode.com/problems/3sum/)
- [33. Search in Rotated Sorted Array](https://leetcode.com/problems/search-in-rotated-sorted-array/)
- [56. Merge Intervals](https://leetcode.com/problems/merge-intervals/)
- [75. Sort Colors](https://leetcode.com/problems/sort-colors/)
- [152. Maximum Product Subarray](https://leetcode.com/problems/maximum-product-subarray/)
- [238. Product of Array Except Self](https://leetcode.com/problems/product-of-array-except-self/)
- [560. Subarray Sum Equals K](https://leetcode.com/problems/subarray-sum-equals-k/)

### Hard
- [42. Trapping Rain Water](https://leetcode.com/problems/trapping-rain-water/)
- [239. Sliding Window Maximum](https://leetcode.com/problems/sliding-window-maximum/)

---

## 💡 Pro Tips

1. **In-place modification**: Use two pointers to avoid extra space
2. **Prefix sum**: Precompute for range queries
3. **Kadane's**: Track current and global max
4. **Dutch flag**: Three-way partitioning in one pass
5. **Cyclic sort**: For arrays with elements in range [1, n]
6. **Negative marking**: Use array values as indices

---

**Arrays are fundamental - master these patterns! 📊**
