# DP on DAG (Directed Acyclic Graphs)

## Introduction
Many Dynamic Programming problems are implicitly finding paths on a Directed Acyclic Graph (DAG). However, sometimes the graph is given explicitly. Since the graph is acyclic, we can process nodes in topological order or use memoized DFS to find the shortest path, longest path, or count paths.

## Core Concepts
1. **Topological Sort**: DP inherently relies on resolving subproblems before solving the main problem. On a DAG, this corresponds directly to topological sorting.
2. **Memoized DFS**: Starting from a node, recursively asking for the answer from its neighbors and caching the result.

*Note: If the graph has cycles, DP generally cannot be used directly for shortest/longest paths (Dijkstra or Bellman-Ford are needed instead).*

## Example Standard Template (Longest Path in DAG)
```python
# graph is represented as an adjacency list: graph[u] = [(v, weight), ...]
memo = {}
def longest_path(u):
    if u in memo:
        return memo[u]
        
    ans = 0 # base case, or could be initialized to float('-inf') depending on problem
    for v, weight in graph[u]:
        ans = max(ans, weight + longest_path(v))
        
    memo[u] = ans
    return ans
```

## Top LeetCode Questions
- [787. Cheapest Flights Within K Stops](https://leetcode.com/problems/cheapest-flights-within-k-stops/) (Medium - DP/Bellman Ford)
- [1976. Number of Ways to Arrive at Destination](https://leetcode.com/problems/number-of-ways-to-arrive-at-destination/) (Medium - DP + Dijkstra)
- [329. Longest Increasing Path in a Matrix](https://leetcode.com/problems/longest-increasing-path-in-a-matrix/) (Hard - DAG over grid)
- [1857. Largest Color Value in a Directed Graph](https://leetcode.com/problems/largest-color-value-in-a-directed-graph/) (Hard)
