# 🗂️ Heap (Priority Queue) - Comprehensive Cheatsheet

## 📚 Core Concepts

### What is a Heap?
A complete binary tree that satisfies the heap property:
- **Min Heap**: Parent ≤ Children
- **Max Heap**: Parent ≥ Children

### Why Use Heaps?
✅ Get min/max in O(1)  
✅ Insert/Delete in O(log n)  
✅ Perfect for priority queues  
✅ Heap sort in O(n log n)

---

## Python Implementation

```python
import heapq

# Python's heapq is MIN HEAP by default

# Create heap
heap = []
heapq.heapify([3, 1, 4, 1, 5, 9])  # [1, 1, 4, 3, 5, 9]

# Push element - O(log n)
heapq.heappush(heap, 2)

# Pop minimum - O(log n)
min_val = heapq.heappop(heap)

# Peek minimum - O(1)
min_val = heap[0]

# Push and pop - O(log n)
replaced = heapq.heapreplace(heap, 6)  # Pop then push

# Pop and push - O(log n)
popped = heapq.heappushpop(heap, 0)  # Push then pop

# N smallest/largest - O(n log k)
smallest_3 = heapq.nsmallest(3, [1, 3, 5, 7, 9])
largest_3 = heapq.nlargest(3, [1, 3, 5, 7, 9])
```

### Max Heap Trick
```python
# Multiply by -1 for max heap
max_heap = []
heapq.heappush(max_heap, -5)
heapq.heappush(max_heap, -3)
heapq.heappush(max_heap, -10)

max_val = -heapq.heappop(max_heap)  # 10
```

---

## Common Patterns

### Pattern 1: Top K Elements

```python
def top_k_frequent(nums, k):
    from collections import Counter
    count = Counter(nums)
    return heapq.nlargest(k, count.keys(), key=count.get)

# Alternative with min heap
def top_k_frequent_heap(nums, k):
    count = {}
    for num in nums:
        count[num] = count.get(num, 0) + 1
    
    heap = []
    for num, freq in count.items():
        heapq.heappush(heap, (freq, num))
        if len(heap) > k:
            heapq.heappop(heap)
    
    return [num for freq, num in heap]
```

### Pattern 2: Merge K Sorted

```python
def merge_k_sorted(lists):
    heap = []
    result = []
    
    # Add first element from each list
    for i, lst in enumerate(lists):
        if lst:
            heapq.heappush(heap, (lst[0], i, 0))
    
    while heap:
        val, list_idx, elem_idx = heapq.heappop(heap)
        result.append(val)
        
        if elem_idx + 1 < len(lists[list_idx]):
            next_val = lists[list_idx][elem_idx + 1]
            heapq.heappush(heap, (next_val, list_idx, elem_idx + 1))
    
    return result
```

### Pattern 3: Running Median

```python
class MedianFinder:
    def __init__(self):
        self.small = []  # max heap (left half)
        self.large = []  # min heap (right half)
    
    def addNum(self, num):
        # Add to max heap (invert for max)
        heapq.heappush(self.small, -num)
        
        # Balance: move max of small to large
        if self.small and self.large and (-self.small[0] > self.large[0]):
            val = -heapq.heappop(self.small)
            heapq.heappush(self.large, val)
        
        # Balance sizes
        if len(self.small) > len(self.large) + 1:
            val = -heapq.heappop(self.small)
            heapq.heappush(self.large, val)
        if len(self.large) > len(self.small):
            val = heapq.heappop(self.large)
            heapq.heappush(self.small, -val)
    
    def findMedian(self):
        if len(self.small) > len(self.large):
            return -self.small[0]
        return (-self.small[0] + self.large[0]) / 2
```

### Pattern 4: Task Scheduler

```python
def leastInterval(tasks, n):
    from collections import Counter
    count = Counter(tasks)
    max_heap = [-freq for freq in count.values()]
    heapq.heapify(max_heap)
    
    time = 0
    queue = deque()  # (freq, available_time)
    
    while max_heap or queue:
        time += 1
        
        if max_heap:
            freq = heapq.heappop(max_heap) + 1
            if freq:
                queue.append((freq, time + n))
        
        if queue and queue[0][1] == time:
            heapq.heappush(max_heap, queue.popleft()[0])
    
    return time
```

---

## 🎯 Must-Know Problems

### Easy
- [703. Kth Largest Element in Stream](https://leetcode.com/problems/kth-largest-element-in-a-stream/)
- [1046. Last Stone Weight](https://leetcode.com/problems/last-stone-weight/)

### Medium
- [215. Kth Largest Element](https://leetcode.com/problems/kth-largest-element-in-an-array/)
- [347. Top K Frequent Elements](https://leetcode.com/problems/top-k-frequent-elements/)
- [295. Find Median from Data Stream](https://leetcode.com/problems/find-median-from-data-stream/)
- [621. Task Scheduler](https://leetcode.com/problems/task-scheduler/)
- [973. K Closest Points to Origin](https://leetcode.com/problems/k-closest-points-to-origin/)

### Hard
- [23. Merge k Sorted Lists](https://leetcode.com/problems/merge-k-sorted-lists/)
- [502. IPO](https://leetcode.com/problems/ipo/)
- [774. Minimize Max Distance to Gas Station](https://leetcode.com/problems/minimize-max-distance-to-gas-station/)

---

## ⏱️ Complexity

| Operation | Time | Space |
|-----------|------|-------|
| Build heap | O(n) | O(1) |
| Insert | O(log n) | O(1) |
| Delete/Pop | O(log n) | O(1) |
| Peek | O(1) | O(1) |
| Search | O(n) | O(1) |
| Heapify array | O(n) | O(1) |

---

**Master heaps for efficient priority-based problems! 🗂️**
