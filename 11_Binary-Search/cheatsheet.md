# 🔍 Binary Search - Comprehensive Cheatsheet

## Core Template

```python
def binary_search(arr, target):
    left, right = 0, len(arr) - 1
    
    while left <= right:
        mid = left + (right - left) // 2  # Avoid overflow
        
        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    
    return -1  # Not found
```

## Why `mid = left + (right - left) // 2`?
Avoids integer overflow: `(left + right) // 2` can overflow!

Alternative: `mid = (left + right) >> 1` (right shift by 1)

---

## Patterns

### Pattern 1: Find Exact Value
```python
def search(arr, target):
    left, right = 0, len(arr) - 1
    
    while left <= right:
        mid = left + (right - left) // 2
        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    
    return -1
```

### Pattern 2: Find Leftmost (First Occurrence)
```python
def find_first(arr, target):
    left, right = 0, len(arr) - 1
    result = -1
    
    while left <= right:
        mid = left + (right - left) // 2
        if arr[mid] == target:
            result = mid
            right = mid - 1  # Continue searching left
        elif arr[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    
    return result
```

### Pattern 3: Find Rightmost (Last Occurrence)
```python
def find_last(arr, target):
    left, right = 0, len(arr) - 1
    result = -1
    
    while left <= right:
        mid = left + (right - left) // 2
        if arr[mid] == target:
            result = mid
            left = mid + 1  # Continue searching right
        elif arr[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    
    return result
```

### Pattern 4: Find Insertion Position
```python
def search_insert(arr, target):
    left, right = 0, len(arr) - 1
    
    while left <= right:
        mid = left + (right - left) // 2
        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    
    return left  # Insertion position
```

### Pattern 5: Search in Rotated Array
```python
def search_rotated(arr, target):
    left, right = 0, len(arr) - 1
    
    while left <= right:
        mid = left + (right - left) // 2
        
        if arr[mid] == target:
            return mid
        
        # Left half sorted
        if arr[left] <= arr[mid]:
            if arr[left] <= target < arr[mid]:
                right = mid - 1
            else:
                left = mid + 1
        # Right half sorted
        else:
            if arr[mid] < target <= arr[right]:
                left = mid + 1
            else:
                right = mid - 1
    
    return -1
```

### Pattern 6: Find Peak Element
```python
def find_peak(arr):
    left, right = 0, len(arr) - 1
    
    while left < right:
        mid = left + (right - left) // 2
        
        if arr[mid] < arr[mid + 1]:
            left = mid + 1
        else:
            right = mid
    
    return left
```

### Pattern 7: Search in 2D Matrix
```python
def search_matrix(matrix, target):
    if not matrix:
        return False
    
    m, n = len(matrix), len(matrix[0])
    left, right = 0, m * n - 1
    
    while left <= right:
        mid = left + (right - left) // 2
        mid_val = matrix[mid // n][mid % n]
        
        if mid_val == target:
            return True
        elif mid_val < target:
            left = mid + 1
        else:
            right = mid - 1
    
    return False
```

### Pattern 8: Binary Search on Answer
```python
# Minimum capacity to ship packages within D days
def ship_within_days(weights, days):
    def can_ship(capacity):
        days_needed = 1
        current_load = 0
        
        for weight in weights:
            if current_load + weight > capacity:
                days_needed += 1
                current_load = 0
            current_load += weight
        
        return days_needed <= days
    
    left = max(weights)
    right = sum(weights)
    
    while left < right:
        mid = left + (right - left) // 2
        if can_ship(mid):
            right = mid
        else:
            left = mid + 1
    
    return left
```

---

## 🎯 Key Problems

### Easy
- [704. Binary Search](https://leetcode.com/problems/binary-search/)
- [35. Search Insert Position](https://leetcode.com/problems/search-insert-position/)
- [69. Sqrt(x)](https://leetcode.com/problems/sqrtx/)

### Medium
- [33. Search in Rotated Sorted Array](https://leetcode.com/problems/search-in-rotated-sorted-array/)
- [34. Find First and Last Position](https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/)
- [153. Find Minimum in Rotated Sorted Array](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/)
- [162. Find Peak Element](https://leetcode.com/problems/find-peak-element/)
- [875. Koko Eating Bananas](https://leetcode.com/problems/koko-eating-bananas/)
- [1011. Capacity To Ship Packages](https://leetcode.com/problems/capacity-to-ship-packages-within-d-days/)

### Hard
- [4. Median of Two Sorted Arrays](https://leetcode.com/problems/median-of-two-sorted-arrays/)
- [410. Split Array Largest Sum](https://leetcode.com/problems/split-array-largest-sum/)

---

## Pro Tips

1. **Always use `left + (right - left) // 2`**
2. **Boundary conditions**: `left <= right` vs `left < right`
3. **Binary search on answer**: If you can verify in O(n), search in O(log n)
4. **Rotated arrays**: Identify which half is sorted

---

**Binary search reduces O(n) to O(log n)! 🔍**
