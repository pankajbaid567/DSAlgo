# 🔄 Sorting Algorithms - Comprehensive Cheatsheet

## 📚 Core Concepts

**Sorting**: Arranging elements in a specific order (ascending/descending).

### Comparison-Based Sorts
- Bubble Sort
- Selection Sort
- Insertion Sort
- Merge Sort
- Quick Sort
- Heap Sort

### Non-Comparison Sorts
- Counting Sort
- Radix Sort
- Bucket Sort

---

## Sorting Algorithm Comparison

| Algorithm | Best | Average | Worst | Space | Stable | In-place |
|-----------|------|---------|-------|-------|--------|----------|
| Bubble Sort | O(n) | O(n²) | O(n²) | O(1) | ✓ | ✓ |
| Selection Sort | O(n²) | O(n²) | O(n²) | O(1) | ✗ | ✓ |
| Insertion Sort | O(n) | O(n²) | O(n²) | O(1) | ✓ | ✓ |
| Merge Sort | O(n log n) | O(n log n) | O(n log n) | O(n) | ✓ | ✗ |
| Quick Sort | O(n log n) | O(n log n) | O(n²) | O(log n) | ✗ | ✓ |
| Heap Sort | O(n log n) | O(n log n) | O(n log n) | O(1) | ✗ | ✓ |
| Counting Sort | O(n+k) | O(n+k) | O(n+k) | O(k) | ✓ | ✗ |
| Radix Sort | O(d(n+k)) | O(d(n+k)) | O(d(n+k)) | O(n+k) | ✓ | ✗ |

---

## 1. Bubble Sort

### Concept
Repeatedly swap adjacent elements if they're in wrong order.

### Implementation
```python
def bubbleSort(arr):
    n = len(arr)
    
    for i in range(n):
        swapped = False
        
        # Last i elements are already sorted
        for j in range(0, n - i - 1):
            if arr[j] > arr[j + 1]:
                arr[j], arr[j + 1] = arr[j + 1], arr[j]
                swapped = True
        
        # If no swaps, array is sorted
        if not swapped:
            break
    
    return arr
```

### Dry Run
```
Input: [64, 34, 25, 12, 22, 11, 90]

Pass 1:
[64, 34, 25, 12, 22, 11, 90]
[34, 64, 25, 12, 22, 11, 90] → swap
[34, 25, 64, 12, 22, 11, 90] → swap
[34, 25, 12, 64, 22, 11, 90] → swap
[34, 25, 12, 22, 64, 11, 90] → swap
[34, 25, 12, 22, 11, 64, 90] → swap
[34, 25, 12, 22, 11, 64, 90] → 90 in position

Pass 2:
[25, 34, 12, 22, 11, 64, 90]
[25, 12, 34, 22, 11, 64, 90]
[25, 12, 22, 34, 11, 64, 90]
[25, 12, 22, 11, 34, 64, 90] → 64 in position

...continue until sorted
```

**Time**: O(n²), **Space**: O(1), **Stable**: Yes

---

## 2. Selection Sort

### Concept
Find minimum element and place it at beginning.

### Implementation
```python
def selectionSort(arr):
    n = len(arr)
    
    for i in range(n):
        # Find minimum in remaining array
        min_idx = i
        
        for j in range(i + 1, n):
            if arr[j] < arr[min_idx]:
                min_idx = j
        
        # Swap with current position
        arr[i], arr[min_idx] = arr[min_idx], arr[i]
    
    return arr
```

### Visualization
```
[64, 25, 12, 22, 11]
 ↓
[11, 25, 12, 22, 64] → min=11, swap with 64
     ↓
[11, 12, 25, 22, 64] → min=12, swap with 25
         ↓
[11, 12, 22, 25, 64] → min=22, swap with 25
             ↓
[11, 12, 22, 25, 64] → sorted
```

**Time**: O(n²), **Space**: O(1), **Stable**: No

