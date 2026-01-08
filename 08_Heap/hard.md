# 🔥 Heap - Hard Problems Collection

A curated collection of challenging Heap/Priority Queue problems with complete solutions, multiple approaches, and detailed explanations.

---

## 📚 Table of Contents

1. [Merge K Sorted Lists](#problem-1-merge-k-sorted-lists)
2. [Find Median from Data Stream](#problem-2-find-median-from-data-stream)
3. [Sliding Window Median](#problem-3-sliding-window-median)
4. [IPO (Maximum Capital)](#problem-4-ipo-maximum-capital)
5. [Employee Free Time](#problem-5-employee-free-time)
6. [The Skyline Problem](#problem-6-the-skyline-problem)
7. [Smallest Range Covering K Lists](#problem-7-smallest-range-covering-k-lists)
8. [Pattern Summary](#-pattern-summary)

---

## Problem 1: Merge K Sorted Lists

**LeetCode 23 - Hard**

### Problem Statement
Merge `k` sorted linked lists and return it as one sorted list.

```
Input: lists = [[1,4,5],[1,3,4],[2,6]]
Output: [1,1,2,3,4,4,5,6]
```

### 🎯 Intuition
Use **min heap** to efficiently get the smallest element among k lists:
- Heap maintains one element from each list
- Extract minimum, add to result, push next from that list
- Continue until all lists exhausted

**Why Heap?** Finding minimum among k elements takes O(1) with heap vs O(k) with linear scan.

### 📊 Visual Representation

```
Input Lists:
List 1: 1 → 4 → 5
List 2: 1 → 3 → 4
List 3: 2 → 6

Heap Process:
Initial heap: [(1, List1), (1, List2), (2, List3)]

Step 1: Pop (1, List1), push 4 from List1
  Result: 1
  Heap: [(1, List2), (2, List3), (4, List1)]

Step 2: Pop (1, List2), push 3 from List2
  Result: 1 → 1
  Heap: [(2, List3), (3, List2), (4, List1)]

Step 3: Pop (2, List3), push 6 from List3
  Result: 1 → 1 → 2
  Heap: [(3, List2), (4, List1), (6, List3)]

... continue until all processed

Final: 1 → 1 → 2 → 3 → 4 → 4 → 5 → 6
```

### Approach 1: Min Heap (Optimal)

```python
import heapq
from typing import List, Optional

class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next
    
    # Make ListNode comparable for heap
    def __lt__(self, other):
        return self.val < other.val

def mergeKLists(lists: List[Optional[ListNode]]) -> Optional[ListNode]:
    """
    Use min heap to efficiently merge k sorted lists.
    
    Logic:
    - Initialize heap with head of each list
    - Extract min, add to result
    - Push next node from extracted list
    
    Time: O(N log k) where N = total nodes, k = lists
    Space: O(k) for heap
    """
    # Build initial heap with non-empty list heads
    heap = []
    for i, node in enumerate(lists):
        if node:
            heapq.heappush(heap, (node.val, i, node))
    
    dummy = ListNode(0)
    current = dummy
    
    while heap:
        val, i, node = heapq.heappop(heap)
        
        # Add to result
        current.next = node
        current = current.next
        
        # Push next node from same list
        if node.next:
            heapq.heappush(heap, (node.next.val, i, node.next))
    
    return dummy.next

# Alternative without modifying ListNode
def mergeKLists_tuple(lists: List[Optional[ListNode]]) -> Optional[ListNode]:
    """Use tuple (val, index, node) for heap comparison."""
    heap = []
    
    for i, node in enumerate(lists):
        if node:
            heapq.heappush(heap, (node.val, i, node))
    
    dummy = ListNode(0)
    current = dummy
    
    while heap:
        val, i, node = heapq.heappop(heap)
        current.next = ListNode(val)
        current = current.next
        
        if node.next:
            heapq.heappush(heap, (node.next.val, i, node.next))
    
    return dummy.next
```

### Approach 2: Divide and Conquer

```python
def mergeKLists_DC(lists: List[Optional[ListNode]]) -> Optional[ListNode]:
    """
    Merge lists pair by pair recursively.
    
    Logic:
    - Merge pairs: (0,1), (2,3), (4,5), ...
    - Recursively merge results
    - Similar to merge sort
    
    Time: O(N log k)
    Space: O(log k) recursion stack
    """
    def mergeTwoLists(l1, l2):
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
    
    if not lists:
        return None
    
    while len(lists) > 1:
        merged = []
        for i in range(0, len(lists), 2):
            l1 = lists[i]
            l2 = lists[i + 1] if i + 1 < len(lists) else None
            merged.append(mergeTwoLists(l1, l2))
        lists = merged
    
    return lists[0]
```

### Approach 3: Sequential Merge (Inefficient)

```python
def mergeKLists_sequential(lists: List[Optional[ListNode]]) -> Optional[ListNode]:
    """
    Merge lists one by one.
    
    Time: O(kN) - inefficient
    Space: O(1)
    """
    def mergeTwoLists(l1, l2):
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
    
    if not lists:
        return None
    
    result = lists[0]
    for i in range(1, len(lists)):
        result = mergeTwoLists(result, lists[i])
    
    return result
```

### 🔍 Dry Run

```
lists = [[1,4,5], [1,3,4], [2,6]]

Heap approach:

Initial:
  heap = [(1, 0, Node1a), (1, 1, Node2a), (2, 2, Node3a)]

Step 1: Pop (1, 0, Node1a)
  result: [1]
  push (4, 0, Node1b)
  heap = [(1, 1, Node2a), (2, 2, Node3a), (4, 0, Node1b)]

Step 2: Pop (1, 1, Node2a)
  result: [1, 1]
  push (3, 1, Node2b)
  heap = [(2, 2, Node3a), (3, 1, Node2b), (4, 0, Node1b)]

Step 3: Pop (2, 2, Node3a)
  result: [1, 1, 2]
  push (6, 2, Node3b)
  heap = [(3, 1, Node2b), (4, 0, Node1b), (6, 2, Node3b)]

Continue until heap empty...

Final: [1, 1, 2, 3, 4, 4, 5, 6]
```

### ⏱️ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Min Heap | O(N log k) | O(k) | ⭐ Optimal |
| Divide & Conquer | O(N log k) | O(log k) | Less space |
| Sequential | O(kN) | O(1) | Too slow |

Where N = total nodes, k = number of lists

---

## Problem 2: Find Median from Data Stream

**LeetCode 295 - Hard**

### Problem Statement
Design a data structure that supports:
- `addNum(int num)` - Add integer to data structure
- `findMedian()` - Return median of all elements

```
Input: ["MedianFinder", "addNum", "addNum", "findMedian", "addNum", "findMedian"]
       [[], [1], [2], [], [3], []]
Output: [null, null, null, 1.5, null, 2.0]
```

### 🎯 Intuition
Use **two heaps** to maintain balance:
- **Max heap** (left): Stores smaller half
- **Min heap** (right): Stores larger half

**Key Properties:**
- `len(max_heap) == len(min_heap)` or `len(max_heap) == len(min_heap) + 1`
- `max(max_heap) <= min(min_heap)`
- Median is either `max(max_heap)` or average of tops

### 📊 Visual Representation

```
Stream: 1, 2, 3, 4, 5

After adding 1:
  Max Heap (left): [1]
  Min Heap (right): []
  Median: 1

After adding 2:
  Max Heap: [1]
  Min Heap: [2]
  Median: (1 + 2) / 2 = 1.5

After adding 3:
  Max Heap: [1, 2]
  Min Heap: [3]
  Median: 2

After adding 4:
  Max Heap: [1, 2]
  Min Heap: [3, 4]
  Median: (2 + 3) / 2 = 2.5

After adding 5:
  Max Heap: [1, 2, 3]
  Min Heap: [4, 5]
  Median: 3

Visualization:
  Max Heap ← | → Min Heap
  (smaller)  |  (larger)
     1,2,3   |   4,5
        ↑median↑
```

### Solution

```python
import heapq

class MedianFinder:
    """
    Use two heaps to maintain median.
    
    Logic:
    - Max heap stores smaller half (negate values for Python)
    - Min heap stores larger half
    - Balance heaps to keep sizes equal or differ by 1
    
    Time: 
      - addNum: O(log n)
      - findMedian: O(1)
    Space: O(n)
    """
    
    def __init__(self):
        self.small = []  # Max heap (use negative values)
        self.large = []  # Min heap
    
    def addNum(self, num: int) -> None:
        """Add number to data structure."""
        # Add to max heap (small)
        heapq.heappush(self.small, -num)
        
        # Balance: ensure max(small) <= min(large)
        if self.small and self.large and (-self.small[0] > self.large[0]):
            val = -heapq.heappop(self.small)
            heapq.heappush(self.large, val)
        
        # Balance sizes: small can have at most 1 more than large
        if len(self.small) > len(self.large) + 1:
            val = -heapq.heappop(self.small)
            heapq.heappush(self.large, val)
        
        if len(self.large) > len(self.small):
            val = heapq.heappop(self.large)
            heapq.heappush(self.small, -val)
    
    def findMedian(self) -> float:
        """Return median of all elements."""
        if len(self.small) > len(self.large):
            return -self.small[0]
        return (-self.small[0] + self.large[0]) / 2.0

# Example usage
mf = MedianFinder()
mf.addNum(1)
mf.addNum(2)
print(mf.findMedian())  # 1.5
mf.addNum(3)
print(mf.findMedian())  # 2.0
```

### Alternative: Balanced Approach

```python
class MedianFinder_v2:
    """Alternative balancing strategy."""
    
    def __init__(self):
        self.small = []  # Max heap
        self.large = []  # Min heap
    
    def addNum(self, num: int) -> None:
        # Always add to small first
        heapq.heappush(self.small, -num)
        
        # Move largest from small to large
        heapq.heappush(self.large, -heapq.heappop(self.small))
        
        # Balance if large is bigger
        if len(self.large) > len(self.small):
            heapq.heappush(self.small, -heapq.heappop(self.large))
    
    def findMedian(self) -> float:
        if len(self.small) > len(self.large):
            return -self.small[0]
        return (-self.small[0] + self.large[0]) / 2.0
```

### 🔍 Dry Run

```
Stream: [1, 2, 3]

addNum(1):
  small = [-1], large = []
  Balance: small has 1 more (OK)
  Result: small=[-1], large=[]

addNum(2):
  push -2 to small: small=[-2,-1]
  -small[0]=2 > large[0]? No large yet
  small size = 2, large = 0, balance!
  pop -2 from small, push 2 to large
  Result: small=[-1], large=[2]

addNum(3):
  push -3 to small: small=[-3,-1]
  -small[0]=3 > large[0]=2? YES
  pop -3, push 3 to large
  small=[-1], large=[2,3]
  large size > small size, rebalance
  pop 2 from large, push -2 to small
  Result: small=[-2,-1], large=[3]

findMedian():
  small size = 2, large size = 1
  return -small[0] = 2
```

### ⏱️ Complexity Analysis

| Operation | Time | Space | Notes |
|-----------|------|-------|-------|
| addNum | O(log n) | O(n) | Heap operations |
| findMedian | O(1) | - | Just peek tops |

---

## Problem 3: Sliding Window Median

**LeetCode 480 - Hard**

### Problem Statement
Given array `nums` and integer `k`, return the median of each sliding window of size `k`.

```
Input: nums = [1,3,-1,-3,5,3,6,7], k = 3
Output: [1.0, -1.0, -1.0, 3.0, 5.0, 6.0]
```

### 🎯 Intuition
Extend "Find Median from Data Stream" with **lazy deletion**:
- Two heaps maintain window elements
- Track elements to be removed in hash map
- Clean up stale elements when needed (lazy deletion)

**Challenge:** Can't directly remove from middle of heap in O(log n).

### 📊 Visual Representation

```
nums = [1, 3, -1, -3, 5], k = 3

Window [1, 3, -1]:
  small = [-1], large = [1, 3]
  median = 1

Window [3, -1, -3]:
  Remove 1: mark in delayed map
  Add -3
  small = [-3, -1], large = [3]
  median = -1

Lazy Deletion:
  Instead of removing immediately from heap,
  mark element and clean when it reaches top.
  
  delayed = {1: 1}  # element: count
  When 1 appears at top, pop and decrement count
```

### Solution

```python
import heapq
from collections import defaultdict

def medianSlidingWindow(nums, k):
    """
    Use two heaps with lazy deletion.
    
    Logic:
    - Maintain two heaps for window
    - Use hash map to track delayed removals
    - Clean tops when accessing
    
    Time: O(n log k)
    Space: O(k)
    """
    def clean_heap(heap):
        """Remove invalid elements from top."""
        while heap and delayed[abs(heap[0])] > 0:
            delayed[abs(heap[0])] -= 1
            heapq.heappop(heap)
    
    def get_median():
        """Calculate median after cleaning."""
        clean_heap(small)
        clean_heap(large)
        
        if k % 2 == 1:
            return -small[0]
        return (-small[0] + large[0]) / 2.0
    
    small = []  # Max heap
    large = []  # Min heap
    delayed = defaultdict(int)  # Track delayed removals
    result = []
    
    # Initialize first window
    for i in range(k):
        heapq.heappush(small, -nums[i])
    
    # Balance to ensure small has at most 1 more
    for _ in range(k // 2):
        heapq.heappush(large, -heapq.heappop(small))
    
    result.append(get_median())
    
    # Slide window
    for i in range(k, len(nums)):
        out_num = nums[i - k]
        in_num = nums[i]
        balance = 0
        
        # Mark outgoing number for removal
        delayed[out_num] += 1
        
        # Adjust balance based on which heap it's in
        if out_num <= -small[0]:
            balance -= 1
        else:
            balance += 1
        
        # Add incoming number
        if small and in_num <= -small[0]:
            heapq.heappush(small, -in_num)
            balance += 1
        else:
            heapq.heappush(large, in_num)
            balance -= 1
        
        # Rebalance
        if balance < 0:
            heapq.heappush(small, -heapq.heappop(large))
        elif balance > 0:
            heapq.heappush(large, -heapq.heappop(small))
        
        # Clean and get median
        result.append(get_median())
    
    return result

# Example usage
nums = [1, 3, -1, -3, 5, 3, 6, 7]
k = 3
print(medianSlidingWindow(nums, k))
# Output: [1.0, -1.0, -1.0, 3.0, 5.0, 6.0]
```

### Alternative: Using Multiset (TreeMap in Python)

```python
from sortedcontainers import SortedList

def medianSlidingWindow_sorted(nums, k):
    """
    Use sorted list for O(log n) insertion/deletion.
    
    Time: O(n log k)
    Space: O(k)
    """
    window = SortedList(nums[:k])
    result = []
    
    def get_median():
        if k % 2 == 1:
            return float(window[k // 2])
        return (window[k // 2 - 1] + window[k // 2]) / 2.0
    
    result.append(get_median())
    
    for i in range(k, len(nums)):
        window.remove(nums[i - k])
        window.add(nums[i])
        result.append(get_median())
    
    return result
```

### ⏱️ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Two Heaps | O(n log k) | O(k) | Lazy deletion |
| Sorted List | O(n log k) | O(k) | ⭐ Simpler code |

---

## Problem 4: IPO (Maximum Capital)

**LeetCode 502 - Hard**

### Problem Statement
You have initial capital `w` and can complete at most `k` projects. Each project `i` has pure profit `profits[i]` and requires minimum capital `capital[i]`. Maximize your final capital.

```
Input: k = 2, w = 0, profits = [1,2,3], capital = [0,1,1]
Output: 4
Explanation: Start with 0, do project 0 (profit 1, capital now 1)
             Do project 2 (profit 3, capital now 4)
```

### 🎯 Intuition
Use **two heaps** greedy strategy:
1. **Min heap**: Projects sorted by capital required
2. **Max heap**: Available projects sorted by profit

**Algorithm:**
- While we can do more projects:
  - Move all affordable projects to max heap
  - Pick project with maximum profit
  - Update capital

### 📊 Visual Representation

```
k=2, w=0, profits=[1,2,3], capital=[0,1,1]

Initial:
  capital_heap = [(0,0), (1,1), (1,2)]  # (capital, index)
  profit_heap = []
  current_capital = 0

Round 1:
  Affordable: projects with capital <= 0
    Move (0,0) to profit_heap
  profit_heap = [-1]  # (negative for max heap)
  Pick project 0: profit = 1
  current_capital = 0 + 1 = 1

Round 2:
  Affordable: projects with capital <= 1
    Move (1,1) and (1,2) to profit_heap
  profit_heap = [-3, -2]
  Pick project with profit 3
  current_capital = 1 + 3 = 4

Final capital = 4
```

### Solution

```python
import heapq

def findMaximizedCapital(k, w, profits, capital):
    """
    Use two heaps: min heap for capital, max heap for profits.
    
    Logic:
    - Sort projects by capital required
    - For each round, move affordable projects to profit heap
    - Pick project with max profit
    
    Time: O(n log n)
    Space: O(n)
    """
    n = len(profits)
    
    # Create min heap of (capital, profit) pairs
    projects = [(capital[i], profits[i]) for i in range(n)]
    heapq.heapify(projects)
    
    # Max heap for available projects
    available = []
    
    for _ in range(k):
        # Move all affordable projects to available heap
        while projects and projects[0][0] <= w:
            cap, prof = heapq.heappop(projects)
            heapq.heappush(available, -prof)  # Max heap
        
        # If no projects available, break
        if not available:
            break
        
        # Pick project with maximum profit
        w += -heapq.heappop(available)
    
    return w

# Example usage
k = 2
w = 0
profits = [1, 2, 3]
capital = [0, 1, 1]
print(findMaximizedCapital(k, w, profits, capital))  # Output: 4
```

### Alternative: Sort and Priority Queue

```python
def findMaximizedCapital_v2(k, w, profits, capital):
    """
    Sort projects by capital, use max heap for profits.
    
    Time: O(n log n)
    Space: O(n)
    """
    # Combine and sort by capital
    projects = sorted(zip(capital, profits))
    
    available = []
    idx = 0
    
    for _ in range(k):
        # Add all affordable projects
        while idx < len(projects) and projects[idx][0] <= w:
            heapq.heappush(available, -projects[idx][1])
            idx += 1
        
        if not available:
            break
        
        w += -heapq.heappop(available)
    
    return w
```

### 🔍 Dry Run

```
k=2, w=0, profits=[1,2,3], capital=[0,1,1]

projects after sort by capital:
  [(0,1), (1,2), (1,3)]

Round 1:
  w = 0
  Affordable: (0,1)
  available = [-1]
  Pick: profit = 1
  w = 0 + 1 = 1
  idx = 1

Round 2:
  w = 1
  Affordable: (1,2), (1,3)
  available = [-3, -2]  # Max heap
  Pick: profit = 3
  w = 1 + 3 = 4
  idx = 3

Result: 4
```

### ⏱️ Complexity Analysis

| Operation | Time | Space | Notes |
|-----------|------|-------|-------|
| Sort | O(n log n) | O(n) | Initial sort |
| K rounds | O(k log n) | O(n) | Heap ops |
| **Total** | **O((n+k) log n)** | **O(n)** | ⭐ Optimal |

---

## Problem 5: Employee Free Time

**LeetCode 759 - Hard**

### Problem Statement
Given list of employee schedules `schedule[i]`, where each schedule contains non-overlapping intervals representing working hours, return list of finite intervals representing common free time for all employees.

```
Input: schedule = [[[1,2],[5,6]],[[1,3]],[[4,10]]]
Output: [[3,4]]
Explanation: All employees are free from 3 to 4
```

### 🎯 Intuition
Two approaches:
1. **Merge all intervals** → Find gaps
2. **Min heap** → Track earliest end time across employees

Heap approach is more elegant for multiple employees.

### 📊 Visual Representation

```
Employee 1: [1,2]     [5,6]
Employee 2: [1,3]
Employee 3:         [4,10]

Timeline:
0  1  2  3  4  5  6  7  8  9  10
   |--E1--|  |--E1--|
   |----E2----|
         |--------E3--------|

Merged busy: [1,3][4,10]
Free time: [3,4]

Using Heap:
  Process intervals in chronological order
  Track when someone becomes free
  Find gaps between busy periods
```

### Approach 1: Merge Intervals

```python
class Interval:
    def __init__(self, start, end):
        self.start = start
        self.end = end

def employeeFreeTime(schedule):
    """
    Merge all intervals and find gaps.
    
    Logic:
    - Flatten all intervals
    - Sort by start time
    - Merge overlapping
    - Find gaps between merged intervals
    
    Time: O(n log n) where n = total intervals
    Space: O(n)
    """
    # Flatten all intervals
    intervals = []
    for employee in schedule:
        for interval in employee:
            intervals.append(interval)
    
    # Sort by start time
    intervals.sort(key=lambda x: x.start)
    
    # Merge overlapping intervals
    merged = [intervals[0]]
    for interval in intervals[1:]:
        if interval.start <= merged[-1].end:
            merged[-1].end = max(merged[-1].end, interval.end)
        else:
            merged.append(interval)
    
    # Find gaps
    result = []
    for i in range(len(merged) - 1):
        if merged[i].end < merged[i + 1].start:
            result.append(Interval(merged[i].end, merged[i + 1].start))
    
    return result
```

### Approach 2: Min Heap

```python
import heapq

def employeeFreeTime_heap(schedule):
    """
    Use min heap to process intervals in order.
    
    Logic:
    - Heap of (start, end, employee_idx, interval_idx)
    - Process intervals chronologically
    - Track gaps between consecutive busy periods
    
    Time: O(n log k) where k = number of employees
    Space: O(k)
    """
    # Initialize heap with first interval of each employee
    heap = []
    for i, employee in enumerate(schedule):
        if employee:
            interval = employee[0]
            heapq.heappush(heap, (interval.start, interval.end, i, 0))
    
    result = []
    prev_end = heap[0][0]  # Start of first interval
    
    while heap:
        start, end, emp_idx, int_idx = heapq.heappop(heap)
        
        # If gap exists
        if prev_end < start:
            result.append(Interval(prev_end, start))
        
        prev_end = max(prev_end, end)
        
        # Add next interval from same employee
        if int_idx + 1 < len(schedule[emp_idx]):
            next_interval = schedule[emp_idx][int_idx + 1]
            heapq.heappush(heap, 
                (next_interval.start, next_interval.end, 
                 emp_idx, int_idx + 1))
    
    return result
```

### 🔍 Dry Run

```
schedule = [[[1,2],[5,6]], [[1,3]], [[4,10]]]

Merge approach:

Flatten: [(1,2), (5,6), (1,3), (4,10)]
Sort: [(1,2), (1,3), (4,10), (5,6)]
Merge:
  Start: [(1,2)]
  Add (1,3): overlaps, merge to (1,3)
  Add (4,10): no overlap, add
  Add (5,6): overlaps with (4,10), stays (4,10)
  Result: [(1,3), (4,10)]

Gaps:
  Between (1,3) and (4,10): [3,4]

Output: [[3,4]]
```

### ⏱️ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Merge | O(n log n) | O(n) | ⭐ Simple |
| Heap | O(n log k) | O(k) | Better for many employees |

---

## Problem 6: The Skyline Problem

**LeetCode 218 - Hard**

### Problem Statement
Given array of buildings `[left, right, height]`, return the skyline formed by these buildings.

```
Input: buildings = [[2,9,10],[3,7,15],[5,12,12],[15,20,10],[19,24,8]]
Output: [[2,10],[3,15],[7,12],[12,0],[15,10],[20,8],[24,0]]
```

### 🎯 Intuition
Use **max heap** with critical points:
- Process all vertical edges (start/end of buildings)
- At each critical point, determine current max height
- Add to result when height changes

**Key Insight:** Track active buildings' heights using max heap.

### 📊 Visual Representation

```
Buildings: [[2,9,10], [3,7,15], [5,12,12]]

Timeline:
    10▓▓▓▓▓▓▓▓
    15   ▓▓▓▓
    12     ▓▓▓▓▓▓▓
    
 x: 2   3  5  7  9  12

Critical points:
  x=2: Building 1 starts, height=10
  x=3: Building 2 starts, height=15 (max)
  x=5: Building 3 starts, height=15 (still max)
  x=7: Building 2 ends, height=12
  x=9: Building 1 ends, height=12
  x=12: Building 3 ends, height=0

Result: [[2,10],[3,15],[7,12],[12,0]]
```

### Solution

```python
import heapq
from collections import defaultdict

def getSkyline(buildings):
    """
    Use max heap to track active building heights.
    
    Logic:
    - Create events for start/end of each building
    - Process events left to right
    - Maintain heap of active heights
    - Record when max height changes
    
    Time: O(n log n)
    Space: O(n)
    """
    # Create events: (x, is_start, height)
    events = []
    for left, right, height in buildings:
        events.append((left, 0, height))   # Start (0 for priority)
        events.append((right, 1, height))  # End (1 for priority)
    
    # Sort events: by x, then start before end, then by height
    events.sort()
    
    result = []
    active = [0]  # Max heap (use negative values)
    
    i = 0
    while i < len(events):
        curr_x = events[i][0]
        
        # Process all events at same x
        while i < len(events) and events[i][0] == curr_x:
            x, is_end, height = events[i]
            
            if is_end == 0:  # Building starts
                heapq.heappush(active, -height)
            else:  # Building ends
                active.remove(-height)
                heapq.heapify(active)
            
            i += 1
        
        # Get current max height
        max_height = -active[0]
        
        # Add to result if height changed
        if not result or result[-1][1] != max_height:
            result.append([curr_x, max_height])
    
    return result
```

### Optimized: Multiset Approach

```python
from sortedcontainers import SortedList

def getSkyline_optimized(buildings):
    """
    Use sorted multiset for O(log n) operations.
    
    Time: O(n log n)
    Space: O(n)
    """
    events = []
    for left, right, height in buildings:
        events.append((left, -height, 0))   # Start (negative height for sort)
        events.append((right, height, 1))   # End
    
    events.sort()
    
    result = []
    heights = SortedList([0])
    
    for x, h, is_end in events:
        if is_end == 0:  # Start
            heights.add(-h)
        else:  # End
            heights.remove(h)
        
        max_height = heights[-1]
        
        if not result or result[-1][1] != max_height:
            result.append([x, max_height])
    
    return result
```

### 🔍 Dry Run

```
buildings = [[2,9,10], [3,7,15]]

Events:
  (2, 0, 10)  - Building 1 starts
  (3, 0, 15)  - Building 2 starts
  (7, 1, 15)  - Building 2 ends
  (9, 1, 10)  - Building 1 ends

Process:
x=2: add height 10, active=[0,-10], max=10
     result=[[2,10]]

x=3: add height 15, active=[0,-10,-15], max=15
     result=[[2,10],[3,15]]

x=7: remove -15, active=[0,-10], max=10
     result=[[2,10],[3,15],[7,10]]

x=9: remove -10, active=[0], max=0
     result=[[2,10],[3,15],[7,10],[9,0]]
```

### ⏱️ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Heap | O(n² log n) | O(n) | Remove is O(n) |
| SortedList | O(n log n) | O(n) | ⭐ Optimal |

---

## Problem 7: Smallest Range Covering K Lists

**LeetCode 632 - Hard**

### Problem Statement
Given `k` sorted lists, find the smallest range that includes at least one number from each list.

```
Input: nums = [[4,10,15,24,26],[0,9,12,20],[5,18,22,30]]
Output: [20,24]
Explanation: Range [20,24] contains 20, 24, 22 from each list
```

### 🎯 Intuition
Use **min heap** with sliding window:
- Heap tracks current element from each list
- Also track current maximum
- Range = [min_heap_top, current_max]
- Advance minimum element, update range

Similar to "Merge K Sorted Lists" but tracking range!

### 📊 Visual Representation

```
Lists:
  L1: 4  10  15  24  26
  L2: 0   9  12  20
  L3: 5  18  22  30

Initial pointers:
  L1[0]=4, L2[0]=0, L3[0]=5
  heap = [0, 4, 5]
  max = 5
  range = [0, 5], size = 5

Advance min (0):
  L1[0]=4, L2[1]=9, L3[0]=5
  heap = [4, 5, 9]
  max = 9
  range = [4, 9], size = 5

Advance min (4):
  L1[1]=10, L2[1]=9, L3[0]=5
  heap = [5, 9, 10]
  max = 10
  range = [5, 10], size = 5

... continue until best range found

Best range: [20, 24]
```

### Solution

```python
import heapq

def smallestRange(nums):
    """
    Use min heap to track current elements from each list.
    
    Logic:
    - Heap contains (value, list_idx, element_idx)
    - Track current maximum
    - Range is [heap_min, current_max]
    - Advance minimum, update max, track best range
    
    Time: O(n log k) where n = total elements, k = lists
    Space: O(k)
    """
    # Initialize heap with first element from each list
    heap = []
    current_max = float('-inf')
    
    for i in range(len(nums)):
        heapq.heappush(heap, (nums[i][0], i, 0))
        current_max = max(current_max, nums[i][0])
    
    best_range = [heap[0][0], current_max]
    
    while True:
        val, list_idx, elem_idx = heapq.heappop(heap)
        
        # Check if we can advance in this list
        if elem_idx + 1 >= len(nums[list_idx]):
            break  # Can't maintain one from each list
        
        # Advance to next element in same list
        next_val = nums[list_idx][elem_idx + 1]
        heapq.heappush(heap, (next_val, list_idx, elem_idx + 1))
        current_max = max(current_max, next_val)
        
        # Update best range
        new_min = heap[0][0]
        if current_max - new_min < best_range[1] - best_range[0]:
            best_range = [new_min, current_max]
    
    return best_range

# Example usage
nums = [[4,10,15,24,26], [0,9,12,20], [5,18,22,30]]
print(smallestRange(nums))  # Output: [20, 24]
```

### Alternative: Pointers Approach

```python
def smallestRange_pointers(nums):
    """
    Use pointers for each list.
    
    Time: O(n log k)
    Space: O(k)
    """
    k = len(nums)
    pointers = [0] * k
    
    def get_range():
        min_val = float('inf')
        max_val = float('-inf')
        min_idx = 0
        
        for i in range(k):
            val = nums[i][pointers[i]]
            if val < min_val:
                min_val = val
                min_idx = i
            max_val = max(max_val, val)
        
        return min_val, max_val, min_idx
    
    best_range = [float('-inf'), float('inf')]
    
    while True:
        min_val, max_val, min_idx = get_range()
        
        if max_val - min_val < best_range[1] - best_range[0]:
            best_range = [min_val, max_val]
        
        # Advance minimum pointer
        pointers[min_idx] += 1
        if pointers[min_idx] >= len(nums[min_idx]):
            break
    
    return best_range
```

### 🔍 Dry Run

```
nums = [[4,10,15], [0,9,12], [5,18,22]]

Initial:
  heap = [(0,1,0), (4,0,0), (5,2,0)]
  max = 5
  range = [0, 5]

Step 1: Pop (0,1,0), add (9,1,1)
  heap = [(4,0,0), (5,2,0), (9,1,1)]
  max = 9
  range = [4, 9] (better? 5 vs 5, no change)

Step 2: Pop (4,0,0), add (10,0,1)
  heap = [(5,2,0), (9,1,1), (10,0,1)]
  max = 10
  range = [5, 10] (worse)

Step 3: Pop (5,2,0), add (18,2,1)
  heap = [(9,1,1), (10,0,1), (18,2,1)]
  max = 18
  range = [9, 18] (worse)

... continue ...

Best range found: [4, 9] or better
```

### ⏱️ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Min Heap | O(n log k) | O(k) | ⭐ Optimal |
| Pointers | O(nk) | O(k) | Simpler but slower |

Where n = total elements, k = number of lists

---

## 🎯 Pattern Summary

### Core Heap Patterns Covered

1. **Merge K Streams**
   - Problems: Merge K Lists, Smallest Range
   - Pattern: Heap with (value, source_id, position)
   - Use: Efficiently merge multiple sorted sources

2. **Two Heaps (Balance)**
   - Problems: Find Median, Sliding Window Median
   - Pattern: Max heap (left) + Min heap (right)
   - Use: Maintain median in stream

3. **Greedy with Heap**
   - Problems: IPO
   - Pattern: Sort by one criteria, heap for another
   - Use: Make optimal choices with constraints

4. **Interval Problems**
   - Problems: Employee Free Time, Skyline
   - Pattern: Events + heap of active intervals
   - Use: Track overlapping intervals

5. **Lazy Deletion**
   - Problems: Sliding Window Median
   - Pattern: Mark for deletion, clean when needed
   - Use: Avoid expensive mid-heap deletions

### Complexity Patterns

- **Time**: Usually O(n log k) or O(n log n)
- **Space**: O(k) or O(n) depending on problem
- **Key**: Heap provides O(log n) insert/extract

### When to Use Heap

✅ **Use Heap When:**
- Need repeated min/max queries
- Merging k sorted streams
- Maintaining dynamic median
- Greedy algorithms with priority
- Finding k largest/smallest elements

❌ **Don't Use Heap When:**
- Need to access middle elements
- Can solve with sorting (one-time)
- Simple two-pointer suffices

### Interview Tips

1. **Two Heaps**: Think of median/balance problems
2. **K-way merge**: Heap is natural choice
3. **Lazy deletion**: Use hash map to delay removal
4. **Events**: Sort critical points, use heap for active state
5. **Space optimization**: Heap beats sorting when k << n

### Common Pitfalls

⚠️ **Watch Out For:**
- Python's heapq is min heap (negate for max heap)
- Heap doesn't support efficient mid-element removal
- Need to track additional state (indices, sources)
- Balance conditions in two-heap problems
- Edge cases: empty heaps, all from one source

---

## 🎓 Key Takeaways

1. **Two heaps** pattern is crucial for median problems
2. **K-way merge** uses heap to track minimum across sources
3. **Lazy deletion** avoids expensive heap operations
4. **Greedy + heap** = powerful combination for optimization
5. **Events + heap** solves complex interval problems

Master these patterns and you'll handle any heap problem in interviews! 🚀

