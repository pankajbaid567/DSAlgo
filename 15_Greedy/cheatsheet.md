# 🎯 Greedy Algorithms - Comprehensive Cheatsheet

## 📚 Core Concept

**Greedy Algorithm**: Make locally optimal choices at each step, hoping to find global optimum.

### When to Use Greedy?
✅ Problem has **optimal substructure**  
✅ **Greedy choice property**: Local optimum leads to global optimum  
✅ Keywords: "maximum", "minimum", "optimize"  

### Greedy vs DP
- **Greedy**: Makes choice, never reconsiders
- **DP**: Considers all options, builds solution

---

## Common Patterns

### Pattern 1: Activity Selection / Interval Scheduling

```python
# Maximum non-overlapping intervals
def maxIntervals(intervals):
    if not intervals:
        return 0
    
    # Sort by end time (KEY INSIGHT!)
    intervals.sort(key=lambda x: x[1])
    
    count = 1
    last_end = intervals[0][1]
    
    for i in range(1, len(intervals)):
        if intervals[i][0] >= last_end:
            count += 1
            last_end = intervals[i][1]
    
    return count

# Minimum meeting rooms
def minMeetingRooms(intervals):
    if not intervals:
        return 0
    
    start_times = sorted([i[0] for i in intervals])
    end_times = sorted([i[1] for i in intervals])
    
    rooms = 0
    end_ptr = 0
    
    for start in start_times:
        if start < end_times[end_ptr]:
            rooms += 1
        else:
            end_ptr += 1
    
    return rooms

# Non-overlapping intervals (minimum removals)
def eraseOverlapIntervals(intervals):
    if not intervals:
        return 0
    
    intervals.sort(key=lambda x: x[1])
    count = 0
    last_end = intervals[0][1]
    
    for i in range(1, len(intervals)):
        if intervals[i][0] < last_end:
            count += 1  # Remove this interval
        else:
            last_end = intervals[i][1]
    
    return count
```

**Key Insight**: Sort by end time for interval problems!

---

### Pattern 2: Fractional Knapsack

```python
def fractionalKnapsack(items, capacity):
    # items = [(value, weight), ...]
    
    # Sort by value/weight ratio (descending)
    items.sort(key=lambda x: x[0]/x[1], reverse=True)
    
    total_value = 0
    remaining = capacity
    
    for value, weight in items:
        if remaining >= weight:
            total_value += value
            remaining -= weight
        else:
            # Take fraction
            total_value += value * (remaining / weight)
            break
    
    return total_value
```

---

### Pattern 3: Huffman Coding

```python
import heapq
from collections import Counter

class Node:
    def __init__(self, char, freq):
        self.char = char
        self.freq = freq
        self.left = None
        self.right = None
    
    def __lt__(self, other):
        return self.freq < other.freq

def huffmanCoding(s):
    if not s:
        return {}
    
    # Count frequencies
    freq = Counter(s)
    
    # Build min heap
    heap = [Node(char, f) for char, f in freq.items()]
    heapq.heapify(heap)
    
    # Build Huffman tree
    while len(heap) > 1:
        left = heapq.heappop(heap)
        right = heapq.heappop(heap)
        
        merged = Node(None, left.freq + right.freq)
        merged.left = left
        merged.right = right
        
        heapq.heappush(heap, merged)
    
    # Generate codes
    codes = {}
    
    def generate_codes(node, code):
        if node.char:
            codes[node.char] = code
            return
        
        if node.left:
            generate_codes(node.left, code + '0')
        if node.right:
            generate_codes(node.right, code + '1')
    
    generate_codes(heap[0], '')
    return codes
```

---

### Pattern 4: Jump Game

```python
# Can jump to last index
def canJump(nums):
    max_reach = 0
    
    for i in range(len(nums)):
        if i > max_reach:
            return False
        max_reach = max(max_reach, i + nums[i])
    
    return True

# Minimum jumps
def minJumps(nums):
    if len(nums) <= 1:
        return 0
    
    jumps = 0
    current_end = 0
    farthest = 0
    
    for i in range(len(nums) - 1):
        farthest = max(farthest, i + nums[i])
        
        if i == current_end:
            jumps += 1
            current_end = farthest
            
            if current_end >= len(nums) - 1:
                break
    
    return jumps
```

---

### Pattern 5: Two City Scheduling

```python
def twoCitySchedCost(costs):
    # costs[i] = [costA, costB]
    
    # Sort by difference (costA - costB)
    costs.sort(key=lambda x: x[0] - x[1])
    
    n = len(costs) // 2
    total = 0
    
    # First n go to city A (smaller costA - costB)
    for i in range(n):
        total += costs[i][0]
    
    # Next n go to city B
    for i in range(n, 2 * n):
        total += costs[i][1]
    
    return total
```

---

### Pattern 6: Candy Distribution

```python
def candy(ratings):
    n = len(ratings)
    candies = [1] * n
    
    # Left to right: if rating higher, give more candy
    for i in range(1, n):
        if ratings[i] > ratings[i-1]:
            candies[i] = candies[i-1] + 1
    
    # Right to left: if rating higher, give more candy
    for i in range(n-2, -1, -1):
        if ratings[i] > ratings[i+1]:
            candies[i] = max(candies[i], candies[i+1] + 1)
    
    return sum(candies)
```