---

## 3. Insertion Sort

### Concept
Build sorted array one element at a time by inserting elements in correct position.

### Implementation
```python
def insertionSort(arr):
    for i in range(1, len(arr)):
        key = arr[i]
        j = i - 1
        
        # Move elements greater than key one position ahead
        while j >= 0 and arr[j] > key:
            arr[j + 1] = arr[j]
            j -= 1
        
        arr[j + 1] = key
    
    return arr
```

### Visualization
```
[12, 11, 13, 5, 6]

i=1: key=11
[12, 12, 13, 5, 6] → shift 12
[11, 12, 13, 5, 6] → insert 11

i=2: key=13
[11, 12, 13, 5, 6] → already in place

i=3: key=5
[11, 12, 13, 13, 6] → shift
[11, 12, 12, 13, 6] → shift
[11, 11, 12, 13, 6] → shift
[5, 11, 12, 13, 6]  → insert 5

i=4: key=6
[5, 11, 12, 13, 13] → shift
[5, 11, 12, 12, 13] → shift
[5, 11, 11, 12, 13] → shift
[5, 6, 11, 12, 13]  → insert 6
```

**Time**: O(n²), **Space**: O(1), **Stable**: Yes

---

## 4. Merge Sort

### Concept
Divide array into halves, recursively sort them, then merge.

### Implementation
```python
def mergeSort(arr):
    if len(arr) <= 1:
        return arr
    
    mid = len(arr) // 2
    left = mergeSort(arr[:mid])
    right = mergeSort(arr[mid:])
    
    return merge(left, right)

def merge(left, right):
    result = []
    i = j = 0
    
    # Merge two sorted arrays
    while i < len(left) and j < len(right):
        if left[i] <= right[j]:
            result.append(left[i])
            i += 1
        else:
            result.append(right[j])
            j += 1
    
    # Add remaining elements
    result.extend(left[i:])
    result.extend(right[j:])
    
    return result
```

### Visualization
```
[38, 27, 43, 3, 9, 82, 10]

Split:
[38, 27, 43, 3]    [9, 82, 10]
[38, 27] [43, 3]   [9, 82] [10]
[38] [27] [43] [3] [9] [82] [10]

Merge:
[27, 38] [3, 43]   [9, 82] [10]
[3, 27, 38, 43]    [9, 10, 82]
[3, 9, 10, 27, 38, 43, 82]
```

**Time**: O(n log n), **Space**: O(n), **Stable**: Yes

---

## 5. Quick Sort

### Concept
Choose pivot, partition array around it, recursively sort subarrays.

### Implementation
```python
def quickSort(arr, low, high):
    if low < high:
        # Partition and get pivot index
        pi = partition(arr, low, high)
        
        # Recursively sort left and right
        quickSort(arr, low, pi - 1)
        quickSort(arr, pi + 1, high)
    
    return arr

def partition(arr, low, high):
    # Choose rightmost as pivot
    pivot = arr[high]
    i = low - 1
    
    for j in range(low, high):
        if arr[j] <= pivot:
            i += 1
            arr[i], arr[j] = arr[j], arr[i]
    
    # Place pivot in correct position
    arr[i + 1], arr[high] = arr[high], arr[i + 1]
    return i + 1

# Wrapper function
def quickSortWrapper(arr):
    return quickSort(arr, 0, len(arr) - 1)
```

### Partition Visualization
```
[10, 80, 30, 90, 40, 50, 70]
pivot = 70, i = -1

j=0: 10 <= 70, i=0, swap arr[0] with arr[0]
[10, 80, 30, 90, 40, 50, 70]

j=1: 80 > 70, no swap

j=2: 30 <= 70, i=1, swap arr[1] with arr[2]
[10, 30, 80, 90, 40, 50, 70]

j=3: 90 > 70, no swap

j=4: 40 <= 70, i=2, swap arr[2] with arr[4]
[10, 30, 40, 90, 80, 50, 70]

j=5: 50 <= 70, i=3, swap arr[3] with arr[5]
[10, 30, 40, 50, 80, 90, 70]

Place pivot: swap arr[4] with arr[6]
[10, 30, 40, 50, 70, 90, 80]
              ↑ pivot index = 4
```

