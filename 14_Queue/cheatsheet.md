# ⏰ Queue - Comprehensive Cheatsheet

## 📚 Core Concept

**Queue**: Linear data structure following **FIFO** (First In First Out) principle.

### When to Use Queue?
✅ **BFS** traversal  
✅ **Level order** processing  
✅ **Task scheduling**  
✅ **Rate limiting**  
✅ Processing in **order of arrival**  

---

## Queue Implementations

### 1. Using List (Not Recommended for Large Data)
```python
class QueueList:
    def __init__(self):
        self.queue = []
    
    def enqueue(self, item):
        self.queue.append(item)  # O(1)
    
    def dequeue(self):
        if self.is_empty():
            raise IndexError("Queue is empty")
        return self.queue.pop(0)  # O(n) - Slow!
    
    def peek(self):
        if self.is_empty():
            raise IndexError("Queue is empty")
        return self.queue[0]
    
    def is_empty(self):
        return len(self.queue) == 0
    
    def size(self):
        return len(self.queue)
```

### 2. Using collections.deque (Recommended)
```python
from collections import deque

class Queue:
    def __init__(self):
        self.queue = deque()
    
    def enqueue(self, item):
        self.queue.append(item)  # O(1)
    
    def dequeue(self):
        if self.is_empty():
            raise IndexError("Queue is empty")
        return self.queue.popleft()  # O(1) - Fast!
    
    def peek(self):
        if self.is_empty():
            raise IndexError("Queue is empty")
        return self.queue[0]
    
    def is_empty(self):
        return len(self.queue) == 0
    
    def size(self):
        return len(self.queue)
```

### 3. Circular Queue (Fixed Size)
```python
class CircularQueue:
    def __init__(self, k):
        self.queue = [None] * k
        self.head = 0
        self.tail = -1
        self.size = 0
        self.capacity = k
    
    def enQueue(self, value):
        if self.isFull():
            return False
        
        self.tail = (self.tail + 1) % self.capacity
        self.queue[self.tail] = value
        self.size += 1
        return True
    
    def deQueue(self):
        if self.isEmpty():
            return False
        
        self.head = (self.head + 1) % self.capacity
        self.size -= 1
        return True
    
    def Front(self):
        return -1 if self.isEmpty() else self.queue[self.head]
    
    def Rear(self):
        return -1 if self.isEmpty() else self.queue[self.tail]
    
    def isEmpty(self):
        return self.size == 0
    
    def isFull(self):
        return self.size == self.capacity
```

### 4. Priority Queue
```python
import heapq

class PriorityQueue:
    def __init__(self):
        self.heap = []
        self.counter = 0  # For FIFO behavior at same priority
    
    def enqueue(self, item, priority):
        # Min heap by default
        heapq.heappush(self.heap, (priority, self.counter, item))
        self.counter += 1
    
    def dequeue(self):
        if self.is_empty():
            raise IndexError("Priority queue is empty")
        return heapq.heappop(self.heap)[2]
    
    def peek(self):
        if self.is_empty():
            raise IndexError("Priority queue is empty")
        return self.heap[0][2]
    
    def is_empty(self):
        return len(self.heap) == 0
```

### 5. Double-Ended Queue (Deque)
```python
from collections import deque

class Deque:
    def __init__(self):
        self.deque = deque()
    
    def add_front(self, item):
        self.deque.appendleft(item)
    
    def add_rear(self, item):
        self.deque.append(item)
    
    def remove_front(self):
        if self.is_empty():
            raise IndexError("Deque is empty")
        return self.deque.popleft()
    
    def remove_rear(self):
        if self.is_empty():
            raise IndexError("Deque is empty")
        return self.deque.pop()
    
    def peek_front(self):
        if self.is_empty():
            raise IndexError("Deque is empty")
        return self.deque[0]
    
    def peek_rear(self):
        if self.is_empty():
            raise IndexError("Deque is empty")
        return self.deque[-1]
    
    def is_empty(self):
        return len(self.deque) == 0
```

---

## Common Patterns

### Pattern 1: BFS / Level Order Traversal

```python
from collections import deque

def bfs_tree(root):
    if not root:
        return []
    
    queue = deque([root])
    result = []
    
    while queue:
        node = queue.popleft()
        result.append(node.val)
        
        if node.left:
            queue.append(node.left)
        if node.right:
            queue.append(node.right)
    
    return result

def levelOrder(root):
    if not root:
        return []
    
    queue = deque([root])
    result = []
    
    while queue:
        level_size = len(queue)
        level = []
        
        for _ in range(level_size):
            node = queue.popleft()
            level.append(node.val)
            
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
        
        result.append(level)
    
    return result
```

### Pattern 2: Sliding Window Maximum

