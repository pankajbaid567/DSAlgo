# 🕸️ Graph Algorithms - Comprehensive Cheatsheet

## 📚 Table of Contents
1. [Core Concepts](#core-concepts)
2. [Graph Representations](#graph-representations)
3. [Traversal Algorithms](#traversal-algorithms)
4. [Shortest Path Algorithms](#shortest-path-algorithms)
5. [Minimum Spanning Tree](#minimum-spanning-tree)
6. [Advanced Patterns](#advanced-patterns)

---

## Core Concepts

### What is a Graph?
A graph G = (V, E) consists of vertices (nodes) and edges (connections).

**Types:**
- **Directed**: Edges have direction (A → B)
- **Undirected**: Edges are bidirectional (A ⟺ B)
- **Weighted**: Edges have weights/costs
- **Unweighted**: All edges have equal weight

---

## Graph Representations

### 1. Adjacency Matrix
```python
# n x n matrix where matrix[i][j] = 1 if edge exists
graph = [[0, 1, 1, 0],
         [1, 0, 1, 1],
         [1, 1, 0, 1],
         [0, 1, 1, 0]]

# Space: O(V²)
# Edge lookup: O(1)
# Get neighbors: O(V)
```

### 2. Adjacency List (Most Common)
```python
from collections import defaultdict

# Using dictionary
graph = {
    0: [1, 2],
    1: [0, 2, 3],
    2: [0, 1, 3],
    3: [1, 2]
}

# Using defaultdict
graph = defaultdict(list)
edges = [(0,1), (0,2), (1,2), (1,3), (2,3)]
for u, v in edges:
    graph[u].append(v)
    graph[v].append(u)  # For undirected

# Space: O(V + E)
# Edge lookup: O(degree)
# Get neighbors: O(degree)
```

### 3. Edge List
```python
edges = [(0, 1, 5), (1, 2, 3), (0, 2, 7)]  # (from, to, weight)

# Space: O(E)
# Useful for: Kruskal's, edge-based algorithms
```

---

## Traversal Algorithms

### 1. Depth First Search (DFS)

```python
# Recursive DFS
def dfs_recursive(graph, node, visited=None):
    if visited is None:
        visited = set()
    
    visited.add(node)
    print(node, end=' ')
    
    for neighbor in graph[node]:
        if neighbor not in visited:
            dfs_recursive(graph, neighbor, visited)
    
    return visited

# Iterative DFS using stack
def dfs_iterative(graph, start):
    visited = set()
    stack = [start]
    
    while stack:
        node = stack.pop()
        
        if node not in visited:
            visited.add(node)
            print(node, end=' ')
            
            # Add neighbors in reverse for same order as recursive
            for neighbor in reversed(graph[node]):
                if neighbor not in visited:
                    stack.append(neighbor)
    
    return visited

# DFS with path tracking
def find_path_dfs(graph, start, end, path=None):
    if path is None:
        path = []
    
    path = path + [start]
    
    if start == end:
        return path
    
    for neighbor in graph[start]:
        if neighbor not in path:
            new_path = find_path_dfs(graph, neighbor, end, path)
            if new_path:
                return new_path
    
    return None

# DFS for cycle detection in directed graph
def has_cycle_directed(graph, n):
    WHITE, GRAY, BLACK = 0, 1, 2
    color = [WHITE] * n
    
    def dfs(node):
        if color[node] == GRAY:
            return True  # Back edge found
        if color[node] == BLACK:
            return False
        
        color[node] = GRAY
        for neighbor in graph[node]:
            if dfs(neighbor):
                return True
        color[node] = BLACK
        return False
    
    for i in range(n):
        if color[i] == WHITE:
            if dfs(i):
                return True
    
    return False

# DFS for cycle detection in undirected graph
def has_cycle_undirected(graph, n):
    visited = [False] * n
    
    def dfs(node, parent):
        visited[node] = True
        
        for neighbor in graph[node]:
            if not visited[neighbor]:
                if dfs(neighbor, node):
                    return True
            elif neighbor != parent:
                return True  # Visited but not parent = cycle
        
        return False
    
    for i in range(n):
        if not visited[i]:
            if dfs(i, -1):
                return True
    
    return False
```

**Time Complexity**: O(V + E)  
**Space Complexity**: O(V)  
**Use Cases**: Cycle detection, topological sort, pathfinding, component detection

---

### 2. Breadth First Search (BFS)

```python
from collections import deque

# Basic BFS
def bfs(graph, start):
    visited = set([start])
    queue = deque([start])
    
    while queue:
        node = queue.popleft()
        print(node, end=' ')
        
        for neighbor in graph[node]:
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append(neighbor)
    
    return visited

# BFS with level tracking
def bfs_level_order(graph, start):
    visited = set([start])
    queue = deque([(start, 0)])  # (node, level)
    levels = {}
    
    while queue:
        node, level = queue.popleft()
        
        if level not in levels:
            levels[level] = []
        levels[level].append(node)
        
        for neighbor in graph[node]:
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append((neighbor, level + 1))
    
    return levels

# BFS shortest path
def shortest_path_bfs(graph, start, end):
    visited = set([start])
    queue = deque([(start, [start])])
    
    while queue:
        node, path = queue.popleft()
        
        if node == end:
            return path
        
        for neighbor in graph[node]:
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append((neighbor, path + [neighbor]))
    
    return None

# BFS for bipartite check
def is_bipartite(graph, n):
    color = [-1] * n
    
    for start in range(n):
        if color[start] == -1:
            queue = deque([start])
            color[start] = 0
            
            while queue:
                node = queue.popleft()
                
                for neighbor in graph[node]:
                    if color[neighbor] == -1:
                        color[neighbor] = 1 - color[node]
                        queue.append(neighbor)
                    elif color[neighbor] == color[node]:
                        return False
    
    return True
```

**Time Complexity**: O(V + E)  
**Space Complexity**: O(V)  
**Use Cases**: Shortest path (unweighted), level-order traversal, bipartite check

---

## Shortest Path Algorithms

### 1. Dijkstra's Algorithm (Non-negative weights)

```python
import heapq

def dijkstra(graph, start):
    # graph[node] = [(neighbor, weight), ...]
    distances = {node: float('inf') for node in graph}
    distances[start] = 0
    
    pq = [(0, start)]  # (distance, node)
    visited = set()
    
    while pq:
        curr_dist, node = heapq.heappop(pq)
        
        if node in visited:
            continue
        
        visited.add(node)
        
        for neighbor, weight in graph[node]:
            distance = curr_dist + weight
            
            if distance < distances[neighbor]:
                distances[neighbor] = distance
                heapq.heappush(pq, (distance, neighbor))
    
    return distances

# With path reconstruction
def dijkstra_with_path(graph, start, end):
    distances = {node: float('inf') for node in graph}
    distances[start] = 0
    parent = {node: None for node in graph}
    
    pq = [(0, start)]
    visited = set()
    
    while pq:
        curr_dist, node = heapq.heappop(pq)
        
        if node == end:
            break
        
        if node in visited:
            continue
        
        visited.add(node)
        
        for neighbor, weight in graph[node]:
            distance = curr_dist + weight
            
            if distance < distances[neighbor]:
                distances[neighbor] = distance
                parent[neighbor] = node
                heapq.heappush(pq, (distance, neighbor))
    
    # Reconstruct path
    path = []
    current = end
    while current is not None:
        path.append(current)
        current = parent[current]
    
    return distances[end], path[::-1]
```

**Time Complexity**: O((V + E) log V)  
**Space Complexity**: O(V)

---

### 2. Bellman-Ford Algorithm (Handles negative weights)

```python
def bellman_ford(edges, n, start):
    # edges = [(u, v, weight), ...]
    distances = [float('inf')] * n
    distances[start] = 0
    
    # Relax edges V-1 times
    for _ in range(n - 1):
        for u, v, weight in edges:
            if distances[u] != float('inf'):
                if distances[u] + weight < distances[v]:
                    distances[v] = distances[u] + weight
    
    # Check for negative cycles
    for u, v, weight in edges:
        if distances[u] != float('inf'):
            if distances[u] + weight < distances[v]:
                return None  # Negative cycle detected
    
    return distances
```

**Time Complexity**: O(V × E)  
**Space Complexity**: O(V)

---

### 3. Floyd-Warshall (All-pairs shortest path)

```python
def floyd_warshall(graph, n):
    # Initialize distance matrix
    dist = [[float('inf')] * n for _ in range(n)]
    
    # Distance to self is 0
    for i in range(n):
        dist[i][i] = 0
    
    # Fill initial distances
    for u in range(n):
        for v, weight in graph[u]:
            dist[u][v] = weight
    
    # Floyd-Warshall
    for k in range(n):
        for i in range(n):
            for j in range(n):
                if dist[i][k] + dist[k][j] < dist[i][j]:
                    dist[i][j] = dist[i][k] + dist[k][j]
    
    return dist
```

**Time Complexity**: O(V³)  
**Space Complexity**: O(V²)

---

## Minimum Spanning Tree

### 1. Kruskal's Algorithm (Union-Find)

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

def kruskal(n, edges):
    # edges = [(u, v, weight), ...]
    edges.sort(key=lambda x: x[2])
    
    uf = UnionFind(n)
    mst = []
    total_weight = 0
    
    for u, v, weight in edges:
        if uf.union(u, v):
            mst.append((u, v, weight))
            total_weight += weight
            
            if len(mst) == n - 1:
                break
    
    return mst, total_weight
```

**Time Complexity**: O(E log E)  
**Space Complexity**: O(V)

---

### 2. Prim's Algorithm

```python
def prim(graph, n):
    # graph[node] = [(neighbor, weight), ...]
    visited = set()
    mst = []
    total_weight = 0
    
    # Start from node 0
    pq = [(0, 0, -1)]  # (weight, node, parent)
    
    while pq and len(visited) < n:
        weight, node, parent = heapq.heappop(pq)
        
        if node in visited:
            continue
        
        visited.add(node)
        
        if parent != -1:
            mst.append((parent, node, weight))
            total_weight += weight
        
        for neighbor, edge_weight in graph[node]:
            if neighbor not in visited:
                heapq.heappush(pq, (edge_weight, neighbor, node))
    
    return mst, total_weight
```

**Time Complexity**: O((V + E) log V)  
**Space Complexity**: O(V)

---

## Advanced Patterns

### 1. Topological Sort

```python
# DFS-based
def topological_sort_dfs(graph, n):
    visited = [False] * n
    stack = []
    
    def dfs(node):
        visited[node] = True
        
        for neighbor in graph[node]:
            if not visited[neighbor]:
                dfs(neighbor)
        
        stack.append(node)
    
    for i in range(n):
        if not visited[i]:
            dfs(i)
    
    return stack[::-1]

# Kahn's Algorithm (BFS-based)
def topological_sort_kahn(graph, n):
    in_degree = [0] * n
    
    for node in graph:
        for neighbor in graph[node]:
            in_degree[neighbor] += 1
    
    queue = deque([i for i in range(n) if in_degree[i] == 0])
    result = []
    
    while queue:
        node = queue.popleft()
        result.append(node)
        
        for neighbor in graph[node]:
            in_degree[neighbor] -= 1
            if in_degree[neighbor] == 0:
                queue.append(neighbor)
    
    return result if len(result) == n else []  # Empty if cycle
```

### 2. Strongly Connected Components (Kosaraju's)

```python
def kosaraju_scc(graph, n):
    # Step 1: Get finish times using DFS
    visited = [False] * n
    stack = []
    
    def dfs1(node):
        visited[node] = True
        for neighbor in graph[node]:
            if not visited[neighbor]:
                dfs1(neighbor)
        stack.append(node)
    
    for i in range(n):
        if not visited[i]:
            dfs1(i)
    
    # Step 2: Create transpose graph
    transpose = defaultdict(list)
    for node in graph:
        for neighbor in graph[node]:
            transpose[neighbor].append(node)
    
    # Step 3: DFS on transpose in order of finish times
    visited = [False] * n
    sccs = []
    
    def dfs2(node, scc):
        visited[node] = True
        scc.append(node)
        for neighbor in transpose[node]:
            if not visited[neighbor]:
                dfs2(neighbor, scc)
    
    while stack:
        node = stack.pop()
        if not visited[node]:
            scc = []
            dfs2(node, scc)
            sccs.append(scc)
    
    return sccs
```

### 3. Articulation Points & Bridges

```python
def find_articulation_points_and_bridges(graph, n):
    visited = [False] * n
    disc = [0] * n  # Discovery time
    low = [0] * n   # Lowest discovery time reachable
    parent = [-1] * n
    time = [0]
    
    articulation_points = set()
    bridges = []
    
    def dfs(u):
        children = 0
        visited[u] = True
        disc[u] = low[u] = time[0]
        time[0] += 1
        
        for v in graph[u]:
            if not visited[v]:
                children += 1
                parent[v] = u
                dfs(v)
                
                low[u] = min(low[u], low[v])
                
                # Articulation point conditions
                if parent[u] == -1 and children > 1:
                    articulation_points.add(u)
                if parent[u] != -1 and low[v] >= disc[u]:
                    articulation_points.add(u)
                
                # Bridge condition
                if low[v] > disc[u]:
                    bridges.append((u, v))
            
            elif v != parent[u]:
                low[u] = min(low[u], disc[v])
    
    for i in range(n):
        if not visited[i]:
            dfs(i)
    
    return list(articulation_points), bridges
```

---

## 🎯 Common Graph Problems

### 1. Number of Islands
```python
def numIslands(grid):
    if not grid:
        return 0
    
    rows, cols = len(grid), len(grid[0])
    count = 0
    
    def dfs(r, c):
        if (r < 0 or r >= rows or c < 0 or c >= cols or 
            grid[r][c] == '0'):
            return
        
        grid[r][c] = '0'
        dfs(r + 1, c)
        dfs(r - 1, c)
        dfs(r, c + 1)
        dfs(r, c - 1)
    
    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == '1':
                count += 1
                dfs(r, c)
    
    return count
```

### 2. Clone Graph
```python
def cloneGraph(node):
    if not node:
        return None
    
    clones = {}
    
    def dfs(node):
        if node in clones:
            return clones[node]
        
        clone = Node(node.val)
        clones[node] = clone
        
        for neighbor in node.neighbors:
            clone.neighbors.append(dfs(neighbor))
        
        return clone
    
    return dfs(node)
```

---

## 📊 Complexity Reference

| Algorithm | Time | Space | Use Case |
|-----------|------|-------|----------|
| DFS | O(V+E) | O(V) | Connectivity, cycles |
| BFS | O(V+E) | O(V) | Shortest path (unweighted) |
| Dijkstra | O((V+E)logV) | O(V) | Shortest path (positive) |
| Bellman-Ford | O(VE) | O(V) | Negative weights |
| Floyd-Warshall | O(V³) | O(V²) | All pairs shortest |
| Kruskal | O(ElogE) | O(V) | MST |
| Prim | O((V+E)logV) | O(V) | MST |
| Topological Sort | O(V+E) | O(V) | DAG ordering |

---

## 🎓 Pro Tips

1. **Choose right representation**: Adjacency list for sparse, matrix for dense
2. **Track visited nodes**: Prevents infinite loops
3. **Use appropriate DS**: Queue for BFS, Stack for DFS
4. **Edge cases**: Disconnected graphs, self-loops, cycles
5. **Bidirectional BFS**: For shortest path optimization

---

**Master these graph algorithms to solve complex problems! 🕸️**