**Time**: O(n log n) avg, O(n²) worst, **Space**: O(log n), **Stable**: No

---

## 6. Heap Sort

### Concept
Build max heap, repeatedly extract maximum.

### Implementation
```python
def heapSort(arr):
    n = len(arr)
    
    # Build max heap
    for i in range(n // 2 - 1, -1, -1):
        heapify(arr, n, i)
    
    # Extract elements one by one
    for i in range(n - 1, 0, -1):
        arr[0], arr[i] = arr[i], arr[0]  # Swap
        heapify(arr, i, 0)
    
    return arr

def heapify(arr, n, i):
    largest = i
    left = 2 * i + 1
    right = 2 * i + 2
    
    # Find largest among root, left, right
    if left < n and arr[left] > arr[largest]:
        largest = left
    
    if right < n and arr[right] > arr[largest]:
        largest = right
    
    # If largest is not root
    if largest != i:
        arr[i], arr[largest] = arr[largest], arr[i]
        heapify(arr, n, largest)
```

**Time**: O(n log n), **Space**: O(1), **Stable**: No

---

## 7. Counting Sort

### Concept
Count occurrences of each element, use counts to place elements.

### Implementation
```python
def countingSort(arr):
    if not arr:
        return arr
    
    # Find range
    max_val = max(arr)
    min_val = min(arr)
    range_val = max_val - min_val + 1
    
    # Count occurrences
    count = [0] * range_val
    output = [0] * len(arr)
    
    for num in arr:
        count[num - min_val] += 1
    
    # Cumulative count
    for i in range(1, range_val):
        count[i] += count[i - 1]
    
    # Build output array
    for i in range(len(arr) - 1, -1, -1):
        output[count[arr[i] - min_val] - 1] = arr[i]
        count[arr[i] - min_val] -= 1
    
    return output
```

**Time**: O(n + k), **Space**: O(k), **Stable**: Yes

---

## 8. Radix Sort

### Concept
Sort digit by digit using counting sort.

### Implementation
```python
def radixSort(arr):
    if not arr:
        return arr
    
    # Find maximum to know number of digits
    max_val = max(arr)
    
    # Do counting sort for every digit
    exp = 1
    while max_val // exp > 0:
        countingSortByDigit(arr, exp)
        exp *= 10
    
    return arr

def countingSortByDigit(arr, exp):
    n = len(arr)
    output = [0] * n
    count = [0] * 10
    
    # Count occurrences of digits
    for i in range(n):
        index = arr[i] // exp
        count[index % 10] += 1
    
    # Cumulative count
    for i in range(1, 10):
        count[i] += count[i - 1]
    
    # Build output array
    for i in range(n - 1, -1, -1):
        index = arr[i] // exp
        output[count[index % 10] - 1] = arr[i]
        count[index % 10] -= 1
    
    # Copy to original array
    for i in range(n):
        arr[i] = output[i]
```

**Time**: O(d(n + k)), **Space**: O(n + k), **Stable**: Yes

---

## 9. Bucket Sort

### Concept
Distribute elements into buckets, sort each bucket.

### Implementation
```python
def bucketSort(arr):
    if not arr:
        return arr
    
    # Create buckets
    bucket_count = len(arr)
    max_val = max(arr)
    min_val = min(arr)
    
    buckets = [[] for _ in range(bucket_count)]
    
    # Distribute elements into buckets
    for num in arr:
        index = int(bucket_count * (num - min_val) / (max_val - min_val + 1))
        buckets[index].append(num)
    
    # Sort individual buckets and concatenate
    result = []
    for bucket in buckets:
        result.extend(sorted(bucket))  # Using built-in sort
    
    return result
```

