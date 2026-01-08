# 🔥 Queue - Hard Problems Collection

This collection contains **7 hard problems** with complete solutions, detailed explanations, and multiple approaches.

---

## Problem 1: Sliding Window Maximum

**LeetCode 239** - Hard

### Problem Statement
Given an array `nums` and a sliding window of size `k`, find the maximum element in each window as it slides from left to right.

**Example:**
```
Input: nums = [1,3,-1,-3,5,3,6,7], k = 3
Output: [3,3,5,5,6,7]

Window position                Max
---------------               -----
[1  3  -1] -3  5  3  6  7       3
 1 [3  -1  -3] 5  3  6  7       3
 1  3 [-1  -3  5] 3  6  7       5
 1  3  -1 [-3  5  3] 6  7       5
 1  3  -1  -3 [5  3  6] 7       6
 1  3  -1  -3  5 [3  6  7]      7
```

### Intuition
We need to track the maximum in each window efficiently. A brute force O(nk) approach won't work for large inputs. We can use a **monotonic decreasing deque** that stores indices of potentially maximum elements.

### Visual Representation
```
Array: [1, 3, -1, -3, 5, 3, 6, 7], k=3

Step 1: [1]
Deque: [0] (index of 1)

Step 2: [1, 3]
Deque: [1] (removed 1, kept 3)

Step 3: [1, 3, -1]
Deque: [1, 2] (3, -1)
Window complete! Max = 3

Step 4: [3, -1, -3]
Remove index 0 (out of window)
Deque: [1, 2, 3] (3, -1, -3)
Max = 3

Step 5: [-1, -3, 5]
Remove smaller elements
Deque: [4] (5)
Max = 5
```

### Approach 1: Monotonic Deque (Optimal)

**Algorithm:**
1. Use deque to store indices in decreasing order of values
2. Remove indices outside current window from front
3. Remove smaller elements from back before adding new element
4. Front of deque always has index of maximum element

### Solution
```python
from collections import deque

def maxSlidingWindow(nums, k):
    """
    Monotonic decreasing deque approach
    Time: O(n), Space: O(k)
    """
    if not nums or k == 0:
        return []
    
    dq = deque()  # Store indices
    result = []
    
    for i in range(len(nums)):
        # Remove indices outside window
        while dq and dq[0] < i - k + 1:
            dq.popleft()
        
        # Remove smaller elements from back
        while dq and nums[dq[-1]] < nums[i]:
            dq.pop()
        
        dq.append(i)
        
        # Add to result when window is complete
        if i >= k - 1:
            result.append(nums[dq[0]])
    
    return result
```

### Dry Run
```
nums = [1,3,-1,-3,5,3,6,7], k = 3

i=0: dq=[0], nums[0]=1
i=1: dq=[1], nums[1]=3 (removed 0)
i=2: dq=[1,2], nums[2]=-1, result=[3]
i=3: dq=[1,2,3], nums[3]=-3, result=[3,3]
i=4: dq=[4], nums[4]=5 (removed 1,2,3), result=[3,3,5]
i=5: dq=[4,5], nums[5]=3, result=[3,3,5,5]
i=6: dq=[6], nums[6]=6 (removed 4,5), result=[3,3,5,5,6]
i=7: dq=[7], nums[7]=7 (removed 6), result=[3,3,5,5,6,7]
```

### Approach 2: Max Heap (Alternative)
```python
import heapq

def maxSlidingWindow_heap(nums, k):
    """
    Max heap approach
    Time: O(n log n), Space: O(n)
    """
    heap = []
    result = []
    
    for i in range(len(nums)):
        heapq.heappush(heap, (-nums[i], i))
        
        # Remove elements outside window
        while heap and heap[0][1] < i - k + 1:
            heapq.heappop(heap)
        
        if i >= k - 1:
            result.append(-heap[0][0])
    
    return result
```

### Complexity Analysis
| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Monotonic Deque | O(n) | O(k) | Optimal, each element added/removed once |
| Max Heap | O(n log n) | O(n) | Good but slower |
| Brute Force | O(nk) | O(1) | Too slow for large inputs |

---

## Problem 2: Design Hit Counter

**LeetCode 362** - Medium/Hard

### Problem Statement
Design a hit counter that counts the number of hits received in the past 5 minutes (300 seconds).

