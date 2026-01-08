# 🔥 Greedy - Hard Problems Collection

## Problem 1: [135. Candy](https://leetcode.com/problems/candy/)

### Problem Statement
There are `n` children standing in a line. Each child is assigned a rating. You are giving candies to these children subjected to the following requirements:
- Each child must have at least one candy
- Children with a higher rating get more candies than their neighbors

Return the minimum number of candies you need.

### Intuition
**Key Insight**: We need two passes!
- **Left to right**: Ensure right neighbor with higher rating gets more candy
- **Right to left**: Ensure left neighbor with higher rating gets more candy
- Take maximum of both passes for each position

### Visual Representation
```
Ratings:  [1, 0, 2]

Pass 1 (L→R):
Index:     0  1  2
Ratings:  [1, 0, 2]
Candies:  [1, 1, 2]  (0 has lower rating, gets 1; 2 has higher, gets 2)

Pass 2 (R←L):
Index:     0  1  2
Ratings:  [1, 0, 2]
Candies:  [1, 1, 2]  (no change needed)

But for [1, 2, 2]:
Pass 1: [1, 2, 1]
Pass 2: [1, 2, 1]
Total: 4 candies
```

### Solution
```python
def candy(ratings):
    n = len(ratings)
    if n <= 1:
        return n
    
    candies = [1] * n
    
    # Left to right pass
    for i in range(1, n):
        if ratings[i] > ratings[i-1]:
            candies[i] = candies[i-1] + 1
    
    # Right to left pass
    for i in range(n-2, -1, -1):
        if ratings[i] > ratings[i+1]:
            candies[i] = max(candies[i], candies[i+1] + 1)
    
    return sum(candies)
```

### Dry Run
```
Input: ratings = [1, 2, 87, 87, 87, 2, 1]

Pass 1 (Left to Right):
i=0: candies = [1, 1, 1, 1, 1, 1, 1]
i=1: 2 > 1 → candies = [1, 2, 1, 1, 1, 1, 1]
i=2: 87 > 2 → candies = [1, 2, 3, 1, 1, 1, 1]
i=3: 87 = 87 → candies = [1, 2, 3, 1, 1, 1, 1]
i=4: 87 = 87 → candies = [1, 2, 3, 1, 1, 1, 1]
i=5: 2 < 87 → candies = [1, 2, 3, 1, 1, 1, 1]
i=6: 1 < 2 → candies = [1, 2, 3, 1, 1, 1, 1]

Pass 2 (Right to Left):
i=5: 2 > 1 → candies = [1, 2, 3, 1, 1, 2, 1]
i=4: 87 > 2 → candies = [1, 2, 3, 1, 1, 2, 1] → max(1, 3) = 3
      But we need max(1, 2+1) = 3 → [1, 2, 3, 1, 3, 2, 1]
i=3: 87 = 87 → candies = [1, 2, 3, 1, 3, 2, 1]
i=2: 87 > 87 → No change
i=1: 2 < 87 → candies = [1, 2, 3, 1, 3, 2, 1]
i=0: 1 < 2 → candies = [1, 2, 3, 1, 3, 2, 1]

Total: 1+2+3+1+3+2+1 = 13
```

**Time**: O(n), **Space**: O(n)

---

## Problem 2: [45. Jump Game II](https://leetcode.com/problems/jump-game-ii/)

### Problem Statement
Given an array where each element is your maximum jump length at that position. Return the minimum number of jumps to reach the last index.

### Intuition
**Greedy BFS approach**: Think of it as levels
- Track the farthest we can reach in current jump
- When we exhaust current range, we must make another jump
- Always update the farthest position we can reach

### Visual Representation
```
Input: [2, 3, 1, 1, 4]
Index:  0  1  2  3  4

Jump 0: At index 0, can reach [1,2]
        farthest = 2, current_end = 0
        
Jump 1: At index 1, can reach [2,3,4]
        farthest = 4, current_end = 2
        Now at index 2, we've exhausted range [0,2]
        Make jump, jumps = 1

Jump 2: At index 3, within range [3,4]
        Already at end!
        jumps = 2

Visualization:
[2, 3, 1, 1, 4]
 └──┬──┘        Jump 1: reaches index 2
    └────────┬─ Jump 2: reaches index 4
```

### Solution
```python
def jump(nums):
    if len(nums) <= 1:
        return 0
    
    jumps = 0
    current_end = 0
    farthest = 0
    
    for i in range(len(nums) - 1):
        # Update farthest position reachable
        farthest = max(farthest, i + nums[i])
        
        # If we've reached end of current jump range
        if i == current_end:
            jumps += 1
            current_end = farthest
            
            # Early exit if we can reach the end
            if current_end >= len(nums) - 1:
                break
    
    return jumps
```

**Time**: O(n), **Space**: O(1)

---

## Problem 3: [765. Couples Holding Hands](https://leetcode.com/problems/couples-holding-hands/)

### Problem Statement
N couples sit in 2N seats arranged in a row. Find minimum swaps so that every couple is sitting side by side.

### Intuition
**Union-Find approach**: Each swap can fix one couple
- Group seats by couples (even indices)
- Use Union-Find to count connected components
- Swaps needed = N - number_of_components

### Solution
```python
def minSwapsCouples(row):
    n = len(row) // 2
    parent = list(range(n))
    
    def find(x):
        if parent[x] != x:
            parent[x] = find(parent[x])
        return parent[x]
    
    def union(x, y):
        px, py = find(x), find(y)
        if px != py:
            parent[px] = py
            return True
        return False
    
    # Union couples
    swaps = 0
    for i in range(0, len(row), 2):
        couple1 = row[i] // 2
        couple2 = row[i+1] // 2
        
        if union(couple1, couple2):
            swaps += 1
    
    return swaps
```

