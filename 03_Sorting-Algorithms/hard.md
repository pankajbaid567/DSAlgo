# 🔥 Sorting - Hard Problems Collection

A comprehensive collection of challenging Sorting problems with custom comparators, special algorithms, and detailed explanations.

---

## 📚 Table of Contents

1. [Merge k Sorted Lists](#problem-1-merge-k-sorted-lists)
2. [Count of Range Sum](#problem-2-count-of-range-sum)
3. [Reverse Pairs](#problem-3-reverse-pairs)
4. [Largest Number](#problem-4-largest-number)
5. [Sort Items by Groups Respecting Dependencies](#problem-5-sort-items-by-groups-respecting-dependencies)
6. [Pattern Summary](#-pattern-summary)

---

## Problem 1: Merge k Sorted Lists

**LeetCode 23 - Hard**

### Problem Statement
Merge k sorted linked lists into one sorted list.

```
Input: lists = [[1,4,5],[1,3,4],[2,6]]
Output: [1,1,2,3,4,4,5,6]
```

### 🎯 Intuition
**Multiple approaches:**
1. **Heap (Priority Queue):** Min heap of size k
2. **Divide and Conquer:** Merge pairs recursively
3. **Sequential Merge:** Merge one by one

### Solution 1: Min Heap

```python
import heapq
from typing import List, Optional

class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next
    
    def __lt__(self, other):
        return self.val < other.val

def mergeKLists(lists: List[Optional[ListNode]]) -> Optional[ListNode]:
    """
    Heap-based merging.
    
    Logic:
    - Use min heap to track smallest elements
    - Always extract minimum
    - Add next element from same list
    
    Time: O(N log k) where N = total nodes, k = lists
    Space: O(k) for heap
    """
    heap = []
    
    # Initialize heap with first node from each list
    for i, head in enumerate(lists):
        if head:
            heapq.heappush(heap, (head.val, i, head))
    
    dummy = ListNode(0)
    current = dummy
    
    while heap:
        val, i, node = heapq.heappop(heap)
        current.next = node
        current = current.next
        
        if node.next:
            heapq.heappush(heap, (node.next.val, i, node.next))
    
    return dummy.next

# Example usage
# lists = [[1,4,5],[1,3,4],[2,6]]
# Result: [1,1,2,3,4,4,5,6]
```

### Solution 2: Divide and Conquer

```python
def mergeKLists_dc(lists: List[Optional[ListNode]]) -> Optional[ListNode]:
    """
    Divide and conquer approach.
    
    Logic:
    - Pair-wise merge lists
    - Recursively merge halves
    
    Time: O(N log k)
    Space: O(log k) recursion stack
    """
    if not lists:
        return None
    if len(lists) == 1:
        return lists[0]
    
    def merge_two(l1, l2):
        dummy = ListNode(0)
        current = dummy
        
        while l1 and l2:
            if l1.val < l2.val:
                current.next = l1
                l1 = l1.next
            else:
                current.next = l2
                l2 = l2.next
            current = current.next
        
        current.next = l1 or l2
        return dummy.next
    
    # Divide and conquer
    interval = 1
    while interval < len(lists):
        for i in range(0, len(lists) - interval, interval * 2):
            lists[i] = merge_two(lists[i], lists[i + interval])
        interval *= 2
    
    return lists[0]
```

### ⏱️ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Min Heap | O(N log k) | O(k) | ⭐ Best average |
| Divide & Conquer | O(N log k) | O(log k) | Optimal space |
| Sequential | O(kN) | O(1) | Avoid |

---

## Problem 2: Count of Range Sum

**LeetCode 327 - Hard**

### Problem Statement
Count number of range sums in [lower, upper].

```
Input: nums = [-2,5,-1], lower = -2, upper = 2
Output: 3
Explanation: [0,0], [2,2], [0,2]
```

### 🎯 Intuition
**Merge sort with counting:**
- Use prefix sums
- During merge, count valid ranges
- Left prefix sum - right prefix sum ∈ [lower, upper]

### Solution

```python
def countRangeSum(nums: List[int], lower: int, upper: int) -> int:
    """
    Merge sort with range counting.
    
    Logic:
    - Compute prefix sums
    - During merge sort, count valid ranges
    - For each left sum, find right sums in range
    
    Time: O(n log n)
    Space: O(n)
    """
    def merge_sort(sums):
        if len(sums) <= 1:
            return 0
        
        mid = len(sums) // 2
        count = merge_sort(sums[:mid]) + merge_sort(sums[mid:])
        
        # Count ranges
        j = k = mid
        for i in range(mid):
            # Find valid range for sums[i]
            while j < len(sums) and sums[j] - sums[i] < lower:
                j += 1
            while k < len(sums) and sums[k] - sums[i] <= upper:
                k += 1
            count += k - j
        
        # Merge
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

| Metric | Complexity | Notes |
|--------|------------|-------|
| Time | O(n log n) | Merge sort |
| Space | O(n) | Prefix sums |

---

## Problem 3: Reverse Pairs

**LeetCode 493 - Hard**

### Problem Statement
Count reverse pairs where i < j and nums[i] > 2 * nums[j].

```
Input: nums = [1,3,2,3,1]
Output: 2
Explanation: (3,1) and (3,1)
```

### Solution

```python
def reversePairs(nums: List[int]) -> int:
    """
    Merge sort with pair counting.
    
    Logic:
    - During merge sort, count reverse pairs
    - For each left element, count valid right elements
    
    Time: O(n log n)
    Space: O(n)
    """
    def merge_sort(arr):
        if len(arr) <= 1:
            return 0
        
        mid = len(arr) // 2
        left = arr[:mid]
        right = arr[mid:]
        
        count = merge_sort(left) + merge_sort(right)
        
        # Count reverse pairs
        j = 0
        for i in range(len(left)):
            while j < len(right) and left[i] > 2 * right[j]:
                j += 1
            count += j
        
        # Merge
        arr[:] = sorted(left + right)
        return count
    
    return merge_sort(nums[:])

# Example usage
print(reversePairs([1,3,2,3,1]))  # Output: 2
```

### Alternative: Binary Indexed Tree

```python
from bisect import bisect_left, insort

def reversePairs_bit(nums: List[int]) -> int:
    """
    BIT approach with coordinate compression.
    
    Time: O(n log n)
    Space: O(n)
    """
    # Coordinate compression
    sorted_nums = sorted(set(nums + [2*x for x in nums]))
    ranks = {v: i for i, v in enumerate(sorted_nums)}
    
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
    
    bit = BIT(len(sorted_nums))
    count = 0
    
    for num in reversed(nums):
        count += bit.query(ranks[2 * num] - 1) if ranks[2 * num] > 0 else 0
        bit.update(ranks[num])
    
    return count
```

### ⏱️ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Merge Sort | O(n log n) | O(n) | ⭐ Clean |
| BIT | O(n log n) | O(n) | Advanced |

---

## Problem 4: Largest Number

**LeetCode 179 - Medium/Hard**

### Problem Statement
Arrange numbers to form largest number.

```
Input: nums = [10,2]
Output: "210"

Input: nums = [3,30,34,5,9]
Output: "9534330"
```

### 🎯 Intuition
**Custom comparator:**
- Compare concatenations: xy vs yx
- Sort in descending order of concatenations
- Handle edge case: all zeros

### Solution

```python
from functools import cmp_to_key

def largestNumber(nums: List[int]) -> str:
    """
    Custom comparator sorting.
    
    Logic:
    - Compare by concatenation
    - xy > yx → x comes before y
    
    Time: O(n log n * k) where k = avg digits
    Space: O(n)
    """
    # Convert to strings
    nums_str = list(map(str, nums))
    
    # Custom comparator
    def compare(x, y):
        if x + y > y + x:
            return -1
        elif x + y < y + x:
            return 1
        else:
            return 0
    
    # Sort
    nums_str.sort(key=cmp_to_key(compare))
    
    # Edge case: all zeros
    result = ''.join(nums_str)
    return '0' if result[0] == '0' else result

# Example usage
print(largestNumber([10,2]))        # "210"
print(largestNumber([3,30,34,5,9])) # "9534330"
```

### Without functools

```python
def largestNumber_manual(nums: List[int]) -> str:
    """
    Manual implementation without cmp_to_key.
    
    Time: O(n log n * k)
    Space: O(n)
    """
    nums_str = list(map(str, nums))
    
    # Bubble sort with custom comparison (can use any sort)
    for i in range(len(nums_str)):
        for j in range(i + 1, len(nums_str)):
            if nums_str[i] + nums_str[j] < nums_str[j] + nums_str[i]:
                nums_str[i], nums_str[j] = nums_str[j], nums_str[i]
    
    result = ''.join(nums_str)
    return '0' if result[0] == '0' else result
```

### ⏱️ Complexity Analysis

| Metric | Complexity | Notes |
|--------|------------|-------|
| Time | O(n log n * k) | k = avg string length |
| Space | O(n * k) | String storage |

---

## Problem 5: Sort Items by Groups Respecting Dependencies

**LeetCode 1203 - Hard**

### Problem Statement
Sort items considering group membership and item dependencies.

```
Input: n = 8, m = 2, 
       group = [-1,-1,1,0,0,1,0,-1],
       beforeItems = [[],[6],[5],[6],[3,6],[],[],[]]
Output: [6,3,4,1,5,2,0,7]
```

### Solution

```python
from collections import defaultdict, deque

def sortItems(n: int, m: int, group: List[int], beforeItems: List[List[int]]) -> List[int]:
    """
    Topological sort on two levels.
    
    Logic:
    - Assign unique groups to ungrouped items
    - Build item dependency graph
    - Build group dependency graph
    - Topological sort groups, then items within each group
    
    Time: O(n + E) where E = edges
    Space: O(n + E)
    """
    # Assign groups to ungrouped items
    for i in range(n):
        if group[i] == -1:
            group[i] = m
            m += 1
    
    # Build graphs
    item_graph = defaultdict(list)
    item_indegree = [0] * n
    group_graph = defaultdict(list)
    group_indegree = [0] * m
    
    for i in range(n):
        for before in beforeItems[i]:
            item_graph[before].append(i)
            item_indegree[i] += 1
            
            if group[before] != group[i]:
                group_graph[group[before]].append(group[i])
                group_indegree[group[i]] += 1
    
    # Remove duplicate group edges
    for g in range(m):
        group_graph[g] = list(set(group_graph[g]))
        for next_g in group_graph[g]:
            if next_g != g:
                pass  # Already counted
    
    # Recalculate group indegrees
    group_indegree = [0] * m
    for g in range(m):
        for next_g in group_graph[g]:
            group_indegree[next_g] += 1
    
    # Topological sort for groups
    def topo_sort(graph, indegree, nodes):
        queue = deque([node for node in nodes if indegree[node] == 0])
        result = []
        
        while queue:
            node = queue.popleft()
            result.append(node)
            
            for neighbor in graph[node]:
                indegree[neighbor] -= 1
                if indegree[neighbor] == 0:
                    queue.append(neighbor)
        
        return result if len(result) == len(nodes) else []
    
    # Sort groups
    sorted_groups = topo_sort(group_graph, group_indegree, range(m))
    if not sorted_groups:
        return []
    
    # Sort items within each group
    result = []
    for g in sorted_groups:
        items_in_group = [i for i in range(n) if group[i] == g]
        sorted_items = topo_sort(item_graph, item_indegree, items_in_group)
        
        if len(sorted_items) != len(items_in_group):
            return []
        
        result.extend(sorted_items)
    
    return result

# Example usage
n = 8
m = 2
group = [-1,-1,1,0,0,1,0,-1]
beforeItems = [[],[6],[5],[6],[3,6],[],[],[]]
print(sortItems(n, m, group, beforeItems))
# Output: [6,3,4,1,5,2,0,7] or other valid order
```

### ⏱️ Complexity Analysis

| Metric | Complexity | Notes |
|--------|------------|-------|
| Time | O(n + E) | Topological sorts |
| Space | O(n + E) | Graphs |

---

## 🎯 Pattern Summary

### Core Sorting Patterns

1. **Merge k Sorted** - Heap or divide & conquer
2. **Counting During Sort** - Modified merge sort
3. **Custom Comparator** - Define ordering rule
4. **Topological Sort** - DAG ordering with dependencies
5. **Coordinate Compression** - Map values to ranks

### Problem Categories

| Category | Problems | Key Technique |
|----------|----------|---------------|
| Merge Multiple | k Sorted Lists | Min heap |
| Count Inversions | Range Sum, Reverse Pairs | Merge sort |
| Custom Order | Largest Number | Comparator |
| Dependencies | Sort Items by Groups | Topo sort |

### Advanced Sorting Techniques

```python
# 1. Merge k sorted with heap
import heapq
heap = [(arr[0], i, 0) for i, arr in enumerate(arrays) if arr]
while heap:
    val, arr_idx, elem_idx = heapq.heappop(heap)
    result.append(val)
    if elem_idx + 1 < len(arrays[arr_idx]):
        heapq.heappush(heap, (...))

# 2. Modified merge sort for counting
def merge_count(arr):
    if len(arr) <= 1:
        return 0, arr
    
    mid = len(arr) // 2
    count_left, left = merge_count(arr[:mid])
    count_right, right = merge_count(arr[mid:])
    
    # Count inversions during merge
    count_cross = count_inversions(left, right)
    merged = merge(left, right)
    
    return count_left + count_right + count_cross, merged

# 3. Custom comparator
from functools import cmp_to_key
def compare(x, y):
    if x + y > y + x:
        return -1
    return 1
arr.sort(key=cmp_to_key(compare))

# 4. Topological sort
def topo_sort(graph, indegree):
    queue = [node for node in nodes if indegree[node] == 0]
    result = []
    
    while queue:
        node = queue.pop(0)
        result.append(node)
        
        for neighbor in graph[node]:
            indegree[neighbor] -= 1
            if indegree[neighbor] == 0:
                queue.append(neighbor)
    
    return result if len(result) == len(nodes) else []
```

### Complexity Quick Reference

| Algorithm | Time | Space | Use Case |
|-----------|------|-------|----------|
| Merge Sort | O(n log n) | O(n) | Counting inversions |
| Heap Sort | O(n log k) | O(k) | Merge k sorted |
| Custom Sort | O(n log n * k) | O(n) | Special ordering |
| Topo Sort | O(V + E) | O(V + E) | Dependencies |

### Interview Tips

1. **Heap for k-way:** Always consider heap for merging multiple sorted
2. **Modified merge sort:** Great for counting inversions/pairs
3. **Custom comparator:** Define clear comparison logic
4. **Topo sort:** Check for cycles (return empty if cycle)
5. **Stability:** Does order of equal elements matter?

### Common Pitfalls

1. **Not handling duplicates** in custom comparators
2. **Forgetting edge cases** (empty arrays, all zeros)
3. **Inefficient string comparisons** (cache results)
4. **Cycle detection** in topological sort
5. **Overflow** in counting problems

Master these sorting patterns for interview success! 🚀