---

### Pattern 7: Gas Station

```python
def canCompleteCircuit(gas, cost):
    if sum(gas) < sum(cost):
        return -1
    
    total = 0
    start = 0
    
    for i in range(len(gas)):
        total += gas[i] - cost[i]
        
        if total < 0:
            total = 0
            start = i + 1
    
    return start
```

---

### Pattern 8: Boats to Save People

```python
def numRescueBoats(people, limit):
    people.sort()
    left, right = 0, len(people) - 1
    boats = 0
    
    while left <= right:
        if people[left] + people[right] <= limit:
            left += 1
        right -= 1
        boats += 1
    
    return boats
```

---

### Pattern 9: Partition Labels

```python
def partitionLabels(s):
    last_occurrence = {char: i for i, char in enumerate(s)}
    
    result = []
    start = 0
    end = 0
    
    for i, char in enumerate(s):
        end = max(end, last_occurrence[char])
        
        if i == end:
            result.append(end - start + 1)
            start = i + 1
    
    return result
```

---

### Pattern 10: Queue Reconstruction

```python
def reconstructQueue(people):
    # people[i] = [height, k] where k = number of people in front >= height
    
    # Sort by height (desc), then by k (asc)
    people.sort(key=lambda x: (-x[0], x[1]))
    
    result = []
    for person in people:
        result.insert(person[1], person)
    
    return result
```

---

## 🎨 Dry Run Example

### Activity Selection

```
Intervals: [[1,3], [2,4], [3,5], [0,6], [5,7], [8,9], [5,9]]

Step 1: Sort by end time
        [[1,3], [2,4], [3,5], [0,6], [5,7], [8,9], [5,9]]
        ↓
        [[1,3], [2,4], [3,5], [0,6], [5,7], [8,9], [5,9]]

Step 2: Select greedily
        [1,3] ✓ (count=1, last_end=3)
        [2,4]   (2 < 3, overlaps, skip)
        [3,5] ✓ (3 >= 3, count=2, last_end=5)
        [0,6]   (0 < 5, overlaps, skip)
        [5,7] ✓ (5 >= 5, count=3, last_end=7)
        [8,9] ✓ (8 >= 7, count=4, last_end=9)
        [5,9]   (5 < 9, overlaps, skip)

Result: 4 intervals maximum
```

---

## ⏱️ Complexity Analysis

| Pattern | Time | Space |
|---------|------|-------|
| Activity Selection | O(n log n) | O(1) |
| Fractional Knapsack | O(n log n) | O(1) |
| Huffman Coding | O(n log n) | O(n) |
| Jump Game | O(n) | O(1) |
| Candy Distribution | O(n) | O(n) |
| Gas Station | O(n) | O(1) |
| Two Pointers | O(n log n) | O(1) |

---

## 🎯 Must-Know Problems

### Easy
- [455. Assign Cookies](https://leetcode.com/problems/assign-cookies/)
- [860. Lemonade Change](https://leetcode.com/problems/lemonade-change/)

### Medium
- [55. Jump Game](https://leetcode.com/problems/jump-game/)
- [45. Jump Game II](https://leetcode.com/problems/jump-game-ii/)
- [134. Gas Station](https://leetcode.com/problems/gas-station/)
- [435. Non-overlapping Intervals](https://leetcode.com/problems/non-overlapping-intervals/)
- [452. Minimum Number of Arrows to Burst Balloons](https://leetcode.com/problems/minimum-number-of-arrows-to-burst-balloons/)
- [763. Partition Labels](https://leetcode.com/problems/partition-labels/)
- [406. Queue Reconstruction by Height](https://leetcode.com/problems/queue-reconstruction-by-height/)

### Hard
- [135. Candy](https://leetcode.com/problems/candy/)
- [1585. Check If String Is Transformable](https://leetcode.com/problems/check-if-string-is-transformable-with-substring-sort-operations/)

---

## 🎓 Key Principles

1. **Sorting is often the first step**
2. **Consider what to sort by** (end time? ratio? difference?)
3. **Greedy choice must be irrevocable**
4. **Prove correctness** (exchange argument)
5. **Two passes sometimes needed** (like Candy problem)

### How to Recognize Greedy Problems?

🔍 **Look for:**
- Optimization problems (max/min)
- "At each step" phrasing
- Interval/scheduling problems
- Resource allocation
- Can make local choice without looking ahead

❌ **Not Greedy if:**
- Need to consider all possibilities
- Future choices depend on past choices
- Counterexample exists for greedy approach

---

## 💡 Pro Tips

1. **Sort first**: Most greedy problems need sorting
2. **Identify greedy criterion**: What makes choice optimal?
3. **Prove with exchange argument**: Swapping doesn't improve
4. **Watch for edge cases**: Empty input, single element
5. **Sometimes need multiple passes**: Like candy distribution

---

## 📊 Common Sorting Criteria

| Problem Type | Sort By |
|-------------|---------|
| Interval Scheduling | End time (ascending) |
| Fractional Knapsack | Value/Weight ratio (descending) |
| Two City | Cost difference |
| Boats | Weight (ascending) |
| Queue Reconstruction | Height (desc), then k (asc) |

---

**Greedy algorithms are powerful when they work - master the patterns! 🎯**
