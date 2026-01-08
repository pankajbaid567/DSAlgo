# 🔥 Graph - Hard Problems Collection

A curated collection of challenging Graph problems with complete solutions, multiple approaches, and detailed explanations.

---

## 📚 Table of Contents

1. [Alien Dictionary](#problem-1-alien-dictionary)
2. [Critical Connections in Network](#problem-2-critical-connections-in-network)
3. [Word Ladder II](#problem-3-word-ladder-ii)
4. [Swim in Rising Water](#problem-4-swim-in-rising-water)
5. [Bus Routes](#problem-5-bus-routes)
6. [Minimum Cost to Make Valid Path](#problem-6-minimum-cost-to-make-valid-path)
7. [Shortest Path Visiting All Nodes](#problem-7-shortest-path-visiting-all-nodes)
8. [Pattern Summary](#-pattern-summary)

---

## Problem 1: Alien Dictionary

**LeetCode 269 - Hard** (Premium)

### Problem Statement
Given sorted dictionary of alien language, derive the order of characters in the alien alphabet.

```
Input: words = ["wrt","wrf","er","ett","rftt"]
Output: "wertf"
Explanation: From the input, we can determine:
  't' < 'f' (from "wrt" and "wrf")
  'w' < 'e' (from "wrt" and "er")
  'r' < 't' (from "er" and "ett")
  'e' < 'r' (from "er" and "rftt")
```

### 🎯 Intuition
This is a **topological sort** problem!

**Key Insights:**
1. Compare adjacent words to find character ordering
2. Build directed graph: edge u→v means u comes before v
3. Topological sort gives valid ordering
4. Check for cycles (invalid input)

**Edge Case:** If word2 is prefix of word1 but comes after → impossible

### 📊 Visual Representation

```
words = ["wrt", "wrf", "er", "ett", "rftt"]

Compare adjacent pairs:

"wrt" vs "wrf":
  w=w, r=r, t≠f → t comes before f
  Graph: t → f

"wrf" vs "er":
  w≠e → w comes before e
  Graph: w → e

"er" vs "ett":
  e=e, r≠t → r comes before t
  Graph: r → t

"ett" vs "rftt":
  e≠r → e comes before r
  Graph: e → r

Final Graph:
    w → e → r → t → f

Topological Sort: w, e, r, t, f → "wertf"
```

### Solution

```python
from collections import defaultdict, deque

def alienOrder(words):
    """
    Build graph from word comparisons, then topological sort.
    
    Logic:
    - Compare adjacent words to find character precedence
    - Build directed graph
    - Topological sort with cycle detection
    
    Time: O(C) where C = total characters in all words
    Space: O(1) since at most 26 letters
    """
    # Initialize graph
    graph = defaultdict(set)
    in_degree = {char: 0 for word in words for char in word}
    
    # Build graph by comparing adjacent words
    for i in range(len(words) - 1):
        word1, word2 = words[i], words[i + 1]
        min_len = min(len(word1), len(word2))
        
        # Check for invalid case: word2 is prefix of word1
        if len(word1) > len(word2) and word1[:min_len] == word2[:min_len]:
            return ""
        
        # Find first different character
        for j in range(min_len):
            if word1[j] != word2[j]:
                # word1[j] comes before word2[j]
                if word2[j] not in graph[word1[j]]:
                    graph[word1[j]].add(word2[j])
                    in_degree[word2[j]] += 1
                break
    
    # Topological sort using BFS (Kahn's algorithm)
    queue = deque([char for char in in_degree if in_degree[char] == 0])
    result = []
    
    while queue:
        char = queue.popleft()
        result.append(char)
        
        for neighbor in graph[char]:
            in_degree[neighbor] -= 1
            if in_degree[neighbor] == 0:
                queue.append(neighbor)
    
    # Check if all characters processed (no cycle)
    if len(result) != len(in_degree):
        return ""  # Cycle detected
    
    return "".join(result)

# Example usage
words = ["wrt", "wrf", "er", "ett", "rftt"]
print(alienOrder(words))  # Output: "wertf"
```

### Alternative: DFS Topological Sort

```python
def alienOrder_DFS(words):
    """
    Use DFS for topological sort.
    
    Time: O(C)
    Space: O(1)
    """
    # Build graph
    graph = defaultdict(set)
    in_degree = {char: 0 for word in words for char in word}
    
    for i in range(len(words) - 1):
        word1, word2 = words[i], words[i + 1]
        min_len = min(len(word1), len(word2))
        
        if len(word1) > len(word2) and word1[:min_len] == word2[:min_len]:
            return ""
        
        for j in range(min_len):
            if word1[j] != word2[j]:
                if word2[j] not in graph[word1[j]]:
                    graph[word1[j]].add(word2[j])
                break
    
    # DFS with cycle detection
    WHITE, GRAY, BLACK = 0, 1, 2
    color = {char: WHITE for char in in_degree}
    result = []
    
    def dfs(node):
        if color[node] == GRAY:
            return False  # Cycle detected
        if color[node] == BLACK:
            return True  # Already processed
        
        color[node] = GRAY
        for neighbor in graph[node]:
            if not dfs(neighbor):
                return False
        
        color[node] = BLACK
        result.append(node)
        return True
    
    for char in in_degree:
        if color[char] == WHITE:
            if not dfs(char):
                return ""
    
    return "".join(reversed(result))
```

### 🔍 Dry Run

```
words = ["abc", "adc"]

Compare "abc" vs "adc":
  a=a, b≠d → b comes before d
  Graph: b → d

in_degree = {a:0, b:0, c:0, d:1}

Topological Sort:
  queue = [a, b, c]
  
  Process a: result=[a]
  Process b: result=[a,b], d's degree becomes 0
  Process c: result=[a,b,c]
  Add d to queue
  Process d: result=[a,b,c,d]

Output: "abcd"
```

### ⏱️ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| BFS | O(C) | O(1) | ⭐ Kahn's algorithm |
| DFS | O(C) | O(1) | Cycle detection |

Where C = total characters in all words

---

## Problem 2: Critical Connections in Network

**LeetCode 1192 - Hard**

### Problem Statement
Find all critical connections (bridges) in a network. A critical connection is an edge whose removal increases the number of connected components.

```
Input: n = 4, connections = [[0,1],[1,2],[2,0],[1,3]]
Output: [[1,3]]
Explanation: Removing [1,3] disconnects node 3
```

### 🎯 Intuition
Use **Tarjan's Bridge-Finding Algorithm**:
- Track discovery time and lowest reachable vertex
- Edge (u,v) is bridge if: `low[v] > disc[u]`
- This means v cannot reach any ancestor of u without using edge u-v

### 📊 Visual Representation

```
Graph:
    0 ─── 1 ─── 3
     \   /
      \ /
       2

DFS from node 0:

Node  disc  low   Parent
0     0     0     None
1     1     0     0      (can reach 0)
2     2     0     1      (can reach 0 via cycle)
3     3     3     1      (cannot reach back!)

Edge Analysis:
  (0,1): low[1]=0 ≤ disc[0]=0 → NOT bridge
  (1,2): low[2]=0 ≤ disc[1]=1 → NOT bridge (cycle)
  (2,0): back edge
  (1,3): low[3]=3 > disc[1]=1 → BRIDGE! ✓

Critical connections: [[1,3]]
```

### Solution: Tarjan's Algorithm

```python
from collections import defaultdict

def criticalConnections(n, connections):
    """
    Find bridges using Tarjan's algorithm.
    
    Logic:
    - DFS tracking discovery time and low-link value
    - Edge (u,v) is bridge if low[v] > disc[u]
    - low[v] = minimum of:
      * disc[v]
      * low[neighbor] for all neighbors
      * disc[neighbor] if back edge
    
    Time: O(V + E)
    Space: O(V + E)
    """
    graph = defaultdict(list)
    for u, v in connections:
        graph[u].append(v)
        graph[v].append(u)
    
    disc = [-1] * n  # Discovery time
    low = [-1] * n   # Lowest reachable vertex
    time = [0]       # Current time
    bridges = []
    
    def dfs(node, parent):
        disc[node] = low[node] = time[0]
        time[0] += 1
        
        for neighbor in graph[node]:
            if neighbor == parent:
                continue
            
            if disc[neighbor] == -1:  # Not visited
                dfs(neighbor, node)
                
                # Update low value
                low[node] = min(low[node], low[neighbor])
                
                # Check if bridge
                if low[neighbor] > disc[node]:
                    bridges.append([node, neighbor])
            else:
                # Back edge, update low
                low[node] = min(low[node], disc[neighbor])
    
    # Run DFS from each component
    for i in range(n):
        if disc[i] == -1:
            dfs(i, -1)
    
    return bridges

# Example usage
n = 4
connections = [[0,1],[1,2],[2,0],[1,3]]
print(criticalConnections(n, connections))  # Output: [[1,3]]
```

### Alternative: Union-Find Approach

```python
class UnionFind:
    def __init__(self, n):
        self.parent = list(range(n))
        self.rank = [0] * n
    
    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])
        return self.parent[x]
    
    def union(self, x, y):
        px, py = self.find(x), self.find(y)
        if px == py:
            return False
        if self.rank[px] < self.rank[py]:
            px, py = py, px
        self.parent[py] = px
        if self.rank[px] == self.rank[py]:
            self.rank[px] += 1
        return True

def criticalConnections_UF(n, connections):
    """
    For each edge, check if removing it disconnects graph.
    
    Time: O(E² * α(V)) - too slow for large inputs
    Space: O(V)
    """
    bridges = []
    
    for i, (u, v) in enumerate(connections):
        # Try without this edge
        uf = UnionFind(n)
        for j, (a, b) in enumerate(connections):
            if i != j:
                uf.union(a, b)
        
        # Check if still connected
        if uf.find(u) != uf.find(v):
            bridges.append([u, v])
    
    return bridges
```

### 🔍 Dry Run

```
n=5, connections=[[0,1],[1,2],[2,0],[1,3],[3,4]]

Build graph:
  0: [1, 2]
  1: [0, 2, 3]
  2: [1, 0]
  3: [1, 4]
  4: [3]

DFS from 0:

Visit 0: disc[0]=0, low[0]=0
  Visit 1: disc[1]=1, low[1]=1
    Visit 2: disc[2]=2, low[2]=2
      Neighbor 1: visited, low[2]=min(2,1)=1
      Neighbor 0: visited, low[2]=min(1,0)=0
    Back to 1: low[1]=min(1,0)=0
    Visit 3: disc[3]=3, low[3]=3
      Visit 4: disc[4]=4, low[4]=4
        Only neighbor is 3 (parent)
      Back to 3: low[3]=min(3,4)=3
      Check: low[4]=4 > disc[3]=3 → Bridge!
    Back to 1: low[1]=min(0,3)=0
    Check: low[3]=3 > disc[1]=1 → Bridge!
  Back to 0: low[0]=min(0,0)=0

Bridges: [[3,4], [1,3]]
```

### ⏱️ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Tarjan | O(V+E) | O(V+E) | ⭐ Optimal |
| Union-Find | O(E²α(V)) | O(V) | Too slow |

---

## Problem 3: Word Ladder II

**LeetCode 126 - Hard**

### Problem Statement
Find all shortest transformation sequences from `beginWord` to `endWord` where each transformed word exists in dictionary and differs by one letter.

```
Input: beginWord = "hit", endWord = "cog"
       wordList = ["hot","dot","dog","lot","log","cog"]
Output: [["hit","hot","dot","dog","cog"],
         ["hit","hot","lot","log","cog"]]
```

### 🎯 Intuition
**Two-phase approach:**
1. **BFS**: Find shortest distance to each word from start
2. **DFS**: Backtrack to build all shortest paths

**Why not just BFS?** BFS finds one shortest path, but we need ALL shortest paths.

### 📊 Visual Representation

```
Word graph (edges = 1 char difference):

hit → hot → dot → dog → cog
        ↓     ↓    ↓
       lot → log ─┘

BFS distances from "hit":
  hit: 0
  hot: 1
  dot, lot: 2
  dog, log: 3
  cog: 4

DFS backtracking from "cog" (dist=4):
  cog ← dog (dist=3) ← dot (dist=2) ← hot (dist=1) ← hit (dist=0) ✓
  cog ← log (dist=3) ← lot (dist=2) ← hot (dist=1) ← hit (dist=0) ✓
  cog ← dog (dist=3) ← hot (dist=1)? No! (not adjacent)
```

### Solution

```python
from collections import defaultdict, deque

def findLadders(beginWord, endWord, wordList):
    """
    BFS to find distances, DFS to build paths.
    
    Logic:
    - BFS: Calculate shortest distance to each word
    - Build parent map during BFS
    - DFS: Backtrack from endWord using parents
    
    Time: O(N * L² + P) where N=words, L=length, P=paths
    Space: O(N * L)
    """
    wordSet = set(wordList)
    if endWord not in wordSet:
        return []
    
    # Build adjacency list
    def get_neighbors(word):
        neighbors = []
        for i in range(len(word)):
            for c in 'abcdefghijklmnopqrstuvwxyz':
                if c != word[i]:
                    new_word = word[:i] + c + word[i+1:]
                    if new_word in wordSet:
                        neighbors.append(new_word)
        return neighbors
    
    # BFS to find shortest distances
    queue = deque([beginWord])
    distances = {beginWord: 0}
    parents = defaultdict(list)
    found = False
    level = 0
    
    while queue and not found:
        level_size = len(queue)
        visited_this_level = set()
        
        for _ in range(level_size):
            word = queue.popleft()
            
            if word == endWord:
                found = True
                continue
            
            for neighbor in get_neighbors(word):
                if neighbor not in distances:
                    distances[neighbor] = level + 1
                
                if distances[neighbor] == level + 1:
                    parents[neighbor].append(word)
                    if neighbor not in visited_this_level:
                        queue.append(neighbor)
                        visited_this_level.add(neighbor)
        
        level += 1
    
    if not found:
        return []
    
    # DFS to build all paths
    result = []
    
    def dfs(word, path):
        if word == beginWord:
            result.append(path[::-1])
            return
        
        for parent in parents[word]:
            dfs(parent, path + [parent])
    
    dfs(endWord, [endWord])
    return result

# Example usage
beginWord = "hit"
endWord = "cog"
wordList = ["hot","dot","dog","lot","log","cog"]
print(findLadders(beginWord, endWord, wordList))
```

### Optimized: Bidirectional BFS

```python
def findLadders_bidirectional(beginWord, endWord, wordList):
    """
    Bidirectional BFS for faster search.
    
    Time: O(N * L²)
    Space: O(N)
    """
    wordSet = set(wordList)
    if endWord not in wordSet:
        return []
    
    # Search from both ends
    forward = {beginWord: [[beginWord]]}
    backward = {endWord: [[endWord]]}
    result = []
    direction = 1  # 1: forward, -1: backward
    
    def get_neighbors(word):
        neighbors = []
        for i in range(len(word)):
            for c in 'abcdefghijklmnopqrstuvwxyz':
                new_word = word[:i] + c + word[i+1:]
                if new_word in wordSet:
                    neighbors.append(new_word)
        return neighbors
    
    while forward and not result:
        # Always expand smaller frontier
        if len(forward) > len(backward):
            forward, backward = backward, forward
            direction = -direction
        
        # Remove current level words
        for word in forward:
            wordSet.discard(word)
        
        next_level = defaultdict(list)
        
        for word in forward:
            for path in forward[word]:
                for neighbor in get_neighbors(word):
                    if neighbor in backward:
                        # Found connection!
                        if direction == 1:
                            result.extend([path + p[::-1] for p in backward[neighbor]])
                        else:
                            result.extend([p[::-1] + path for p in backward[neighbor]])
                    
                    if neighbor in wordSet and not result:
                        next_level[neighbor].append(
                            path + [neighbor] if direction == 1 else [neighbor] + path
                        )
        
        forward = next_level
    
    return result
```

### 🔍 Dry Run

```
beginWord="hit", endWord="cog"
wordList=["hot","dot","dog","lot","log","cog"]

BFS Phase:

Level 0: hit (dist=0)
Level 1: hot (dist=1)
  parents[hot] = [hit]
Level 2: dot, lot (dist=2)
  parents[dot] = [hot]
  parents[lot] = [hot]
Level 3: dog, log (dist=3)
  parents[dog] = [dot]
  parents[log] = [lot]
Level 4: cog (dist=4)
  parents[cog] = [dog, log]

DFS Phase from "cog":

Path 1:
  cog → parents = [dog, log]
  dog → parents = [dot]
  dot → parents = [hot]
  hot → parents = [hit]
  hit → base case
  Result: [hit, hot, dot, dog, cog]

Path 2:
  cog → parents = [dog, log]
  log → parents = [lot]
  lot → parents = [hot]
  hot → parents = [hit]
  hit → base case
  Result: [hit, hot, lot, log, cog]

Final: 2 shortest paths found!
```

### ⏱️ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| BFS+DFS | O(N*L²+P) | O(N*L) | P=paths |
| Bidirectional | O(N*L²) | O(N) | ⭐ Faster search |

---

## Problem 4: Swim in Rising Water

**LeetCode 778 - Hard**

### Problem Statement
In NxN grid where `grid[i][j]` represents water elevation, find minimum time to swim from (0,0) to (n-1,n-1). At time t, you can swim in cells with elevation ≤ t.

```
Input: grid = [[0,2],[1,3]]
Output: 3
Explanation: At time 3, you can go 0→1→3 or 0→2→3
```

### 🎯 Intuition
**Three approaches:**
1. **Binary Search + BFS**: Binary search on time, check if path exists
2. **Dijkstra**: Min heap with cost = max elevation seen
3. **Union-Find**: Process cells in elevation order

Binary search is most intuitive!

### 📊 Visual Representation

```
Grid:  0  2
       1  3

Binary search on time [0, 3]:

Time = 1:
  Can visit: 0, 1
  0 → 1? No (not adjacent)
  Path to (1,1)? NO

Time = 2:
  Can visit: 0, 1, 2
  0 → 2 (adjacent!)
  Path to (1,1)? NO (3 not accessible)

Time = 3:
  Can visit: all cells
  0 → 1 → 3 ✓
  0 → 2 → 3 ✓
  Path to (1,1)? YES!

Minimum time = 3
```

### Approach 1: Binary Search + BFS

```python
from collections import deque

def swimInWater(grid):
    """
    Binary search on time, BFS to check reachability.
    
    Logic:
    - Binary search time in [0, max_elevation]
    - For each time, BFS to check if destination reachable
    - Find minimum valid time
    
    Time: O(n² log n²) = O(n² log n)
    Space: O(n²)
    """
    n = len(grid)
    
    def can_reach(time):
        """Check if can reach (n-1,n-1) at given time."""
        if grid[0][0] > time:
            return False
        
        visited = {(0, 0)}
        queue = deque([(0, 0)])
        
        while queue:
            r, c = queue.popleft()
            
            if r == n - 1 and c == n - 1:
                return True
            
            for dr, dc in [(0,1), (1,0), (0,-1), (-1,0)]:
                nr, nc = r + dr, c + dc
                if (0 <= nr < n and 0 <= nc < n and
                    (nr, nc) not in visited and
                    grid[nr][nc] <= time):
                    visited.add((nr, nc))
                    queue.append((nr, nc))
        
        return False
    
    # Binary search on time
    left, right = grid[0][0], n * n - 1
    
    while left < right:
        mid = (left + right) // 2
        if can_reach(mid):
            right = mid
        else:
            left = mid + 1
    
    return left

# Example usage
grid = [[0,2],[1,3]]
print(swimInWater(grid))  # Output: 3
```

### Approach 2: Dijkstra's Algorithm

```python
import heapq

def swimInWater_dijkstra(grid):
    """
    Use Dijkstra with cost = max elevation on path.
    
    Time: O(n² log n²)
    Space: O(n²)
    """
    n = len(grid)
    
    # Min heap: (max_elevation, row, col)
    heap = [(grid[0][0], 0, 0)]
    visited = set()
    
    while heap:
        elevation, r, c = heapq.heappop(heap)
        
        if (r, c) in visited:
            continue
        
        visited.add((r, c))
        
        if r == n - 1 and c == n - 1:
            return elevation
        
        for dr, dc in [(0,1), (1,0), (0,-1), (-1,0)]:
            nr, nc = r + dr, c + dc
            if 0 <= nr < n and 0 <= nc < n and (nr, nc) not in visited:
                new_elevation = max(elevation, grid[nr][nc])
                heapq.heappush(heap, (new_elevation, nr, nc))
    
    return -1
```

### Approach 3: Union-Find

```python
class UnionFind:
    def __init__(self, n):
        self.parent = list(range(n))
    
    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])
        return self.parent[x]
    
    def union(self, x, y):
        self.parent[self.find(x)] = self.find(y)

def swimInWater_uf(grid):
    """
    Process cells in elevation order, union adjacent cells.
    
    Time: O(n² log n²)
    Space: O(n²)
    """
    n = len(grid)
    uf = UnionFind(n * n)
    
    # Create list of (elevation, r, c)
    cells = []
    for r in range(n):
        for c in range(n):
            cells.append((grid[r][c], r, c))
    
    cells.sort()
    
    directions = [(0,1), (1,0), (0,-1), (-1,0)]
    accessible = [[False] * n for _ in range(n)]
    
    for elevation, r, c in cells:
        accessible[r][c] = True
        
        # Union with accessible neighbors
        for dr, dc in directions:
            nr, nc = r + dr, c + dc
            if 0 <= nr < n and 0 <= nc < n and accessible[nr][nc]:
                uf.union(r * n + c, nr * n + nc)
        
        # Check if (0,0) and (n-1,n-1) connected
        if uf.find(0) == uf.find(n * n - 1):
            return elevation
    
    return -1
```

### ⏱️ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Binary Search | O(n² log n) | O(n²) | ⭐ Intuitive |
| Dijkstra | O(n² log n) | O(n²) | Clean code |
| Union-Find | O(n² log n) | O(n²) | Elegant |

---

## Problem 5: Bus Routes

**LeetCode 815 - Hard**

### Problem Statement
Given array `routes` where `routes[i]` is bus route i, find minimum buses to reach `target` from `source`.

```
Input: routes = [[1,2,7],[3,6,7]], source = 1, target = 6
Output: 2
Explanation: Take bus 0 to stop 7, then bus 1 to stop 6
```

### 🎯 Intuition
Treat this as **graph problem** where:
- Nodes = bus routes (not stops!)
- Edge exists if two routes share a stop
- BFS to find shortest path from routes containing source to routes containing target

**Why buses not stops?** Reduces complexity when routes are long.

### 📊 Visual Representation

```
routes = [[1,2,7], [3,6,7]]

Stop → Routes mapping:
  1: [0]
  2: [0]
  7: [0, 1]  ← Common stop!
  3: [1]
  6: [1]

Route Graph:
  Route 0 connects to Route 1 via stop 7

BFS:
  Start: routes containing stop 1 = {0}
  Target: routes containing stop 6 = {1}
  
  Level 0: Route 0
  Level 1: Route 1 (via stop 7)
  
  Buses needed: 2
```

### Solution

```python
from collections import defaultdict, deque

def numBusesToDestination(routes, source, target):
    """
    BFS on route graph.
    
    Logic:
    - Build stop → routes mapping
    - Build route → routes adjacency (via common stops)
    - BFS from source routes to target routes
    
    Time: O(N² * K) worst case, usually much better
    Space: O(N * K)
    
    N = number of routes, K = average stops per route
    """
    if source == target:
        return 0
    
    # Build stop → routes mapping
    stop_to_routes = defaultdict(set)
    for i, route in enumerate(routes):
        for stop in route:
            stop_to_routes[stop].add(i)
    
    # Check if source/target exist
    if source not in stop_to_routes or target not in stop_to_routes:
        return -1
    
    # BFS
    queue = deque([(route, 1) for route in stop_to_routes[source]])
    visited_routes = set(stop_to_routes[source])
    visited_stops = set(routes[queue[0][0]] if queue else [])
    
    while queue:
        route_idx, buses = queue.popleft()
        
        # Check all stops in current route
        for stop in routes[route_idx]:
            if stop == target:
                return buses
            
            if stop in visited_stops:
                continue
            
            visited_stops.add(stop)
            
            # Add all routes passing through this stop
            for next_route in stop_to_routes[stop]:
                if next_route not in visited_routes:
                    visited_routes.add(next_route)
                    queue.append((next_route, buses + 1))
    
    return -1

# Example usage
routes = [[1,2,7], [3,6,7]]
source, target = 1, 6
print(numBusesToDestination(routes, source, target))  # Output: 2
```

### Optimized: Faster BFS

```python
def numBusesToDestination_optimized(routes, source, target):
    """
    More efficient BFS implementation.
    
    Time: O(N * K)
    Space: O(N * K)
    """
    if source == target:
        return 0
    
    # Build graph
    stop_to_routes = defaultdict(list)
    for i, route in enumerate(routes):
        for stop in route:
            stop_to_routes[stop].append(i)
    
    # BFS
    queue = deque([source])
    visited_stops = {source}
    visited_routes = set()
    buses = 0
    
    while queue:
        buses += 1
        size = len(queue)
        
        for _ in range(size):
            stop = queue.popleft()
            
            # Check all routes passing through this stop
            for route_idx in stop_to_routes[stop]:
                if route_idx in visited_routes:
                    continue
                
                visited_routes.add(route_idx)
                
                # Check all stops in this route
                for next_stop in routes[route_idx]:
                    if next_stop == target:
                        return buses
                    
                    if next_stop not in visited_stops:
                        visited_stops.add(next_stop)
                        queue.append(next_stop)
    
    return -1
```

### ⏱️ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Route BFS | O(N*K) | O(N*K) | ⭐ Efficient |
| Stop BFS | O(N*K) | O(N*K) | More intuitive |

Where N = routes, K = avg stops per route

---

## Problem 6: Minimum Cost to Make Valid Path

**LeetCode 1368 - Hard**

### Problem Statement
Given grid where each cell has direction arrow, find minimum cost to change arrows to make path from (0,0) to (m-1,n-1). Cost to change one arrow = 1.

```
Input: grid = [[1,1,1,1],[2,2,2,2],[1,1,1,1],[2,2,2,2]]
Output: 3
```

### 🎯 Intuition
Use **0-1 BFS** (Deque):
- Moving in arrow's direction = cost 0 (add to front)
- Changing direction = cost 1 (add to back)
- This maintains cost ordering without full priority queue

**Key:** This is Dijkstra with only costs 0 and 1!

### 📊 Visual Representation

```
Grid (1=right, 2=down):
  1 → 1 → 1 → 1
  2 ↓ 2 ↓ 2 ↓ 2 ↓
  1 → 1 → 1 → 1
  2 ↓ 2 ↓ 2 ↓ 2 ↓

0-1 BFS:

Start (0,0):
  Arrow points right (1)
  Go right: cost 0 (front of deque)
  Go down: cost 1 (back of deque)

Optimal path:
  (0,0) → (0,1) → (0,2) → (0,3) cost 0
  (0,3) → (1,3) cost 1 (change!)
  (1,3) → (2,3) cost 0
  (2,3) → (3,3) cost 1 (change!)
  
  Total: 3 changes
```

### Solution: 0-1 BFS

```python
from collections import deque

def minCost(grid):
    """
    Use 0-1 BFS with deque.
    
    Logic:
    - Moving in arrow direction: cost 0 (add to front)
    - Changing direction: cost 1 (add to back)
    - Deque maintains cost ordering
    
    Time: O(m * n)
    Space: O(m * n)
    """
    m, n = len(grid), len(grid[0])
    
    # Directions: 1=right, 2=down, 3=left, 4=up
    directions = {
        1: (0, 1),   # right
        2: (1, 0),   # down
        3: (0, -1),  # left
        4: (-1, 0)   # up
    }
    
    # Deque: (cost, row, col)
    dq = deque([(0, 0, 0)])
    dist = [[float('inf')] * n for _ in range(m)]
    dist[0][0] = 0
    
    while dq:
        cost, r, c = dq.popleft()
        
        if r == m - 1 and c == n - 1:
            return cost
        
        if cost > dist[r][c]:
            continue
        
        # Try all 4 directions
        for direction, (dr, dc) in directions.items():
            nr, nc = r + dr, c + dc
            
            if 0 <= nr < m and 0 <= nc < n:
                # Cost 0 if following arrow, cost 1 if changing
                new_cost = cost + (0 if grid[r][c] == direction else 1)
                
                if new_cost < dist[nr][nc]:
                    dist[nr][nc] = new_cost
                    
                    if grid[r][c] == direction:
                        dq.appendleft((new_cost, nr, nc))  # Cost 0
                    else:
                        dq.append((new_cost, nr, nc))  # Cost 1
    
    return dist[m-1][n-1]

# Example usage
grid = [[1,1,1,1],[2,2,2,2],[1,1,1,1],[2,2,2,2]]
print(minCost(grid))  # Output: 3
```

### Alternative: Dijkstra

```python
import heapq

def minCost_dijkstra(grid):
    """
    Standard Dijkstra's algorithm.
    
    Time: O(mn log(mn))
    Space: O(mn)
    """
    m, n = len(grid), len(grid[0])
    directions = {1: (0,1), 2: (1,0), 3: (0,-1), 4: (-1,0)}
    
    heap = [(0, 0, 0)]  # (cost, row, col)
    dist = [[float('inf')] * n for _ in range(m)]
    dist[0][0] = 0
    
    while heap:
        cost, r, c = heapq.heappop(heap)
        
        if r == m - 1 and c == n - 1:
            return cost
        
        if cost > dist[r][c]:
            continue
        
        for direction, (dr, dc) in directions.items():
            nr, nc = r + dr, c + dc
            
            if 0 <= nr < m and 0 <= nc < n:
                new_cost = cost + (0 if grid[r][c] == direction else 1)
                
                if new_cost < dist[nr][nc]:
                    dist[nr][nc] = new_cost
                    heapq.heappush(heap, (new_cost, nr, nc))
    
    return dist[m-1][n-1]
```

### ⏱️ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| 0-1 BFS | O(mn) | O(mn) | ⭐ Optimal for 0-1 costs |
| Dijkstra | O(mn log mn) | O(mn) | More general |

---

## Problem 7: Shortest Path Visiting All Nodes

**LeetCode 847 - Hard**

### Problem Statement
Given undirected connected graph, find length of shortest path that visits every node at least once. Can start and end at any node, can revisit.

```
Input: graph = [[1,2,3],[0],[0],[0]]
Output: 4
Explanation: Path: 1-0-2-0-3 or 1-0-3-0-2
```

### 🎯 Intuition
Use **BFS with bitmask** state:
- State = (node, visited_mask)
- visited_mask tracks which nodes visited
- Goal: reach state where all nodes visited

**Why bitmask?** n ≤ 12, so 2^12 states manageable!

### 📊 Visual Representation

```
Graph:    1
        / | \
       0  2  3

States (node, mask):
  Start: can begin at any node
  Goal: mask = 1111 (all 4 nodes visited)

BFS:
  Level 0: (0,0001), (1,0010), (2,0100), (3,1000)
  
  From (1, 0010):
    Visit 0: (0, 0011)
    Visit 2: (2, 0110)
    Visit 3: (3, 1010)
  
  From (0, 0011):
    Visit 1: (1, 0011) - already visited
    Visit 2: (2, 0111)
    Visit 3: (3, 1011)
  
  Continue until mask = 1111
  
  Shortest path length = 4
```

### Solution

```python
from collections import deque

def shortestPathLength(graph):
    """
    BFS with state = (node, visited_bitmask).
    
    Logic:
    - Each state tracks current node and visited set
    - Use bitmask to represent visited nodes
    - BFS finds shortest path to all-visited state
    
    Time: O(n * 2^n)
    Space: O(n * 2^n)
    """
    n = len(graph)
    target_mask = (1 << n) - 1  # All nodes visited
    
    # Start from all nodes simultaneously
    queue = deque()
    visited = set()
    
    for i in range(n):
        mask = 1 << i
        queue.append((i, mask, 0))  # (node, mask, distance)
        visited.add((i, mask))
    
    while queue:
        node, mask, dist = queue.popleft()
        
        # All nodes visited
        if mask == target_mask:
            return dist
        
        # Explore neighbors
        for neighbor in graph[node]:
            new_mask = mask | (1 << neighbor)
            state = (neighbor, new_mask)
            
            if state not in visited:
                visited.add(state)
                queue.append((neighbor, new_mask, dist + 1))
    
    return -1  # Should never reach

# Example usage
graph = [[1,2,3],[0],[0],[0]]
print(shortestPathLength(graph))  # Output: 4
```

### Alternative: Dijkstra with States

```python
import heapq

def shortestPathLength_dijkstra(graph):
    """
    Dijkstra on state graph.
    
    Time: O(n * 2^n * log(n * 2^n))
    Space: O(n * 2^n)
    """
    n = len(graph)
    target_mask = (1 << n) - 1
    
    heap = []
    dist = {}
    
    # Initialize with all starting nodes
    for i in range(n):
        mask = 1 << i
        heapq.heappush(heap, (0, i, mask))
        dist[(i, mask)] = 0
    
    while heap:
        d, node, mask = heapq.heappop(heap)
        
        if mask == target_mask:
            return d
        
        if d > dist.get((node, mask), float('inf')):
            continue
        
        for neighbor in graph[node]:
            new_mask = mask | (1 << neighbor)
            new_dist = d + 1
            
            if new_dist < dist.get((neighbor, new_mask), float('inf')):
                dist[(neighbor, new_mask)] = new_dist
                heapq.heappush(heap, (new_dist, neighbor, new_mask))
    
    return -1
```

### 🔍 Dry Run

```
graph = [[1],[0,2],[1]]

n = 3, target_mask = 111 (binary) = 7

Initial states:
  (0, 001, 0) → mask=1
  (1, 010, 0) → mask=2
  (2, 100, 0) → mask=4

BFS:

Level 0:
  (0,1,0), (1,2,0), (2,4,0)

Level 1:
  From (0,1,0) → neighbor 1:
    (1, 011, 1) → mask=3
  From (1,2,0) → neighbor 0:
    (0, 011, 1) → mask=3
  From (1,2,0) → neighbor 2:
    (2, 110, 1) → mask=6
  From (2,4,0) → neighbor 1:
    (1, 110, 1) → mask=6

Level 2:
  From (1,3,1) → neighbor 2:
    (2, 111, 2) → mask=7 ✓

Shortest path = 2
```

### ⏱️ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| BFS | O(n*2^n) | O(n*2^n) | ⭐ Optimal for n≤12 |
| Dijkstra | O(n*2^n*log(n*2^n)) | O(n*2^n) | Overkill |

---

## 🎯 Pattern Summary

### Core Graph Patterns Covered

1. **Topological Sort**
   - Problems: Alien Dictionary
   - Pattern: BFS (Kahn's) or DFS with cycle detection
   - Use: Dependency resolution, ordering

2. **Bridge Finding**
   - Problems: Critical Connections
   - Pattern: Tarjan's algorithm with disc/low values
   - Use: Network reliability, single points of failure

3. **Multi-source BFS**
   - Problems: Word Ladder II
   - Pattern: BFS with parent tracking, DFS backtracking
   - Use: All shortest paths

4. **Binary Search + Graph**
   - Problems: Swim in Rising Water
   - Pattern: Binary search on answer, BFS to verify
   - Use: Optimization with constraints

5. **Graph Transformation**
   - Problems: Bus Routes
   - Pattern: Transform problem to different graph
   - Use: Simplify complex relationships

6. **0-1 BFS**
   - Problems: Minimum Cost Path
   - Pattern: Deque with 0-cost front, 1-cost back
   - Use: Shortest path with only 0/1 weights

7. **Bitmask DP/BFS**
   - Problems: Shortest Path All Nodes
   - Pattern: State = (node, visited_set)
   - Use: TSP-like problems, small n

### Complexity Patterns

- **BFS/DFS**: O(V+E) typically
- **Topological Sort**: O(V+E)
- **Tarjan's**: O(V+E)
- **Binary Search + BFS**: O(log(max) * (V+E))
- **Bitmask**: O(n * 2^n) for small n

### When to Use Each Pattern

✅ **Topological Sort**: Dependencies, prerequisites
✅ **Tarjan's**: Bridges, articulation points, SCCs
✅ **Multi-source BFS**: All shortest paths
✅ **Binary Search**: Optimization problems
✅ **Graph Transform**: Simplify complex graphs
✅ **0-1 BFS**: Only 0 and 1 weights
✅ **Bitmask**: Visit all nodes/states, n ≤ 12

### Interview Tips

1. **Identify graph type**: Directed/undirected, weighted/unweighted
2. **Choose traversal**: BFS for shortest path, DFS for connectivity
3. **State representation**: What defines uniqueness?
4. **Optimization**: Can binary search? Can use bitmask?
5. **Edge cases**: Disconnected graph, cycles, self-loops

---

## 🎓 Key Takeaways

1. **Tarjan's algorithm** is essential for bridges/articulation points
2. **Topological sort** has two standard implementations (BFS/DFS)
3. **Multi-source BFS** powerful for all shortest paths
4. **0-1 BFS** is Dijkstra optimized for binary weights
5. **Bitmask states** enable solving NP problems for small n
6. **Graph transformation** can simplify complex problems

Master these patterns and graph problems become manageable! 🌐🚀