**Time**: O(n), **Space**: O(n)

---

## Problem 4: [502. IPO](https://leetcode.com/problems/ipo/)

### Problem Statement
Given `k` projects to invest in, each with capital requirement and profit. Starting with `w` capital, maximize final capital after at most `k` projects.

### Intuition
**Two heaps approach**:
1. Min heap for projects by capital (available projects)
2. Max heap for profits (choose most profitable)
3. At each step, move affordable projects to max heap, pick best

### Solution
```python
import heapq

def findMaximizedCapital(k, w, profits, capital):
    n = len(profits)
    # Min heap of (capital, profit)
    projects = sorted(zip(capital, profits))
    
    # Max heap for profits (negate for max heap)
    available = []
    
    i = 0
    for _ in range(k):
        # Add all affordable projects
        while i < n and projects[i][0] <= w:
            heapq.heappush(available, -projects[i][1])
            i += 1
        
        # If no projects available, break
        if not available:
            break
        
        # Pick most profitable
        w += -heapq.heappop(available)
    
    return w
```

**Time**: O(n log n + k log n), **Space**: O(n)

---

## Problem 5: [1235. Maximum Profit in Job Scheduling](https://leetcode.com/problems/maximum-profit-in-job-scheduling/)

### Problem Statement
Given `startTime`, `endTime`, and `profit` arrays for jobs. Find maximum profit you can make (jobs can't overlap).

### Intuition
**DP + Binary Search**:
1. Sort jobs by end time
2. For each job, decide: take it or skip it
3. If taking, binary search for last non-overlapping job
4. Use DP to memoize

### Solution
```python
def jobScheduling(startTime, endTime, profit):
    jobs = sorted(zip(endTime, startTime, profit))
    n = len(jobs)
    
    # dp[i] = max profit using jobs 0..i
    dp = [0] * n
    dp[0] = jobs[0][2]
    
    def binary_search(index):
        """Find last job that doesn't overlap with jobs[index]"""
        left, right = 0, index - 1
        result = -1
        
        while left <= right:
            mid = (left + right) // 2
            if jobs[mid][0] <= jobs[index][1]:
                result = mid
                left = mid + 1
            else:
                right = mid - 1
        
        return result
    
    for i in range(1, n):
        # Option 1: Don't take current job
        profit_without = dp[i-1]
        
        # Option 2: Take current job
        prev = binary_search(i)
        profit_with = jobs[i][2]
        if prev != -1:
            profit_with += dp[prev]
        
        dp[i] = max(profit_without, profit_with)
    
    return dp[n-1]
```

**Time**: O(n log n), **Space**: O(n)

---

## Problem 6: [630. Course Schedule III](https://leetcode.com/problems/course-schedule-iii/)

### Problem Statement
Given courses with `[duration, lastDay]`, maximize number of courses you can take.

### Intuition
**Greedy + Max Heap**:
1. Sort courses by last day
2. Try to take each course
3. If time exceeds, remove longest duration course
4. This ensures maximum courses

### Solution
```python
import heapq

def scheduleCourse(courses):
    # Sort by last day
    courses.sort(key=lambda x: x[1])
    
    max_heap = []
    time = 0
    
    for duration, lastDay in courses:
        # Try to take the course
        time += duration
        heapq.heappush(max_heap, -duration)
        
        # If exceeds deadline, remove longest course
        if time > lastDay:
            time += heapq.heappop(max_heap)
    
    return len(max_heap)
```

**Time**: O(n log n), **Space**: O(n)

---

## Problem 7: [1505. Minimum Possible Integer After at Most K Adjacent Swaps On Digits](https://leetcode.com/problems/minimum-possible-integer-after-at-most-k-adjacent-swaps-on-digits/)

### Problem Statement
Given a string `num` and integer `k`, return minimum possible integer after at most `k` adjacent swaps.

### Intuition
**Greedy + Fenwick Tree**:
1. For each position, find smallest digit within reach (k swaps)
2. Use Fenwick tree to track positions
3. Move that digit to current position

### Solution
```python
def minInteger(num, k):
    if k == 0:
        return num
    
    n = len(num)
    digits = list(num)
    
    # For each position, find smallest digit in range
    for i in range(n):
        # Find smallest digit in next k positions
        min_digit = digits[i]
        min_pos = i
        
        for j in range(i + 1, min(i + k + 1, n)):
            if digits[j] < min_digit:
                min_digit = digits[j]
                min_pos = j
        
        # Move that digit to position i
        while min_pos > i:
            digits[min_pos], digits[min_pos-1] = digits[min_pos-1], digits[min_pos]
            min_pos -= 1
            k -= 1
        
        if k == 0:
            break
    
    return ''.join(digits)
```

**Time**: O(n²), **Space**: O(n)

---

## Summary Table

| Problem | Pattern | Key Technique | Difficulty |
|---------|---------|---------------|------------|
| Candy | Two Pass | Greedy from both sides | Hard |
| Jump Game II | BFS Levels | Track ranges | Medium-Hard |
| Couples Holding Hands | Union-Find | Component counting | Hard |
| IPO | Two Heaps | Greedy selection | Hard |
| Job Scheduling | DP + Binary Search | Optimal substructure | Hard |
| Course Schedule III | Heap | Remove longest | Hard |

**Master these patterns to ace greedy problems! 🎯**
