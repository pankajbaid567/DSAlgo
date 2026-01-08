# 🔍 DFS & BFS - Comprehensive Cheatsheet

## 📚 Core Concepts

**DFS (Depth-First Search)**: Explores as far as possible along each branch before backtracking.  
**BFS (Breadth-First Search)**: Explores all neighbors at current depth before moving deeper.

### When to Use
| DFS | BFS |
|-----|-----|
| **Path existence** | **Shortest path** (unweighted) |
| **Connected components** | **Level-order traversal** |
| **Cycle detection** | **Minimum steps** |
| **Topological sort** | **Nearest neighbor** |
| **Memory efficient** (deep graphs) | **Memory efficient** (wide graphs) |

---

## DFS Implementation Patterns

### 1. Recursive DFS (Most Common)
```python
def dfs_recursive(graph, node, visited=None):
    """Standard recursive DFS"""
    if visited is None:
        visited = set()
    
    # Mark as visited
    visited.add(node)
    print(node, end=' ')
    
    # Visit all neighbors
    for neighbor in graph[node]:
        if neighbor not in visited:
            dfs_recursive(graph, neighbor, visited)
    
    return visited

# Usage
graph = {
    0: [1, 2],
    1: [0, 3, 4],
    2: [0, 5],
    3: [1],
    4: [1],
    5: [2]
}
dfs_recursive(graph, 0)  # Output: 0 1 3 4 2 5
```

### 2. Iterative DFS (Using Stack)
```python
def dfs_iterative(graph, start):
    """Iterative DFS using stack"""
    visited = set()
    stack = [start]
    
    while stack:
        node = stack.pop()
        
        if node not in visited:
            visited.add(node)
            print(node, end=' ')
            
            # Add neighbors in reverse order
            # (to match recursive DFS order)
            for neighbor in reversed(graph[node]):
                if neighbor not in visited:
                    stack.append(neighbor)
    
    return visited
```

### 3. DFS with Path Tracking
```python
def dfs_find_path(graph, start, end, path=None):
    """Find path from start to end"""
    if path is None:
        path = []
    
    path = path + [start]
    
    if start == end:
        return path
    
    for neighbor in graph[start]:
        if neighbor not in path:  # Avoid cycles
            new_path = dfs_find_path(graph, neighbor, end, path)
            if new_path:
                return new_path
    
    return None
```

### 4. DFS with Backtracking
```python
def dfs_all_paths(graph, start, end, path=None, all_paths=None):
    """Find all paths from start to end"""
    if path is None:
        path = []
    if all_paths is None:
        all_paths = []
    
    path = path + [start]
    
    if start == end:
        all_paths.append(path)
        return all_paths
    
    for neighbor in graph[start]:
        if neighbor not in path:
            dfs_all_paths(graph, neighbor, end, path, all_paths)
    
    return all_paths
```

---

## BFS Implementation Patterns

### 1. Standard BFS (Using Queue)
```python
from collections import deque

def bfs(graph, start):
    """Standard BFS"""
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

# Usage
bfs(graph, 0)  # Output: 0 1 2 3 4 5
```

### 2. BFS with Level Tracking
```python
def bfs_with_levels(graph, start):
    """BFS tracking depth/level of each node"""
    visited = {start: 0}  # node -> level
    queue = deque([(start, 0)])
    
    while queue:
        node, level = queue.popleft()
        print(f"Node {node} at level {level}")
        
        for neighbor in graph[node]:
            if neighbor not in visited:
                visited[neighbor] = level + 1
                queue.append((neighbor, level + 1))
    
    return visited
```

### 3. BFS Shortest Path
```python
def bfs_shortest_path(graph, start, end):
    """Find shortest path using BFS"""
    if start == end:
        return [start]
    
    visited = {start}
    queue = deque([[start]])
    
    while queue:
        path = queue.popleft()
        node = path[-1]
        
        for neighbor in graph[node]:
            if neighbor not in visited:
                new_path = path + [neighbor]
                
                if neighbor == end:
                    return new_path
                
                visited.add(neighbor)
                queue.append(new_path)
    
    return None
```

### 4. Multi-Source BFS
```python
def multi_source_bfs(graph, sources):
    """BFS from multiple starting points"""
    visited = set(sources)
    queue = deque(sources)
    level = 0
    
    while queue:
        level_size = len(queue)
        
        for _ in range(level_size):
            node = queue.popleft()
            
            for neighbor in graph[node]:
                if neighbor not in visited:
                    visited.add(neighbor)
                    queue.append(neighbor)
        
        level += 1
    
    return level - 1  # Return max distance
```

