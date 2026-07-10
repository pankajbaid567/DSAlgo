# DP with Bitmasking

## Introduction
Dynamic Programming with Bitmasking is an advanced pattern typically used when the problem constraints are very small (e.g., $N \le 20$). It is extremely popular in Quant interviews and Hard level FAANG questions. 
We use an integer to represent a subset of items, where the $i$-th bit is `1` if the $i$-th item is included, and `0` otherwise.

## Common Problem Types
1. **Traveling Salesman Problem (TSP) / Hamiltonian Path**: Visiting all nodes in a graph with minimum cost. State is usually `(mask, last_visited_node)`.
2. **Assignment Problems**: Assigning $N$ tasks to $N$ people with minimum/maximum cost. State is `(mask)`.
3. **Partitioning into Subsets**: E.g., Distributing items to $K$ people.

## Identification
- Look for small constraints: $N \le 15$ to $20$ (since $2^{20} \approx 10^6$, which easily fits in time limits).
- The problem asks to select/assign a subset of elements optimally.

## Example Standard Template (TSP variation)
```python
# Finding minimum cost to visit all nodes
# mask represents visited nodes, u is current node
memo = {}
def dfs(mask, u):
    if mask == (1 << n) - 1: # all nodes visited
        return 0
    if (mask, u) in memo:
        return memo[(mask, u)]
        
    ans = float('inf')
    for v in range(n):
        if not (mask & (1 << v)): # if v is not visited
            ans = min(ans, cost[u][v] + dfs(mask | (1 << v), v))
            
    memo[(mask, u)] = ans
    return ans
```

## Top LeetCode Questions
- [847. Shortest Path Visiting All Nodes](https://leetcode.com/problems/shortest-path-visiting-all-nodes/) (Hard)
- [1879. Minimum XOR Sum of Two Arrays](https://leetcode.com/problems/minimum-xor-sum-of-two-arrays/) (Hard)
- [1947. Maximum Compatibility Score Sum](https://leetcode.com/problems/maximum-compatibility-score-sum/) (Medium)
- [1349. Maximum Students Taking Exam](https://leetcode.com/problems/maximum-students-taking-exam/) (Hard) - *Bitmask on Grid*