### Solution
```python
from collections import deque

class HitCounter:
    def __init__(self):
        self.hits = deque()
    
    def hit(self, timestamp):
        """Record a hit at given timestamp"""
        self.hits.append(timestamp)
    
    def getHits(self, timestamp):
        """Get number of hits in past 300 seconds"""
        # Remove hits older than 300 seconds
        while self.hits and self.hits[0] <= timestamp - 300:
            self.hits.popleft()
        
        return len(self.hits)

# Optimized with bucketing
class HitCounter_Optimized:
    def __init__(self):
        self.times = [0] * 300
        self.hits = [0] * 300
    
    def hit(self, timestamp):
        idx = timestamp % 300
        if self.times[idx] != timestamp:
            self.times[idx] = timestamp
            self.hits[idx] = 1
        else:
            self.hits[idx] += 1
    
    def getHits(self, timestamp):
        total = 0
        for i in range(300):
            if timestamp - self.times[i] < 300:
                total += self.hits[i]
        return total
```

### Complexity
- **hit()**: O(1)
- **getHits()**: O(1) with bucketing, O(n) with deque

---

## Problem 3: Task Scheduler

**LeetCode 621** - Medium/Hard

### Problem Statement
Given tasks represented by characters and a cooldown period `n`, find the minimum time to complete all tasks. Same task must have at least `n` intervals between executions.

**Example:**
```
Input: tasks = ["A","A","A","B","B","B"], n = 2
Output: 8
Explanation: A -> B -> idle -> A -> B -> idle -> A -> B
```

### Intuition
- Schedule most frequent tasks first
- Use max heap to track task frequencies
- Use queue to track tasks in cooldown

### Solution
```python
from collections import Counter, deque
import heapq

def leastInterval(tasks, n):
    """
    Time: O(N log k) where k is unique tasks
    Space: O(k)
    """
    if n == 0:
        return len(tasks)
    
    # Count frequencies
    freq = Counter(tasks)
    max_heap = [-count for count in freq.values()]
    heapq.heapify(max_heap)
    
    time = 0
    queue = deque()  # (count, available_time)
    
    while max_heap or queue:
        time += 1
        
        if max_heap:
            count = heapq.heappop(max_heap)
            count += 1  # Decrease frequency (negative)
            
            if count < 0:  # Still has remaining tasks
                queue.append((count, time + n))
        
        # Check if any task is available again
        if queue and queue[0][1] == time:
            count, _ = queue.popleft()
            heapq.heappush(max_heap, count)
    
    return time
```

### Mathematical Approach (Optimal)
```python
def leastInterval_math(tasks, n):
    """
    Time: O(N), Space: O(1)
    """
    freq = Counter(tasks)
    max_freq = max(freq.values())
    max_count = sum(1 for f in freq.values() if f == max_freq)
    
    # Minimum time needed
    min_time = (max_freq - 1) * (n + 1) + max_count
    
    return max(min_time, len(tasks))
```

### Dry Run
```
tasks = ["A","A","A","B","B","B"], n = 2

Frequencies: A=3, B=3
max_freq = 3, max_count = 2

min_time = (3-1) * (2+1) + 2 = 2*3 + 2 = 8

Schedule:
A -> B -> idle -> A -> B -> idle -> A -> B
1    2    3       4    5    6       7    8
```

---

## Problem 4: Shortest Subarray with Sum at Least K

**LeetCode 862** - Hard

### Problem Statement
Return the length of the shortest non-empty subarray with sum at least K. If none exists, return -1.

**Example:**
```
Input: nums = [2,-1,2], K = 3
Output: 3
```

### Intuition
Use prefix sum + monotonic deque to find shortest subarray efficiently.

### Solution
```python
from collections import deque

def shortestSubarray(nums, k):
    """
    Monotonic deque + prefix sum
    Time: O(n), Space: O(n)
    """
    n = len(nums)
    prefix = [0] * (n + 1)
    
    # Calculate prefix sums
    for i in range(n):
        prefix[i + 1] = prefix[i] + nums[i]
    
    dq = deque()
    result = n + 1
    
    for i in range(n + 1):
        # Check if we found a valid subarray
        while dq and prefix[i] - prefix[dq[0]] >= k:
            result = min(result, i - dq.popleft())
        
        # Maintain increasing deque
        while dq and prefix[i] <= prefix[dq[-1]]:
            dq.pop()
        
        dq.append(i)
    
    return result if result <= n else -1
```

### Complexity
- **Time**: O(n)
- **Space**: O(n)

---

## Problem 5: Jump Game VI

**LeetCode 1696** - Medium/Hard

### Problem Statement
Given array `nums` and integer `k`, start at index 0 and jump to any index `i + j` where `1 <= j <= k`. Return maximum score (sum of values at visited indices).

**Example:**
```
Input: nums = [1,-1,-2,4,-7,3], k = 2
Output: 7
Explanation: [1, -1, 4, 3] = 7
```

