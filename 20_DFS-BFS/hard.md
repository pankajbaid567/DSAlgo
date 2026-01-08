# 🔥 DFS-BFS - Hard Problems Collection

A comprehensive collection of challenging DFS-BFS problems with advanced search techniques, bidirectional search, A*, and detailed explanations.

---

## 📚 Table of Contents

1. [Shortest Path to Get All Keys](#problem-1-shortest-path-to-get-all-keys)
2. [Sliding Puzzle](#problem-2-sliding-puzzle)
3. [Minimum Knight Moves](#problem-3-minimum-knight-moves)
4. [Shortest Path with Alternating Colors](#problem-4-shortest-path-with-alternating-colors)
5. [Reachable Nodes in Subdivided Graph](#problem-5-reachable-nodes-in-subdivided-graph)
6. [Cut Off Trees for Golf Event](#problem-6-cut-off-trees-for-golf-event)
7. [Parallel Courses III](#problem-7-parallel-courses-iii)
8. [Pattern Summary](#-pattern-summary)

---

## Problem 1: Shortest Path to Get All Keys

**LeetCode 864 - Hard**

### Problem Statement
Find shortest path to collect all keys in a grid with locks and keys.

```
Input: grid = ["@.a..","###.#","b.A.B"]
Output: 8
Explanation: Collect all keys starting from @
```

### 🎯 Intuition
**BFS with state tracking:**
- State: (row, col, keys_collected)
- Keys collected as bitmask
- Can pass through lock only if have key
- Target: reach state with all keys

**Key insight:** State includes position + keys, not just position.

### 📊 Visual Representation

```
grid = ["@.a..",
        "###.#",
        "b.A.B"]

@ = start
a, b = keys (lowercase)
A, B = locks (uppercase)
# = wall

State representation:
  (row, col, keys_bitmask)
  
  keys_bitmask:
    bit 0: key 'a'
    bit 1: key 'b'
    
Example states:
  (@, 0, 0b00) → start, no keys
  (a, 0, 0b01) → at 'a', collected key 'a'
  (b, 0, 0b11) → at 'b', collected both keys
```

### Solution

```python
from collections import deque

def shortestPathAllKeys(grid):
    """
    BFS with state = (position, keys collected).
    
    Logic:
    - State: (row, col, keys_mask)
    - BFS to find shortest path to all keys
    - Can pass lock if have corresponding key
    
    Time: O(m*n*2^k) where k = number of keys
    Space: O(m*n*2^k)
    """
    m, n = len(grid), len(grid[0])
    
    # Find start position and count keys
    start_r = start_c = 0
    key_count = 0
    
    for i in range(m):
        for j in range(n):
            if grid[i][j] == '@':
                start_r, start_c = i, j
            elif grid[i][j].islower():
                key_count += 1
    
    # BFS
    target_mask = (1 << key_count) - 1
    queue = deque([(start_r, start_c, 0, 0)])  # (row, col, keys, steps)
    visited = {(start_r, start_c, 0)}
    
    directions = [(0,1), (1,0), (0,-1), (-1,0)]
    
    while queue:
        r, c, keys, steps = queue.popleft()
        
        # Found all keys
        if keys == target_mask:
            return steps
        
        for dr, dc in directions:
            nr, nc = r + dr, c + dc
            
            if 0 <= nr < m and 0 <= nc < n:
                cell = grid[nr][nc]
                
                # Wall
                if cell == '#':
                    continue
                
                new_keys = keys
                
                # Key
                if cell.islower():
                    key_bit = ord(cell) - ord('a')
                    new_keys = keys | (1 << key_bit)
                
                # Lock
                elif cell.isupper():
                    key_bit = ord(cell) - ord('A')
                    if not (keys & (1 << key_bit)):
                        continue  # Don't have key
                
                # Check if visited this state
                if (nr, nc, new_keys) not in visited:
                    visited.add((nr, nc, new_keys))
                    queue.append((nr, nc, new_keys, steps + 1))
    
    return -1

# Example usage
grid = ["@.a..","###.#","b.A.B"]
print(shortestPathAllKeys(grid))  # Output: 8
```

### 🔍 Dry Run

```
grid = ["@.a..",
        "###.#",
        "b.A.B"]

BFS:
  Start: (0,0, 0b00, 0) → '@', no keys
  
  Step 1: Try all 4 directions
    Right: (0,1, 0b00, 1) → '.', valid
  
  From (0,1):
    Right: (0,2, 0b01, 2) → 'a', collect key!
  
  From (0,2):
    Can't go down through wall
    Right: (0,3, 0b01, 3)
    Continue...
  
  Eventually reach (2,0) with key 'a':
    (2,0, 0b01, ?) → 'b', collect key!
    (2,0, 0b11, ?)
  
  With both keys, can pass lock 'A':
    Reach 'B' lock, have both keys
    
  Total steps: 8
```

### ⏱️ Complexity Analysis

| Metric | Complexity | Notes |
|--------|------------|-------|
| Time | O(m*n*2^k) | k = keys (≤6 typically) |
| Space | O(m*n*2^k) | Visited states |

---

## Problem 2: Sliding Puzzle

**LeetCode 773 - Hard**

### Problem Statement
Solve sliding puzzle with minimum moves.

```
Input: board = [[1,2,3],[4,0,5]]
Output: 1
Explanation: Swap 0 and 5 in one move
```

### 🎯 Intuition
**BFS on state space:**
- State: board configuration as tuple
- Transition: swap 0 with adjacent tile
- Target: [[1,2,3],[4,5,0]]

### Solution

```python
from collections import deque

def slidingPuzzle(board):
    """
    BFS on puzzle states.
    
    Logic:
    - State: board configuration
    - Generate next states by swapping 0
    - Use BFS for shortest path
    
    Time: O((m*n)! * m*n) worst case
    Space: O((m*n)!)
    """
    m, n = 2, 3
    target = "123450"
    
    # Convert board to string
    start = ''.join(str(board[i][j]) for i in range(m) for j in range(n))
    
    if start == target:
        return 0
    
    # Neighbors for each position (hardcoded for 2x3)
    neighbors = {
        0: [1, 3],
        1: [0, 2, 4],
        2: [1, 5],
        3: [0, 4],
        4: [1, 3, 5],
        5: [2, 4]
    }
    
    queue = deque([(start, 0)])
    visited = {start}
    
    while queue:
        state, moves = queue.popleft()
        
        # Find position of 0
        zero_pos = state.index('0')
        
        # Try swapping with neighbors
        for neighbor in neighbors[zero_pos]:
            # Swap
            state_list = list(state)
            state_list[zero_pos], state_list[neighbor] = state_list[neighbor], state_list[zero_pos]
            new_state = ''.join(state_list)
            
            if new_state == target:
                return moves + 1
            
            if new_state not in visited:
                visited.add(new_state)
                queue.append((new_state, moves + 1))
    
    return -1

# Example usage
board = [[1,2,3],[4,0,5]]
print(slidingPuzzle(board))  # Output: 1
```

### Bidirectional BFS Optimization

```python
def slidingPuzzle_bidirectional(board):
    """
    Bidirectional BFS for faster search.
    
    Time: O(√((m*n)!))
    Space: O((m*n)!)
    """
    m, n = 2, 3
    start = ''.join(str(board[i][j]) for i in range(m) for j in range(n))
    target = "123450"
    
    if start == target:
        return 0
    
    neighbors = {
        0: [1, 3], 1: [0, 2, 4], 2: [1, 5],
        3: [0, 4], 4: [1, 3, 5], 5: [2, 4]
    }
    
    # Two BFS frontiers
    front_start = {start}
    front_target = {target}
    visited_start = {start: 0}
    visited_target = {target: 0}
    
    moves = 0
    
    while front_start and front_target:
        # Always expand smaller frontier
        if len(front_start) > len(front_target):
            front_start, front_target = front_target, front_start
            visited_start, visited_target = visited_target, visited_start
        
        moves += 1
        next_front = set()
        
        for state in front_start:
            zero_pos = state.index('0')
            
            for neighbor in neighbors[zero_pos]:
                state_list = list(state)
                state_list[zero_pos], state_list[neighbor] = state_list[neighbor], state_list[zero_pos]
                new_state = ''.join(state_list)
                
                # Found in other frontier
                if new_state in visited_target:
                    return moves + visited_target[new_state]
                
                if new_state not in visited_start:
                    visited_start[new_state] = moves
                    next_front.add(new_state)
        
        front_start = next_front
    
    return -1
```

### ⏱️ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| BFS | O((m*n)!) | O((m*n)!) | Standard |
| Bidirectional | O(√((m*n)!)) | O((m*n)!) | ⭐ Faster |

---

## Problem 3: Minimum Knight Moves

**LeetCode 1197 - Medium/Hard**

### Problem Statement
Find minimum knight moves from (0,0) to (x,y).

```
Input: x = 2, y = 1
Output: 1
Explanation: [0,0] → [2,1]
```

### Solution 1: BFS with Symmetry

```python
from collections import deque

def minKnightMoves(x, y):
    """
    BFS with symmetry optimization.
    
    Logic:
    - Use 4-way symmetry (only consider first quadrant)
    - BFS from origin
    - Expand search area slightly beyond target
    
    Time: O(|x|*|y|)
    Space: O(|x|*|y|)
    """
    x, y = abs(x), abs(y)
    
    if x == 0 and y == 0:
        return 0
    
    moves = [
        (2,1), (1,2), (-1,2), (-2,1),
        (-2,-1), (-1,-2), (1,-2), (2,-1)
    ]
    
    queue = deque([(0, 0, 0)])
    visited = {(0, 0)}
    
    while queue:
        cx, cy, steps = queue.popleft()
        
        for dx, dy in moves:
            nx, ny = cx + dx, cy + dy
            
            if (nx, ny) == (x, y):
                return steps + 1
            
            # Allow exploring slightly beyond target
            if (nx, ny) not in visited and -2 <= nx <= x + 2 and -2 <= ny <= y + 2:
                visited.add((nx, ny))
                queue.append((nx, ny, steps + 1))
    
    return -1

# Example usage
print(minKnightMoves(2, 1))  # Output: 1
print(minKnightMoves(5, 5))  # Output: 4
```

### Solution 2: Bidirectional BFS

```python
def minKnightMoves_bidirectional(x, y):
    """
    Bidirectional BFS for faster search.
    
    Time: O(√(|x|*|y|))
    Space: O(|x|*|y|)
    """
    if x == 0 and y == 0:
        return 0
    
    x, y = abs(x), abs(y)
    
    moves = [
        (2,1), (1,2), (-1,2), (-2,1),
        (-2,-1), (-1,-2), (1,-2), (2,-1)
    ]
    
    front1 = {(0, 0)}
    front2 = {(x, y)}
    visited1 = {(0, 0): 0}
    visited2 = {(x, y): 0}
    
    steps = 0
    
    while front1:
        steps += 1
        next_front = set()
        
        for cx, cy in front1:
            for dx, dy in moves:
                nx, ny = cx + dx, cy + dy
                
                if (nx, ny) in visited2:
                    return steps + visited2[(nx, ny)]
                
                if (nx, ny) not in visited1 and -2 <= nx <= x + 2 and -2 <= ny <= y + 2:
                    visited1[(nx, ny)] = steps
                    next_front.add((nx, ny))
        
        front1, front2 = front2, next_front
        visited1, visited2 = visited2, visited1
    
    return -1
```

### ⏱️ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| BFS | O(\|x\|*\|y\|) | O(\|x\|*\|y\|) | Simple |
| Bidirectional | O(√(\|x\|*\|y\|)) | O(\|x\|*\|y\|) | ⭐ Optimal |

---

## Problem 4: Shortest Path with Alternating Colors

**LeetCode 1129 - Medium/Hard**

### Problem Statement
Find shortest path with alternating red/blue edges.

```
Input: n = 3, redEdges = [[0,1],[1,2]], blueEdges = []
Output: [0,1,-1]
```

### Solution

```python
from collections import deque, defaultdict

def shortestAlternatingPaths(n, redEdges, blueEdges):
    """
    BFS with state = (node, last_color).
    
    Logic:
    - State includes last edge color
    - Can only use opposite color next
    - Track shortest path for each state
    
    Time: O(n + E)
    Space: O(n + E)
    """
    # Build adjacency lists
    red_graph = defaultdict(list)
    blue_graph = defaultdict(list)
    
    for u, v in redEdges:
        red_graph[u].append(v)
    
    for u, v in blueEdges:
        blue_graph[u].append(v)
    
    # BFS: state = (node, color) where color = 0 (red) or 1 (blue)
    # Start with both colors from node 0
    queue = deque([(0, 0, 0), (0, 1, 0)])  # (node, last_color, dist)
    visited = {(0, 0), (0, 1)}
    answer = [-1] * n
    answer[0] = 0
    
    while queue:
        node, last_color, dist = queue.popleft()
        
        # Choose graph based on alternating color
        if last_color == 0:  # Last was red, use blue
            graph = blue_graph
            next_color = 1
        else:  # Last was blue, use red
            graph = red_graph
            next_color = 0
        
        for neighbor in graph[node]:
            if (neighbor, next_color) not in visited:
                visited.add((neighbor, next_color))
                queue.append((neighbor, next_color, dist + 1))
                
                # Update answer if not set or found shorter
                if answer[neighbor] == -1:
                    answer[neighbor] = dist + 1
    
    return answer

# Example usage
print(shortestAlternatingPaths(3, [[0,1],[1,2]], []))
# Output: [0, 1, -1]
```

### ⏱️ Complexity Analysis

| Metric | Complexity | Notes |
|--------|------------|-------|
| Time | O(n + E) | E = edges |
| Space | O(n + E) | Adjacency lists |

---

## Problem 5: Reachable Nodes in Subdivided Graph

**LeetCode 882 - Hard**

### Problem Statement
Find reachable nodes in graph where edges are subdivided, with max moves limit.

```
Input: edges = [[0,1,10],[0,2,1],[1,2,2]], maxMoves = 6, n = 3
Output: 13
```

### Solution

```python
import heapq
from collections import defaultdict

def reachableNodes(edges, maxMoves, n):
    """
    Dijkstra with node counting.
    
    Logic:
    - Use Dijkstra to find shortest paths
    - Track how many subdivided nodes visited on each edge
    - Count original + subdivided nodes
    
    Time: O(E log V)
    Space: O(V + E)
    """
    graph = defaultdict(list)
    
    for u, v, cnt in edges:
        graph[u].append((v, cnt))
        graph[v].append((u, cnt))
    
    # Dijkstra
    pq = [(0, 0)]  # (moves_used, node)
    dist = {0: 0}
    used = {}
    
    while pq:
        moves, node = heapq.heappop(pq)
        
        if moves > dist.get(node, float('inf')):
            continue
        
        for neighbor, cnt in graph[node]:
            # How many subdivided nodes can reach on this edge
            edge_key = (node, neighbor)
            can_reach = min(cnt, maxMoves - moves)
            used[edge_key] = can_reach
            
            # Can reach neighbor?
            moves_to_neighbor = moves + cnt + 1
            if moves_to_neighbor <= maxMoves:
                if neighbor not in dist or moves_to_neighbor < dist[neighbor]:
                    dist[neighbor] = moves_to_neighbor
                    heapq.heappush(pq, (moves_to_neighbor, neighbor))
    
    # Count reachable nodes
    result = len(dist)  # Original nodes
    
    # Add subdivided nodes
    for u, v, cnt in edges:
        result += min(cnt, used.get((u,v), 0) + used.get((v,u), 0))
    
    return result

# Example usage
edges = [[0,1,10],[0,2,1],[1,2,2]]
print(reachableNodes(edges, 6, 3))  # Output: 13
```

### ⏱️ Complexity Analysis

| Metric | Complexity | Notes |
|--------|------------|-------|
| Time | O(E log V) | Dijkstra |
| Space | O(V + E) | Graph storage |

---

## Problem 6: Cut Off Trees for Golf Event

**LeetCode 675 - Hard**

### Problem Statement
Cut trees in height order, find total steps (or -1 if impossible).

```
Input: forest = [[1,2,3],[0,0,4],[7,6,5]]
Output: 6
```

### Solution

```python
from collections import deque
import heapq

def cutOffTree(forest):
    """
    Sort trees by height, BFS between each pair.
    
    Logic:
    - Sort trees by height
    - BFS from current position to next tree
    - Sum all distances
    
    Time: O(m²*n² * t) where t = trees
    Space: O(m*n)
    """
    if not forest or not forest[0]:
        return -1
    
    m, n = len(forest), len(forest[0])
    
    # Find all trees
    trees = []
    for i in range(m):
        for j in range(n):
            if forest[i][j] > 1:
                trees.append((forest[i][j], i, j))
    
    # Sort by height
    trees.sort()
    
    def bfs(sr, sc, tr, tc):
        """BFS from (sr,sc) to (tr,tc)."""
        if sr == tr and sc == tc:
            return 0
        
        queue = deque([(sr, sc, 0)])
        visited = {(sr, sc)}
        
        while queue:
            r, c, dist = queue.popleft()
            
            for dr, dc in [(0,1), (1,0), (0,-1), (-1,0)]:
                nr, nc = r + dr, c + dc
                
                if (0 <= nr < m and 0 <= nc < n and 
                    (nr, nc) not in visited and forest[nr][nc] != 0):
                    
                    if nr == tr and nc == tc:
                        return dist + 1
                    
                    visited.add((nr, nc))
                    queue.append((nr, nc, dist + 1))
        
        return -1
    
    # Start at (0,0)
    sr, sc = 0, 0
    total_steps = 0
    
    for height, tr, tc in trees:
        steps = bfs(sr, sc, tr, tc)
        if steps == -1:
            return -1
        total_steps += steps
        sr, sc = tr, tc
    
    return total_steps

# Example usage
forest = [[1,2,3],[0,0,4],[7,6,5]]
print(cutOffTree(forest))  # Output: 6
```

### Optimization: A* Search

```python
def cutOffTree_astar(forest):
    """
    Use A* instead of BFS for each search.
    
    Time: O(m*n*log(m*n) * t)
    Space: O(m*n)
    """
    if not forest or not forest[0]:
        return -1
    
    m, n = len(forest), len(forest[0])
    
    trees = []
    for i in range(m):
        for j in range(n):
            if forest[i][j] > 1:
                trees.append((forest[i][j], i, j))
    trees.sort()
    
    def astar(sr, sc, tr, tc):
        """A* from (sr,sc) to (tr,tc)."""
        if sr == tr and sc == tc:
            return 0
        
        def heuristic(r, c):
            return abs(r - tr) + abs(c - tc)
        
        pq = [(heuristic(sr, sc), 0, sr, sc)]  # (f, g, r, c)
        visited = {(sr, sc)}
        
        while pq:
            f, g, r, c = heapq.heappop(pq)
            
            if r == tr and c == tc:
                return g
            
            for dr, dc in [(0,1), (1,0), (0,-1), (-1,0)]:
                nr, nc = r + dr, c + dc
                
                if (0 <= nr < m and 0 <= nc < n and 
                    (nr, nc) not in visited and forest[nr][nc] != 0):
                    
                    visited.add((nr, nc))
                    new_g = g + 1
                    new_f = new_g + heuristic(nr, nc)
                    heapq.heappush(pq, (new_f, new_g, nr, nc))
        
        return -1
    
    sr, sc = 0, 0
    total_steps = 0
    
    for height, tr, tc in trees:
        steps = astar(sr, sc, tr, tc)
        if steps == -1:
            return -1
        total_steps += steps
        sr, sc = tr, tc
    
    return total_steps
```

### ⏱️ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| BFS | O(m²*n²*t) | O(m*n) | Simple |
| A* | O(m*n*log(m*n)*t) | O(m*n) | ⭐ Faster |

---

## Problem 7: Parallel Courses III

**LeetCode 2050 - Hard**

### Problem Statement
Find minimum time to complete all courses with prerequisites and durations.

```
Input: n = 3, relations = [[1,3],[2,3]], time = [3,2,5]
Output: 8
Explanation: Course 1 (3) and 2 (2) in parallel, then 3 (5)
```

### Solution

```python
from collections import defaultdict, deque

def minimumTime(n, relations, time):
    """
    Topological sort with time tracking.
    
    Logic:
    - Build dependency graph
    - Use Kahn's algorithm
    - Track earliest completion time for each course
    
    Time: O(n + E)
    Space: O(n + E)
    """
    # Build graph
    graph = defaultdict(list)
    indegree = [0] * (n + 1)
    
    for prev, nxt in relations:
        graph[prev].append(nxt)
        indegree[nxt] += 1
    
    # Initialize with courses having no prerequisites
    queue = deque()
    completion_time = [0] * (n + 1)
    
    for i in range(1, n + 1):
        if indegree[i] == 0:
            queue.append(i)
            completion_time[i] = time[i - 1]
    
    # Process courses in topological order
    while queue:
        course = queue.popleft()
        
        for next_course in graph[course]:
            # Update earliest start time for next course
            completion_time[next_course] = max(
                completion_time[next_course],
                completion_time[course] + time[next_course - 1]
            )
            
            indegree[next_course] -= 1
            if indegree[next_course] == 0:
                queue.append(next_course)
    
    return max(completion_time)

# Example usage
n = 3
relations = [[1,3],[2,3]]
time = [3,2,5]
print(minimumTime(n, relations, time))  # Output: 8
```

### Alternative: DFS with Memoization

```python
def minimumTime_dfs(n, relations, time):
    """
    DFS with memoization.
    
    Time: O(n + E)
    Space: O(n + E)
    """
    graph = defaultdict(list)
    
    for prev, nxt in relations:
        graph[prev].append(nxt)
    
    memo = {}
    
    def dfs(course):
        """Return earliest completion time for course."""
        if course in memo:
            return memo[course]
        
        # Course time + max of all prerequisites
        max_prereq = 0
        for next_course in graph[course]:
            max_prereq = max(max_prereq, dfs(next_course))
        
        memo[course] = time[course - 1] + max_prereq
        return memo[course]
    
    # Try all courses as potential ending points
    return max(dfs(i) for i in range(1, n + 1))
```

### ⏱️ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Topological Sort | O(n + E) | O(n + E) | ⭐ Clean |
| DFS Memo | O(n + E) | O(n + E) | Alternative |

---

## 🎯 Pattern Summary

### Core DFS-BFS Patterns

1. **State Space Search** - BFS with complex state (position + metadata)
2. **Bidirectional Search** - Meet in middle for faster convergence
3. **A* Algorithm** - Heuristic-guided search for shortest path
4. **Alternating Constraints** - State includes last action
5. **Dijkstra Variants** - Weighted graphs with special counting
6. **Topological Sort with Timing** - Critical path method

### Problem Categories

| Category | Problems | Key Technique |
|----------|----------|---------------|
| State Space | Keys, Sliding Puzzle | BFS with state tuple |
| Bidirectional | Knight Moves, Puzzle | Meet in middle |
| Weighted Path | Reachable Nodes, Trees | Dijkstra/A* |
| Constraints | Alternating Colors | State includes constraint |
| Scheduling | Parallel Courses | Topological + timing |

### When to Use Each Pattern

- **BFS with State:** Multi-dimensional constraints (keys, colors, etc.)
- **Bidirectional BFS:** Large search space, known start & end
- **A*:** Shortest path with good heuristic available
- **Dijkstra:** Weighted graphs, need actual distances
- **Topological Sort:** Dependencies with timing/ordering

### Optimization Techniques

```python
# 1. Bidirectional BFS
front1, front2 = {start}, {end}
while front1 and front2:
    if len(front1) > len(front2):
        front1, front2 = front2, front1
    # Expand smaller frontier

# 2. A* with heuristic
def heuristic(state):
    return manhattan_distance(state, goal)
pq = [(heuristic(start) + 0, 0, start)]

# 3. State compression
state = (position, bitmask)  # Instead of (position, set)

# 4. Symmetry reduction
x, y = abs(x), abs(y)  # Only search first quadrant

# 5. Early termination
if found_solution and current_cost > best_cost:
    continue
```

### Common Pitfalls

1. **Forgetting to track state properly** (position + metadata)
2. **Not handling symmetry** (wasting computation)
3. **Using BFS when Dijkstra needed** (weighted edges)
4. **Poor heuristic in A*** (not admissible/consistent)
5. **Not pruning search space** (exponential explosion)

### Interview Tips

1. **Identify state space:** What defines unique state?
2. **Check for optimizations:** Bidirectional? Symmetry?
3. **Weighted vs unweighted:** Choose BFS or Dijkstra
4. **State compression:** Use bitmasks for sets
5. **Heuristics:** Manhattan/Euclidean for grids

Master these advanced DFS-BFS patterns for interview success! 🚀