---

## Pattern 1: Graph Traversal

### Connected Components
```python
def count_components(n, edges):
    """Count connected components in undirected graph"""
    # Build adjacency list
    graph = [[] for _ in range(n)]
    for u, v in edges:
        graph[u].append(v)
        graph[v].append(u)
    
    visited = set()
    count = 0
    
    def dfs(node):
        visited.add(node)
        for neighbor in graph[node]:
            if neighbor not in visited:
                dfs(neighbor)
    
    for node in range(n):
        if node not in visited:
            dfs(node)
            count += 1
    
    return count
```

### Number of Islands (2D Grid DFS)
```python
def num_islands(grid):
    """Count islands in 2D grid"""
    if not grid:
        return 0
    
    rows, cols = len(grid), len(grid[0])
    count = 0
    
    def dfs(r, c):
        # Out of bounds or water
        if (r < 0 or r >= rows or c < 0 or c >= cols or 
            grid[r][c] == '0'):
            return
        
        # Mark as visited
        grid[r][c] = '0'
        
        # Explore 4 directions
        dfs(r + 1, c)
        dfs(r - 1, c)
        dfs(r, c + 1)
        dfs(r, c - 1)
    
    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == '1':
                dfs(r, c)
                count += 1
    
    return count
```

### Clone Graph
```python
class Node:
    def __init__(self, val=0, neighbors=None):
        self.val = val
        self.neighbors = neighbors if neighbors else []

def clone_graph(node):
    """Deep copy of graph"""
    if not node:
        return None
    
    clones = {}  # original -> clone
    
    def dfs(node):
        if node in clones:
            return clones[node]
        
        # Create clone
        clone = Node(node.val)
        clones[node] = clone
        
        # Clone neighbors
        for neighbor in node.neighbors:
            clone.neighbors.append(dfs(neighbor))
        
        return clone
    
    return dfs(node)
```

---

## Pattern 2: Shortest Path (BFS)

### Shortest Path in Binary Matrix
```python
def shortest_path_binary_matrix(grid):
    """Find shortest clear path in n×n grid"""
    n = len(grid)
    
    if grid[0][0] == 1 or grid[n-1][n-1] == 1:
        return -1
    
    directions = [(-1,-1), (-1,0), (-1,1), (0,-1), 
                  (0,1), (1,-1), (1,0), (1,1)]
    
    queue = deque([(0, 0, 1)])  # (row, col, distance)
    visited = {(0, 0)}
    
    while queue:
        r, c, dist = queue.popleft()
        
        if r == n - 1 and c == n - 1:
            return dist
        
        for dr, dc in directions:
            nr, nc = r + dr, c + dc
            
            if (0 <= nr < n and 0 <= nc < n and 
                grid[nr][nc] == 0 and (nr, nc) not in visited):
                visited.add((nr, nc))
                queue.append((nr, nc, dist + 1))
    
    return -1
```

### Word Ladder
```python
def ladder_length(begin_word, end_word, word_list):
    """Minimum transformations from begin to end word"""
    if end_word not in word_list:
        return 0
    
    word_set = set(word_list)
    queue = deque([(begin_word, 1)])
    
    while queue:
        word, steps = queue.popleft()
        
        if word == end_word:
            return steps
        
        # Try all one-letter changes
        for i in range(len(word)):
            for c in 'abcdefghijklmnopqrstuvwxyz':
                next_word = word[:i] + c + word[i+1:]
                
                if next_word in word_set:
                    word_set.remove(next_word)
                    queue.append((next_word, steps + 1))
    
    return 0
```

### Rotting Oranges
```python
def oranges_rotting(grid):
    """Minimum time until all oranges rot"""
    rows, cols = len(grid), len(grid[0])
    queue = deque()
    fresh_count = 0
    
    # Find all rotten oranges and count fresh
    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == 2:
                queue.append((r, c, 0))
            elif grid[r][c] == 1:
                fresh_count += 1
    
    if fresh_count == 0:
        return 0
    
    directions = [(0,1), (0,-1), (1,0), (-1,0)]
    max_time = 0
    
    while queue:
        r, c, time = queue.popleft()
        max_time = max(max_time, time)
        
        for dr, dc in directions:
            nr, nc = r + dr, c + dc
            
            if (0 <= nr < rows and 0 <= nc < cols and 
                grid[nr][nc] == 1):
                grid[nr][nc] = 2
                fresh_count -= 1
                queue.append((nr, nc, time + 1))
    
    return max_time if fresh_count == 0 else -1
```