```python
from collections import deque

def maxSlidingWindow(nums, k):
    if not nums:
        return []
    
    dq = deque()  # Store indices
    result = []
    
    for i in range(len(nums)):
        # Remove indices outside current window
        while dq and dq[0] <= i - k:
            dq.popleft()
        
        # Remove elements smaller than current
        while dq and nums[dq[-1]] < nums[i]:
            dq.pop()
        
        dq.append(i)
        
        # Add to result when window is complete
        if i >= k - 1:
            result.append(nums[dq[0]])
    
    return result
```

### Pattern 3: Moving Average

```python
from collections import deque

class MovingAverage:
    def __init__(self, size):
        self.size = size
        self.queue = deque()
        self.window_sum = 0
    
    def next(self, val):
        self.queue.append(val)
        self.window_sum += val
        
        if len(self.queue) > self.size:
            self.window_sum -= self.queue.popleft()
        
        return self.window_sum / len(self.queue)
```

### Pattern 4: Recent Counter

```python
from collections import deque

class RecentCounter:
    def __init__(self):
        self.requests = deque()
    
    def ping(self, t):
        self.requests.append(t)
        
        # Remove requests older than 3000ms
        while self.requests and self.requests[0] < t - 3000:
            self.requests.popleft()
        
        return len(self.requests)
```

### Pattern 5: Task Scheduler

```python
from collections import deque, Counter
import heapq

def leastInterval(tasks, n):
    # Count task frequencies
    count = Counter(tasks)
    
    # Max heap (use negative for max heap)
    max_heap = [-freq for freq in count.values()]
    heapq.heapify(max_heap)
    
    time = 0
    queue = deque()  # (next_available_time, freq)
    
    while max_heap or queue:
        time += 1
        
        if max_heap:
            freq = heapq.heappop(max_heap) + 1
            if freq < 0:
                queue.append((time + n, freq))
        
        if queue and queue[0][0] == time:
            _, freq = queue.popleft()
            heapq.heappush(max_heap, freq)
    
    return time
```

### Pattern 6: Queue Using Stacks

```python
class MyQueue:
    def __init__(self):
        self.stack_in = []
        self.stack_out = []
    
    def push(self, x):
        self.stack_in.append(x)
    
    def pop(self):
        self._move()
        return self.stack_out.pop()
    
    def peek(self):
        self._move()
        return self.stack_out[-1]
    
    def empty(self):
        return not self.stack_in and not self.stack_out
    
    def _move(self):
        if not self.stack_out:
            while self.stack_in:
                self.stack_out.append(self.stack_in.pop())
```

### Pattern 7: Number of Islands (BFS)

```python
from collections import deque

def numIslands(grid):
    if not grid:
        return 0
    
    rows, cols = len(grid), len(grid[0])
    visited = set()
    islands = 0
    
    def bfs(r, c):
        queue = deque([(r, c)])
        visited.add((r, c))
        
        while queue:
            row, col = queue.popleft()
            
            directions = [(1,0), (-1,0), (0,1), (0,-1)]
            for dr, dc in directions:
                nr, nc = row + dr, col + dc
                
                if (0 <= nr < rows and 0 <= nc < cols and
                    grid[nr][nc] == '1' and (nr, nc) not in visited):
                    
                    queue.append((nr, nc))
                    visited.add((nr, nc))
    
    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == '1' and (r, c) not in visited:
                bfs(r, c)
                islands += 1
    
    return islands
```

### Pattern 8: Shortest Path (BFS)

```python
from collections import deque

def shortestPath(grid, start, end):
    if not grid or grid[start[0]][start[1]] == 1:
        return -1
    
    rows, cols = len(grid), len(grid[0])
    queue = deque([(start[0], start[1], 0)])  # (row, col, distance)
    visited = {(start[0], start[1])}
    
    while queue:
        row, col, dist = queue.popleft()
        
        if (row, col) == end:
            return dist
        
        directions = [(1,0), (-1,0), (0,1), (0,-1)]
        for dr, dc in directions:
            nr, nc = row + dr, col + dc
            
            if (0 <= nr < rows and 0 <= nc < cols and
                grid[nr][nc] == 0 and (nr, nc) not in visited):
                
                queue.append((nr, nc, dist + 1))
                visited.add((nr, nc))
    
    return -1
```

### Pattern 9: Rotting Oranges

```python
from collections import deque

def orangesRotting(grid):
    rows, cols = len(grid), len(grid[0])
    queue = deque()
    fresh = 0
    
    # Count fresh oranges and add rotten to queue
    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == 2:
                queue.append((r, c, 0))  # (row, col, time)
            elif grid[r][c] == 1:
                fresh += 1
    
    if fresh == 0:
        return 0
    
    minutes = 0
    
    while queue:
        row, col, time = queue.popleft()
        minutes = max(minutes, time)
        
        directions = [(1,0), (-1,0), (0,1), (0,-1)]
        for dr, dc in directions:
            nr, nc = row + dr, col + dc
            
            if (0 <= nr < rows and 0 <= nc < cols and
                grid[nr][nc] == 1):
                
                grid[nr][nc] = 2
                fresh -= 1
                queue.append((nr, nc, time + 1))
    
    return minutes if fresh == 0 else -1
```