### Solution
```python
from collections import deque

def maxResult(nums, k):
    """
    Monotonic deque + DP
    Time: O(n), Space: O(n)
    """
    n = len(nums)
    dp = [float('-inf')] * n
    dp[0] = nums[0]
    
    dq = deque([0])  # Indices in decreasing order of dp values
    
    for i in range(1, n):
        # Remove indices out of range
        while dq and dq[0] < i - k:
            dq.popleft()
        
        # Current max is from front of deque
        dp[i] = nums[i] + dp[dq[0]]
        
        # Maintain decreasing deque
        while dq and dp[i] >= dp[dq[-1]]:
            dq.pop()
        
        dq.append(i)
    
    return dp[n - 1]
```

### Dry Run
```
nums = [1,-1,-2,4,-7,3], k = 2

i=0: dp[0]=1, dq=[0]
i=1: dp[1]=1+(-1)=0, dq=[0,1]
i=2: dp[2]=max(dp[0],dp[1])+(-2)=1-2=-1, dq=[0,1,2]
i=3: dp[3]=max(dp[1],dp[2])+4=0+4=4, dq=[3]
i=4: dp[4]=max(dp[2],dp[3])+(-7)=4-7=-3, dq=[3,4]
i=5: dp[5]=max(dp[3],dp[4])+3=4+3=7, dq=[5]

Result: 7
```

---

## Problem 6: Constrained Subsequence Sum

**LeetCode 1425** - Hard

### Problem Statement
Return maximum sum of non-empty subsequence with constraint: if you pick element at index `i`, you can't pick element at index `i + k + 1` or later.

**Example:**
```
Input: nums = [10,2,-10,5,20], k = 2
Output: 37
Explanation: [10, 2, 5, 20]
```

### Solution
```python
from collections import deque

def constrainedSubsetSum(nums, k):
    """
    Monotonic deque + DP
    Time: O(n), Space: O(n)
    """
    n = len(nums)
    dp = nums[:]
    dq = deque([0])
    
    for i in range(1, n):
        # Remove indices out of k range
        while dq and dq[0] < i - k:
            dq.popleft()
        
        # Either take current alone or extend from best previous
        dp[i] = max(nums[i], nums[i] + dp[dq[0]])
        
        # Maintain decreasing deque
        while dq and dp[i] >= dp[dq[-1]]:
            dq.pop()
        
        dq.append(i)
    
    return max(dp)
```

### Complexity
- **Time**: O(n)
- **Space**: O(n)

---

## Problem 7: Maximum Number of Robots Within Budget

**LeetCode 2398** - Hard

### Problem Statement
Given `chargeTimes`, `runningCosts`, and `budget`, find maximum number of consecutive robots you can run.

**Cost formula**: `max(chargeTimes) + k * sum(runningCosts)` where k is number of robots.

### Solution
```python
from collections import deque

def maximumRobots(chargeTimes, runningCosts, budget):
    """
    Sliding window + monotonic deque
    Time: O(n), Space: O(n)
    """
    n = len(chargeTimes)
    dq = deque()  # Monotonic decreasing deque for max
    left = 0
    running_sum = 0
    result = 0
    
    for right in range(n):
        # Add current robot
        running_sum += runningCosts[right]
        
        # Maintain monotonic deque
        while dq and chargeTimes[dq[-1]] <= chargeTimes[right]:
            dq.pop()
        dq.append(right)
        
        # Shrink window if over budget
        while dq and left <= right:
            max_charge = chargeTimes[dq[0]]
            k = right - left + 1
            cost = max_charge + k * running_sum
            
            if cost <= budget:
                break
            
            # Remove leftmost robot
            running_sum -= runningCosts[left]
            if dq[0] == left:
                dq.popleft()
            left += 1
        
        result = max(result, right - left + 1)
    
    return result
```

### Complexity
- **Time**: O(n)
- **Space**: O(n)

---

## 🎯 Summary Table

| Problem | Difficulty | Key Technique | Time | Space |
|---------|-----------|---------------|------|-------|
| Sliding Window Maximum | Hard | Monotonic Deque | O(n) | O(k) |
| Design Hit Counter | Medium | Deque/Bucketing | O(1) | O(1) |
| Task Scheduler | Medium | Max Heap + Queue | O(n log k) | O(k) |
| Shortest Subarray Sum K | Hard | Prefix Sum + Deque | O(n) | O(n) |
| Jump Game VI | Medium | DP + Monotonic Deque | O(n) | O(n) |
| Constrained Subset Sum | Hard | DP + Monotonic Deque | O(n) | O(n) |
| Max Robots in Budget | Hard | Sliding Window + Deque | O(n) | O(n) |

---

## 💡 Key Patterns Learned

1. **Monotonic Deque**: Maintain increasing/decreasing order for range queries
2. **Deque + DP**: Optimize DP by tracking best previous states
3. **Sliding Window + Deque**: Handle range maximum/minimum efficiently
4. **Queue for Cooldown**: Track elements that need to wait
5. **Prefix Sum + Deque**: Find subarrays with constraints

---

**Master these patterns and you'll ace any queue-related interview question! 🔥**