---

## Pattern 3: Cycle Detection

### Detect Cycle in Undirected Graph (DFS)
```python
def has_cycle_undirected(n, edges):
    """Detect cycle in undirected graph"""
    graph = [[] for _ in range(n)]
    for u, v in edges:
        graph[u].append(v)
        graph[v].append(u)
    
    visited = set()
    
    def dfs(node, parent):
        visited.add(node)
        
        for neighbor in graph[node]:
            if neighbor not in visited:
                if dfs(neighbor, node):
                    return True
            elif neighbor != parent:
                return True  # Cycle found
        
        return False
    
    for node in range(n):
        if node not in visited:
            if dfs(node, -1):
                return True
    
    return False
```

### Detect Cycle in Directed Graph (DFS)
```python
def has_cycle_directed(n, edges):
    """Detect cycle in directed graph"""
    graph = [[] for _ in range(n)]
    for u, v in edges:
        graph[u].append(v)
    
    WHITE, GRAY, BLACK = 0, 1, 2
    color = [WHITE] * n
    
    def dfs(node):
        color[node] = GRAY
        
        for neighbor in graph[node]:
            if color[neighbor] == GRAY:
                return True  # Back edge = cycle
            if color[neighbor] == WHITE:
                if dfs(neighbor):
                    return True
        
        color[node] = BLACK
        return False
    
    for node in range(n):
        if color[node] == WHITE:
            if dfs(node):
                return True
    
    return False
```

---

## Pattern 4: Topological Sort

### Kahn's Algorithm (BFS)
```python
def topological_sort_bfs(n, edges):
    """Topological sort using BFS"""
    graph = [[] for _ in range(n)]
    in_degree = [0] * n
    
    # Build graph and calculate in-degrees
    for u, v in edges:
        graph[u].append(v)
        in_degree[v] += 1
    
    # Queue with nodes having 0 in-degree
    queue = deque([i for i in range(n) if in_degree[i] == 0])
    result = []
    
    while queue:
        node = queue.popleft()
        result.append(node)
        
        for neighbor in graph[node]:
            in_degree[neighbor] -= 1
            if in_degree[neighbor] == 0:
                queue.append(neighbor)
    
    # Check if all nodes processed (no cycle)
    return result if len(result) == n else []
```

### DFS-based Topological Sort
```python
def topological_sort_dfs(n, edges):
    """Topological sort using DFS"""
    graph = [[] for _ in range(n)]
    for u, v in edges:
        graph[u].append(v)
    
    visited = set()
    result = []
    
    def dfs(node):
        visited.add(node)
        
        for neighbor in graph[node]:
            if neighbor not in visited:
                dfs(neighbor)
        
        result.append(node)
    
    for node in range(n):
        if node not in visited:
            dfs(node)
    
    return result[::-1]  # Reverse postorder
```

### Course Schedule
```python
def can_finish(num_courses, prerequisites):
    """Check if all courses can be finished"""
    graph = [[] for _ in range(num_courses)]
    in_degree = [0] * num_courses
    
    for course, prereq in prerequisites:
        graph[prereq].append(course)
        in_degree[course] += 1
    
    queue = deque([i for i in range(num_courses) if in_degree[i] == 0])
    count = 0
    
    while queue:
        node = queue.popleft()
        count += 1
        
        for neighbor in graph[node]:
            in_degree[neighbor] -= 1
            if in_degree[neighbor] == 0:
                queue.append(neighbor)
    
    return count == num_courses
```

---

## Pattern 5: Level Order Traversal

### Binary Tree Level Order
```python
def level_order(root):
    """Level-order traversal of binary tree"""
    if not root:
        return []
    
    result = []
    queue = deque([root])
    
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

### Zigzag Level Order
```python
def zigzag_level_order(root):
    """Zigzag level-order traversal"""
    if not root:
        return []
    
    result = []
    queue = deque([root])
    left_to_right = True
    
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
        
        if not left_to_right:
            level.reverse()
        
        result.append(level)
        left_to_right = not left_to_right
    
    return result