### Pattern 10: LRU Cache (Queue + HashMap)

```python
class Node:
    def __init__(self, key, val):
        self.key = key
        self.val = val
        self.prev = None
        self.next = None

class LRUCache:
    def __init__(self, capacity):
        self.capacity = capacity
        self.cache = {}  # key -> Node
        
        # Dummy head and tail
        self.head = Node(0, 0)
        self.tail = Node(0, 0)
        self.head.next = self.tail
        self.tail.prev = self.head
    
    def get(self, key):
        if key in self.cache:
            node = self.cache[key]
            self._remove(node)
            self._add(node)
            return node.val
        return -1
    
    def put(self, key, value):
        if key in self.cache:
            self._remove(self.cache[key])
        
        node = Node(key, value)
        self._add(node)
        self.cache[key] = node
        
        if len(self.cache) > self.capacity:
            # Remove LRU (node after head)
            lru = self.head.next
            self._remove(lru)
            del self.cache[lru.key]
    
    def _remove(self, node):
        prev, nxt = node.prev, node.next
        prev.next = nxt
        nxt.prev = prev
    
    def _add(self, node):
        # Add to tail (most recently used)
        prev = self.tail.prev
        prev.next = node
        node.prev = prev
        node.next = self.tail
        self.tail.prev = node
```

---

## 🎨 Dry Run Example

### Level Order Traversal

```
Tree:       1
          /   \
         2     3
        / \   /
       4   5 6

Step 1: queue = [1], result = []
        Process 1: result = [[1]]
        Add children: queue = [2, 3]

Step 2: queue = [2, 3], level_size = 2
        Process 2: level = [2], queue = [3, 4, 5]
        Process 3: level = [2, 3], queue = [4, 5, 6]
        result = [[1], [2, 3]]

Step 3: queue = [4, 5, 6], level_size = 3
        Process 4: level = [4], queue = [5, 6]
        Process 5: level = [4, 5], queue = [6]
        Process 6: level = [4, 5, 6], queue = []
        result = [[1], [2, 3], [4, 5, 6]]

Result: [[1], [2, 3], [4, 5, 6]]
```

---

## ⏱️ Complexity Analysis

| Operation | List | Deque | Circular | Priority |
|-----------|------|-------|----------|----------|
| Enqueue | O(1) | O(1) | O(1) | O(log n) |
| Dequeue | O(n) | O(1) | O(1) | O(log n) |
| Peek | O(1) | O(1) | O(1) | O(1) |
| Space | O(n) | O(n) | O(k) | O(n) |

---

## 🎯 Must-Know Problems

### Easy
- [232. Implement Queue using Stacks](https://leetcode.com/problems/implement-queue-using-stacks/)
- [933. Number of Recent Calls](https://leetcode.com/problems/number-of-recent-calls/)
- [346. Moving Average from Data Stream](https://leetcode.com/problems/moving-average-from-data-stream/)

### Medium
- [102. Binary Tree Level Order Traversal](https://leetcode.com/problems/binary-tree-level-order-traversal/)
- [200. Number of Islands](https://leetcode.com/problems/number-of-islands/)
- [994. Rotting Oranges](https://leetcode.com/problems/rotting-oranges/)
- [622. Design Circular Queue](https://leetcode.com/problems/design-circular-queue/)
- [239. Sliding Window Maximum](https://leetcode.com/problems/sliding-window-maximum/)
- [207. Course Schedule](https://leetcode.com/problems/course-schedule/)
- [752. Open the Lock](https://leetcode.com/problems/open-the-lock/)

### Hard
- [146. LRU Cache](https://leetcode.com/problems/lru-cache/)
- [295. Find Median from Data Stream](https://leetcode.com/problems/find-median-from-data-stream/)
- [128. Longest Consecutive Sequence](https://leetcode.com/problems/longest-consecutive-sequence/)

---

## 💡 Pro Tips

1. **Use `collections.deque`**: Much faster than list for queue operations
2. **BFS = Queue**: Always use queue for level-by-level traversal
3. **Track level size**: For level order, process exactly `len(queue)` nodes
4. **Visited set**: Prevent revisiting in graph BFS
5. **Monotonic deque**: For sliding window maximum/minimum
6. **Priority queue**: Use `heapq` for task scheduling

---

## 🔥 Common Mistakes

❌ **Using list.pop(0)** - O(n) time complexity  
✅ **Use deque.popleft()** - O(1) time complexity

❌ **Not tracking level size** in level order  
✅ **Store `level_size = len(queue)` before loop**

❌ **Forgetting visited set** in BFS  
✅ **Always use visited set for graphs**

❌ **Wrong condition** for circular queue full/empty  
✅ **Use size counter or careful pointer math**

---

**Queues are essential for BFS and ordering problems - master them! 🚀**