**Time**: O(n + k), **Space**: O(n + k), **Stable**: Yes

---

## Custom Comparators

### Sort with Lambda
```python
# Sort by absolute value
arr = [-5, 2, -8, 3, -1]
arr.sort(key=lambda x: abs(x))
# Result: [-1, 2, 3, -5, -8]

# Sort strings by length
words = ["apple", "pie", "banana", "cat"]
words.sort(key=len)
# Result: ["pie", "cat", "apple", "banana"]

# Sort tuples by second element
pairs = [(1, 5), (3, 2), (2, 8)]
pairs.sort(key=lambda x: x[1])
# Result: [(3, 2), (1, 5), (2, 8)]
```

### Custom Comparator Class
```python
from functools import cmp_to_key

def compare(a, b):
    # Sort in descending order
    if a > b:
        return -1
    elif a < b:
        return 1
    else:
        return 0

arr = [5, 2, 8, 1, 9]
arr.sort(key=cmp_to_key(compare))
# Result: [9, 8, 5, 2, 1]
```

---

## Special Sorting Problems

### Sort Colors (Dutch National Flag)
```python
def sortColors(nums):
    """Sort 0s, 1s, 2s in one pass"""
    low = mid = 0
    high = len(nums) - 1
    
    while mid <= high:
        if nums[mid] == 0:
            nums[low], nums[mid] = nums[mid], nums[low]
            low += 1
            mid += 1
        elif nums[mid] == 1:
            mid += 1
        else:
            nums[mid], nums[high] = nums[high], nums[mid]
            high -= 1
```

### Wiggle Sort
```python
def wiggleSort(nums):
    """nums[0] <= nums[1] >= nums[2] <= nums[3]..."""
    for i in range(len(nums) - 1):
        if (i % 2 == 0) == (nums[i] > nums[i + 1]):
            nums[i], nums[i + 1] = nums[i + 1], nums[i]
```

---

## 🎯 When to Use Which Sort?

| Use Case | Algorithm | Reason |
|----------|-----------|--------|
| Small arrays (n < 50) | Insertion Sort | Simple, efficient for small data |
| Nearly sorted | Insertion Sort | O(n) for nearly sorted |
| Guaranteed O(n log n) | Merge Sort | Worst case O(n log n) |
| Average case, in-place | Quick Sort | Fast average, O(1) space |
| External sorting | Merge Sort | Sequential access |
| Integer range [0, k] | Counting Sort | Linear time |
| Integers with digits | Radix Sort | Linear time |
| Uniform distribution | Bucket Sort | Linear average |

---

## 🎯 Must-Know Problems

### Easy
- [88. Merge Sorted Array](https://leetcode.com/problems/merge-sorted-array/)
- [75. Sort Colors](https://leetcode.com/problems/sort-colors/)

### Medium
- [147. Insertion Sort List](https://leetcode.com/problems/insertion-sort-list/)
- [148. Sort List](https://leetcode.com/problems/sort-list/)
- [912. Sort an Array](https://leetcode.com/problems/sort-an-array/)
- [215. Kth Largest Element](https://leetcode.com/problems/kth-largest-element-in-an-array/)

### Hard
- [164. Maximum Gap](https://leetcode.com/problems/maximum-gap/)
- [493. Reverse Pairs](https://leetcode.com/problems/reverse-pairs/)

---

## 💡 Pro Tips

1. **Python's Timsort**: `sorted()` and `.sort()` use Timsort (hybrid of merge + insertion)
2. **Stability matters**: When sorting objects with multiple fields
3. **In-place vs extra space**: Trade-off between space and complexity
4. **Recursion depth**: Quick/Merge sort can hit recursion limit
5. **Cache performance**: Merge sort has better cache performance than quick sort

---

**Master sorting algorithms - fundamental to DSA! 🔄**