```

### Right Side View
```python
def right_side_view(root):
    """Right side view of binary tree"""
    if not root:
        return []
    
    result = []
    queue = deque([root])
    
    while queue:
        level_size = len(queue)
        
        for i in range(level_size):
            node = queue.popleft()
            
            # Last node in level
            if i == level_size - 1:
                result.append(node.val)
            
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
    
    return result
```

---

## 🎨 DFS vs BFS Visualization

### Graph
```
    0
   / \
  1   2
 / \   \
3   4   5
```

### DFS (Stack/Recursion)
```
Visit order: 0 → 1 → 3 → 4 → 2 → 5

Stack visualization:
[0]
[0, 1]
[0, 1, 3]
[0, 1, 4]
[0, 2]
[0, 2, 5]
```

### BFS (Queue)
```
Visit order: 0 → 1 → 2 → 3 → 4 → 5

Queue visualization:
[0]
[1, 2]
[2, 3, 4]
[3, 4, 5]
[4, 5]
[5]
```

---

## ⏱️ Complexity Analysis

| Algorithm | Time | Space |
|-----------|------|-------|
| DFS | O(V + E) | O(V) (stack) |
| BFS | O(V + E) | O(V) (queue) |
| Topological Sort | O(V + E) | O(V) |
| Shortest Path (BFS) | O(V + E) | O(V) |

Where:
- V = number of vertices
- E = number of edges

---

## 🎯 Must-Know Problems

### Easy
- [104. Maximum Depth of Binary Tree](https://leetcode.com/problems/maximum-depth-of-binary-tree/)
- [200. Number of Islands](https://leetcode.com/problems/number-of-islands/)
- [733. Flood Fill](https://leetcode.com/problems/flood-fill/)
- [110. Balanced Binary Tree](https://leetcode.com/problems/balanced-binary-tree/)

### Medium
- [102. Binary Tree Level Order Traversal](https://leetcode.com/problems/binary-tree-level-order-traversal/)
- [133. Clone Graph](https://leetcode.com/problems/clone-graph/)
- [207. Course Schedule](https://leetcode.com/problems/course-schedule/)
- [127. Word Ladder](https://leetcode.com/problems/word-ladder/)
- [994. Rotting Oranges](https://leetcode.com/problems/rotting-oranges/)
- [1091. Shortest Path in Binary Matrix](https://leetcode.com/problems/shortest-path-in-binary-matrix/)

### Hard
- [212. Word Search II](https://leetcode.com/problems/word-search-ii/)
- [329. Longest Increasing Path in Matrix](https://leetcode.com/problems/longest-increasing-path-in-a-matrix/)
- [126. Word Ladder II](https://leetcode.com/problems/word-ladder-ii/)

---

## 💡 Pro Tips

1. **Use BFS for shortest path**: In unweighted graphs
2. **Use DFS for connectivity**: Components, cycles, paths
3. **Topological sort = DFS postorder reversed**: Or BFS with in-degrees
4. **Multi-source BFS**: Add all sources to queue initially
5. **Grid = implicit graph**: Use (row, col) as nodes
6. **Mark visited early**: Add to visited when adding to queue (BFS)
7. **Watch for directed vs undirected**: Different cycle detection logic

---

## 🔥 Common Patterns

```python
# Pattern 1: Grid DFS
def dfs_grid(grid, r, c, visited):
    if (r < 0 or r >= len(grid) or c < 0 or c >= len(grid[0]) or
        (r, c) in visited or grid[r][c] == 0):
        return
    
    visited.add((r, c))
    for dr, dc in [(0,1), (0,-1), (1,0), (-1,0)]:
        dfs_grid(grid, r + dr, c + dc, visited)

# Pattern 2: BFS with levels
def bfs_levels(start):
    queue = deque([start])
    level = 0
    
    while queue:
        level_size = len(queue)
        for _ in range(level_size):
            node = queue.popleft()
            # Process node
            for neighbor in get_neighbors(node):
                queue.append(neighbor)
        level += 1

# Pattern 3: Cycle detection (directed)
def has_cycle(node, visited, rec_stack):
    visited.add(node)
    rec_stack.add(node)
    
    for neighbor in graph[node]:
        if neighbor not in visited:
            if has_cycle(neighbor, visited, rec_stack):
                return True
        elif neighbor in rec_stack:
            return True
    
    rec_stack.remove(node)
    return False
```

---

**Master DFS & BFS - the foundation of graph algorithms! 🔍**
