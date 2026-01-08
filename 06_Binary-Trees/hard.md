# 🔥 Binary Trees - Hard Problems Collection

A curated collection of challenging Binary Tree problems with complete solutions, multiple approaches, and detailed explanations.

---

## 📚 Table of Contents

1. [Serialize and Deserialize Binary Tree](#problem-1-serialize-and-deserialize-binary-tree)
2. [Binary Tree Maximum Path Sum](#problem-2-binary-tree-maximum-path-sum)
3. [Recover Binary Search Tree](#problem-3-recover-binary-search-tree)
4. [Binary Tree Cameras](#problem-4-binary-tree-cameras)
5. [Distribute Coins in Binary Tree](#problem-5-distribute-coins-in-binary-tree)
6. [Vertical Order Traversal](#problem-6-vertical-order-traversal)
7. [Count Complete Tree Nodes](#problem-7-count-complete-tree-nodes)
8. [Pattern Summary](#-pattern-summary)

---

## Problem 1: Serialize and Deserialize Binary Tree

**LeetCode 297 - Hard**

### Problem Statement
Design an algorithm to serialize and deserialize a binary tree. Serialization converts tree to string; deserialization reconstructs tree from string.

```
Input: root = [1,2,3,null,null,4,5]
Output: "1,2,null,null,3,4,null,null,5,null,null"
```

### 🎯 Intuition
Multiple approaches:
1. **Pre-order** traversal with null markers
2. **Level-order (BFS)** with null markers
3. **Parentheses** notation

Pre-order is most intuitive: visit node, serialize left, serialize right.

### 📊 Visual Representation

```
Tree:
       1
      / \
     2   3
        / \
       4   5

Pre-order: 1,2,null,null,3,4,null,null,5,null,null

How it works:
  Visit 1 → "1"
    Visit 2 → "1,2"
      Visit null → "1,2,null"
      Visit null → "1,2,null,null"
    Back to 1
    Visit 3 → "1,2,null,null,3"
      Visit 4 → "1,2,null,null,3,4"
        Visit null → "1,2,null,null,3,4,null"
        Visit null → "1,2,null,null,3,4,null,null"
      Visit 5 → "1,2,null,null,3,4,null,null,5"
        Visit null → "1,2,null,null,3,4,null,null,5,null"
        Visit null → "1,2,null,null,3,4,null,null,5,null,null"
```

### Approach 1: Pre-order Traversal (Optimal)

```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

class Codec:
    """
    Serialize/deserialize using pre-order traversal.
    
    Logic:
    - Serialize: Pre-order with null markers
    - Deserialize: Recursively build from pre-order sequence
    
    Time: O(n) for both
    Space: O(n)
    """
    
    def serialize(self, root):
        """Encode tree to string."""
        def preorder(node):
            if not node:
                return "null"
            return f"{node.val},{preorder(node.left)},{preorder(node.right)}"
        
        return preorder(root)
    
    def deserialize(self, data):
        """Decode string to tree."""
        def build():
            val = next(values)
            if val == "null":
                return None
            
            node = TreeNode(int(val))
            node.left = build()
            node.right = build()
            return node
        
        values = iter(data.split(','))
        return build()

# Example usage
codec = Codec()
root = TreeNode(1, TreeNode(2), TreeNode(3, TreeNode(4), TreeNode(5)))
serialized = codec.serialize(root)
print(serialized)  # "1,2,null,null,3,4,null,null,5,null,null"
deserialized = codec.deserialize(serialized)
```

### Approach 2: Level-order (BFS)

```python
from collections import deque

class Codec_BFS:
    """
    Serialize/deserialize using level-order traversal.
    
    Time: O(n)
    Space: O(n)
    """
    
    def serialize(self, root):
        """BFS serialization."""
        if not root:
            return ""
        
        result = []
        queue = deque([root])
        
        while queue:
            node = queue.popleft()
            
            if node:
                result.append(str(node.val))
                queue.append(node.left)
                queue.append(node.right)
            else:
                result.append("null")
        
        return ",".join(result)
    
    def deserialize(self, data):
        """BFS deserialization."""
        if not data:
            return None
        
        values = data.split(',')
        root = TreeNode(int(values[0]))
        queue = deque([root])
        i = 1
        
        while queue and i < len(values):
            node = queue.popleft()
            
            # Left child
            if values[i] != "null":
                node.left = TreeNode(int(values[i]))
                queue.append(node.left)
            i += 1
            
            # Right child
            if i < len(values) and values[i] != "null":
                node.right = TreeNode(int(values[i]))
                queue.append(node.right)
            i += 1
        
        return root
```

### Approach 3: Parentheses Notation

```python
class Codec_Parens:
    """
    Use parentheses to denote structure.
    Example: 1(2)(3(4)(5))
    
    Time: O(n)
    Space: O(n)
    """
    
    def serialize(self, root):
        """Serialize with parentheses."""
        if not root:
            return ""
        
        left = f"({self.serialize(root.left)})" if root.left else ""
        right = f"({self.serialize(root.right)})" if root.right or root.left else ""
        
        return f"{root.val}{left}{right}"
    
    def deserialize(self, data):
        """Deserialize from parentheses notation."""
        if not data:
            return None
        
        def build(s, start):
            if start >= len(s):
                return None, start
            
            # Parse number
            end = start
            while end < len(s) and (s[end].isdigit() or s[end] == '-'):
                end += 1
            
            if start == end:
                return None, start
            
            node = TreeNode(int(s[start:end]))
            pos = end
            
            # Parse left child
            if pos < len(s) and s[pos] == '(':
                pos += 1
                node.left, pos = build(s, pos)
                pos += 1  # Skip ')'
            
            # Parse right child
            if pos < len(s) and s[pos] == '(':
                pos += 1
                node.right, pos = build(s, pos)
                pos += 1  # Skip ')'
            
            return node, pos
        
        root, _ = build(data, 0)
        return root
```

### 🔍 Dry Run

```
Tree:     1
         / \
        2   3

Pre-order Serialize:

serialize(1):
  val = "1"
  left = serialize(2):
    val = "2"
    left = serialize(null) = "null"
    right = serialize(null) = "null"
    return "2,null,null"
  right = serialize(3):
    val = "3"
    left = "null"
    right = "null"
    return "3,null,null"
  return "1,2,null,null,3,null,null"

Deserialize "1,2,null,null,3,null,null":

values = iter(['1','2','null','null','3','null','null'])

build():
  val = '1' → node(1)
  node.left = build():
    val = '2' → node(2)
    node.left = build(): val='null' → None
    node.right = build(): val='null' → None
    return node(2)
  node.right = build():
    val = '3' → node(3)
    node.left = build(): val='null' → None
    node.right = build(): val='null' → None
    return node(3)
  return node(1) with structure restored
```

### ⏱️ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Pre-order | O(n) | O(n) | ⭐ Clean, recursive |
| BFS | O(n) | O(n) | Iterative |
| Parentheses | O(n) | O(n) | Compact notation |

---

## Problem 2: Binary Tree Maximum Path Sum

**LeetCode 124 - Hard**

### Problem Statement
Given binary tree, find the maximum path sum. Path may start and end at any node.

```
Input: root = [-10,9,20,null,null,15,7]
Output: 42
Explanation: Path 15 → 20 → 7 has sum 42
```

### 🎯 Intuition
Use **post-order traversal** with global maximum:
- For each node, consider 4 options:
  1. Node only
  2. Node + left path
  3. Node + right path
  4. Node + left + right (this can't extend upward)

Return maximum gain that can extend upward (options 1-3).
Update global max with all options including node + left + right.

### 📊 Visual Representation

```
Tree:      -10
           /  \
          9   20
             /  \
            15   7

At node 15: max_gain = 15, global_max = 15
At node 7: max_gain = 7, global_max = 15
At node 20:
  left_gain = 15
  right_gain = 7
  Options:
    - Just 20: 20
    - 20 + 15: 35
    - 20 + 7: 27
    - 20 + 15 + 7: 42 ← MAXIMUM (can't extend up)
  Return 35 (20 + 15) to parent
  global_max = 42

At node 9: max_gain = 9, global_max = 42
At node -10:
  left_gain = 9
  right_gain = 35
  Options:
    - Just -10: -10
    - -10 + 9: -1
    - -10 + 35: 25
    - -10 + 9 + 35: 34
  global_max remains 42

Answer: 42
```

### Solution

```python
class Solution:
    """
    Use post-order traversal with global maximum.
    
    Logic:
    - For each node, calculate maximum gain extending upward
    - Update global max considering path through this node
    - Return maximum single-path gain
    
    Time: O(n)
    Space: O(h) recursion stack
    """
    
    def maxPathSum(self, root):
        self.max_sum = float('-inf')
        
        def max_gain(node):
            if not node:
                return 0
            
            # Recurse on children (ignore negative gains)
            left_gain = max(max_gain(node.left), 0)
            right_gain = max(max_gain(node.right), 0)
            
            # Path through this node (can't extend upward)
            path_sum = node.val + left_gain + right_gain
            
            # Update global maximum
            self.max_sum = max(self.max_sum, path_sum)
            
            # Return maximum gain extending upward
            return node.val + max(left_gain, right_gain)
        
        max_gain(root)
        return self.max_sum

# Example usage
root = TreeNode(-10)
root.left = TreeNode(9)
root.right = TreeNode(20, TreeNode(15), TreeNode(7))
sol = Solution()
print(sol.maxPathSum(root))  # Output: 42
```

### Alternative: With Path Tracking

```python
class Solution_WithPath:
    """Track the actual path, not just sum."""
    
    def maxPathSum(self, root):
        self.max_sum = float('-inf')
        self.best_path = []
        
        def max_gain(node):
            if not node:
                return 0, []
            
            left_gain, left_path = max_gain(node.left)
            right_gain, right_path = max_gain(node.right)
            
            # Ignore negative gains
            left_gain = max(left_gain, 0)
            right_gain = max(right_gain, 0)
            
            # Path through this node
            path_sum = node.val + left_gain + right_gain
            
            if path_sum > self.max_sum:
                self.max_sum = path_sum
                # Construct path
                self.best_path = (list(reversed(left_path)) + 
                                  [node.val] + right_path)
            
            # Return path extending upward
            if left_gain > right_gain:
                return node.val + left_gain, [node.val] + left_path
            else:
                return node.val + right_gain, [node.val] + right_path
        
        max_gain(root)
        return self.max_sum, self.best_path
```

### 🔍 Dry Run

```
Tree:   1
       / \
      2   3

max_gain(2):
  left_gain = 0, right_gain = 0
  path_sum = 2 + 0 + 0 = 2
  max_sum = 2
  return 2

max_gain(3):
  left_gain = 0, right_gain = 0
  path_sum = 3 + 0 + 0 = 3
  max_sum = 3
  return 3

max_gain(1):
  left_gain = 2, right_gain = 3
  path_sum = 1 + 2 + 3 = 6
  max_sum = 6
  return 1 + max(2, 3) = 4

Final answer: 6
```

### ⏱️ Complexity Analysis

| Metric | Complexity | Notes |
|--------|------------|-------|
| Time | O(n) | Visit each node once |
| Space | O(h) | Recursion stack height |

---

## Problem 3: Recover Binary Search Tree

**LeetCode 99 - Hard**

### Problem Statement
Two nodes of a BST are swapped by mistake. Recover the tree without changing its structure.

```
Input: root = [3,1,4,null,null,2]
Output: [2,1,4,null,null,3]
Explanation: 3 and 2 are swapped
```

### 🎯 Intuition
**In-order traversal of BST gives sorted sequence!**

If two nodes swapped:
- **Case 1**: Adjacent nodes → one violation
  - Example: [1, 3, 2, 4] → swap 3 and 2
- **Case 2**: Non-adjacent → two violations
  - Example: [1, 4, 3, 2, 5] → swap 4 and 2

**Algorithm:**
1. In-order traverse, track previous node
2. Find violations where `prev.val > current.val`
3. First violation: mark `prev` as first swapped node
4. Second violation (or if only one): mark `current` as second swapped node
5. Swap their values

### 📊 Visual Representation

```
Incorrect BST:
       3
      / \
     1   4
        /
       2

In-order: 1, 3, 2, 4
              ↑  ↑
           violation: 3 > 2

Correct BST:
       2
      / \
     1   4
        /
       3

In-order: 1, 2, 3, 4 ✓

Algorithm:
  prev = None
  Traverse: 1 → prev=1
  Traverse: 3 → prev=3
  Traverse: 2 → VIOLATION! 3 > 2
    first_node = 3
    second_node = 2
  Traverse: 4 → prev=4
  
  Swap values of first_node and second_node
```

### Approach 1: Recursive In-order

```python
class Solution:
    """
    Find swapped nodes using in-order traversal.
    
    Logic:
    - In-order traverse BST
    - Track previous node
    - Find violations: prev.val > curr.val
    - Swap the two identified nodes
    
    Time: O(n)
    Space: O(h) recursion stack
    """
    
    def recoverTree(self, root):
        self.first = None
        self.second = None
        self.prev = None
        
        def inorder(node):
            if not node:
                return
            
            inorder(node.left)
            
            # Check for violation
            if self.prev and self.prev.val > node.val:
                if not self.first:
                    self.first = self.prev
                self.second = node
            
            self.prev = node
            inorder(node.right)
        
        inorder(root)
        
        # Swap values
        self.first.val, self.second.val = self.second.val, self.first.val

# Example usage
root = TreeNode(3, TreeNode(1), TreeNode(4, TreeNode(2)))
sol = Solution()
sol.recoverTree(root)
```

### Approach 2: Morris Traversal (O(1) Space)

```python
class Solution_Morris:
    """
    Use Morris in-order traversal for O(1) space.
    
    Logic:
    - Create threaded binary tree temporarily
    - In-order traverse without recursion/stack
    - Find violations and swap
    
    Time: O(n)
    Space: O(1) ⭐
    """
    
    def recoverTree(self, root):
        first = second = prev = None
        current = root
        
        while current:
            if not current.left:
                # Visit current
                if prev and prev.val > current.val:
                    if not first:
                        first = prev
                    second = current
                prev = current
                current = current.right
            else:
                # Find predecessor
                predecessor = current.left
                while predecessor.right and predecessor.right != current:
                    predecessor = predecessor.right
                
                if not predecessor.right:
                    # Create thread
                    predecessor.right = current
                    current = current.left
                else:
                    # Remove thread, visit current
                    predecessor.right = None
                    if prev and prev.val > current.val:
                        if not first:
                            first = prev
                        second = current
                    prev = current
                    current = current.right
        
        # Swap values
        first.val, second.val = second.val, first.val
```

### Approach 3: Iterative with Stack

```python
class Solution_Iterative:
    """
    Iterative in-order with explicit stack.
    
    Time: O(n)
    Space: O(h)
    """
    
    def recoverTree(self, root):
        stack = []
        first = second = prev = None
        current = root
        
        while stack or current:
            # Go to leftmost
            while current:
                stack.append(current)
                current = current.left
            
            # Visit node
            current = stack.pop()
            
            if prev and prev.val > current.val:
                if not first:
                    first = prev
                second = current
            
            prev = current
            current = current.right
        
        # Swap values
        first.val, second.val = second.val, first.val
```

### 🔍 Dry Run

```
Tree:   1
       / \
      3   2

Correct BST should be:
       2
      / \
     1   3

In-order of incorrect tree: 3, 1, 2

Step 1: Visit 3
  prev = None
  No violation
  prev = 3

Step 2: Visit 1
  prev = 3, current = 1
  VIOLATION: 3 > 1
  first = 3
  second = 1
  prev = 1

Step 3: Visit 2
  prev = 1, current = 2
  No violation (1 < 2)
  prev = 2

Swap: first(3) ↔ second(1)
Result: Tree becomes correct BST
```

### ⏱️ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Recursive | O(n) | O(h) | Clean code |
| Morris | O(n) | O(1) | ⭐ Optimal space |
| Iterative | O(n) | O(h) | Explicit stack |

---

## Problem 4: Binary Tree Cameras

**LeetCode 968 - Hard**

### Problem Statement
Install minimum cameras to monitor all nodes. A camera at node monitors parent, itself, and immediate children.

```
Input: root = [0,0,null,0,0]
Output: 1
```

### 🎯 Intuition
Use **greedy post-order** approach with states:
- **0 (Not Covered)**: Node needs coverage
- **1 (Has Camera)**: Node has camera
- **2 (Covered)**: Node is monitored

**Strategy:**
- Process children first (post-order)
- If any child not covered → place camera here
- If any child has camera → this node is covered
- If all children covered → leave this not covered (parent will handle)

**Key Insight:** Place cameras as high as possible, but don't miss coverage!

### 📊 Visual Representation

```
Tree:         0
             / \
            0   0
           /
          0

Post-order processing:

Leaf 0 (left-left): Return 0 (not covered)

Node 0 (left):
  left child = 0 (not covered)
  Must place camera here!
  Return 1 (has camera)

Node 0 (right):
  No children
  Return 0 (not covered)

Root:
  left child = 1 (has camera) → covered
  right child = 0 (not covered) → need camera!
  Must place camera here!
  Return 1 (has camera)

Total cameras: 2 (at left child and root)

States:
  0 = Not covered (needs monitoring)
  1 = Has camera (monitors parent/children)
  2 = Covered (being monitored)
```

### Solution

```python
class Solution:
    """
    Use greedy post-order with state tracking.
    
    Logic:
    - Process children first (post-order)
    - Place camera if any child not covered
    - Track three states: not covered, has camera, covered
    
    Time: O(n)
    Space: O(h) recursion stack
    """
    
    def minCameraCover(self, root):
        self.cameras = 0
        
        # States:
        # 0 = Not covered
        # 1 = Has camera
        # 2 = Covered (no camera but monitored)
        
        def dfs(node):
            if not node:
                return 2  # Null nodes are considered covered
            
            left = dfs(node.left)
            right = dfs(node.right)
            
            # If any child not covered, place camera here
            if left == 0 or right == 0:
                self.cameras += 1
                return 1
            
            # If any child has camera, this node is covered
            if left == 1 or right == 1:
                return 2
            
            # Both children covered, leave this not covered
            return 0
        
        # If root not covered after DFS, need camera at root
        if dfs(root) == 0:
            self.cameras += 1
        
        return self.cameras

# Example usage
root = TreeNode(0)
root.left = TreeNode(0, TreeNode(0), TreeNode(0))
sol = Solution()
print(sol.minCameraCover(root))  # Output: 1
```

### Alternative: DP Approach

```python
class Solution_DP:
    """
    DP with three states per node.
    
    States:
    - dp[0] = min cameras if this node not covered
    - dp[1] = min cameras if this node has camera
    - dp[2] = min cameras if this node covered but no camera
    
    Time: O(n)
    Space: O(n)
    """
    
    def minCameraCover(self, root):
        def dfs(node):
            if not node:
                # [not_covered, has_camera, covered]
                return [0, float('inf'), 0]
            
            left = dfs(node.left)
            right = dfs(node.right)
            
            # This node not covered
            # Children must have cameras or be covered
            not_covered = left[2] + right[2]
            
            # This node has camera
            # Children can be in any state
            has_camera = 1 + min(left) + min(right)
            
            # This node covered (no camera)
            # At least one child must have camera
            covered = min(
                left[1] + min(right[1], right[2]),
                right[1] + min(left[1], left[2])
            )
            
            return [not_covered, has_camera, covered]
        
        result = dfs(root)
        # Root must be covered or have camera
        return min(result[1], result[2])
```

### 🔍 Dry Run

```
Tree:   0
       / \
      0   0

dfs(left child 0):
  left = 2 (null), right = 2 (null)
  Both null → covered
  left=0 or right=0? NO
  left=1 or right=1? NO
  return 0 (not covered)

dfs(right child 0):
  Similar, return 0 (not covered)

dfs(root):
  left = 0 (not covered), right = 0 (not covered)
  left=0 or right=0? YES
  Place camera! cameras = 1
  return 1 (has camera)

Root returned 1 (has camera), so no additional camera needed
Total cameras: 1
```

### ⏱️ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Greedy | O(n) | O(h) | ⭐ Simple, optimal |
| DP | O(n) | O(n) | More formal |

---

## Problem 5: Distribute Coins in Binary Tree

**LeetCode 979 - Hard**

### Problem Statement
Given binary tree with `n` nodes and `n` coins, each node has `node.val` coins. In one move, we can move one coin from one node to adjacent node. Return minimum moves required so each node has exactly 1 coin.

```
Input: root = [3,0,0]
Output: 2
Explanation: Move 2 coins from root to leaves
```

### 🎯 Intuition
Use **post-order traversal** to calculate excess/deficit:
- For each node: `excess = node.val + left_excess + right_excess - 1`
- Number of moves = sum of absolute excess values
- Positive excess → give away coins
- Negative excess → receive coins

**Key Insight:** Every coin movement between parent-child counts as 1 move. Total moves = sum of |excess| for all edges.

### 📊 Visual Representation

```
Tree:   3
       / \
      0   0

Process bottom-up:

Left child (0):
  val = 0, needs 1 coin
  excess = 0 - 1 = -1
  moves += |−1| = 1

Right child (0):
  val = 0, needs 1 coin  
  excess = 0 - 1 = -1
  moves += |−1| = 1

Root (3):
  val = 3
  left_excess = -1 (needs 1)
  right_excess = -1 (needs 1)
  Keep 1 for itself
  excess = 3 + (-1) + (-1) - 1 = 0
  All balanced!

Total moves: 1 + 1 = 2

Visualization of moves:
     3              1              1
    / \    →       / \      →     / \
   0   0          1   0          1   1
   
  Move 1: 3→left   Move 2: 1→right
```

### Solution

```python
class Solution:
    """
    Calculate excess coins using post-order traversal.
    
    Logic:
    - For each node: excess = val + left_excess + right_excess - 1
    - Moves needed = |excess| (coins moving up or down)
    - Sum all moves
    
    Time: O(n)
    Space: O(h) recursion stack
    """
    
    def distributeCoins(self, root):
        self.moves = 0
        
        def dfs(node):
            if not node:
                return 0
            
            left_excess = dfs(node.left)
            right_excess = dfs(node.right)
            
            # Excess coins at this node
            excess = node.val + left_excess + right_excess - 1
            
            # Add moves (absolute value of excess)
            self.moves += abs(excess)
            
            return excess
        
        dfs(root)
        return self.moves

# Example usage
root = TreeNode(3, TreeNode(0), TreeNode(0))
sol = Solution()
print(sol.distributeCoins(root))  # Output: 2
```

### Alternative: With Move Details

```python
class Solution_WithDetails:
    """Track which moves are made."""
    
    def distributeCoins(self, root):
        self.moves = 0
        self.move_list = []
        
        def dfs(node):
            if not node:
                return 0
            
            left_excess = dfs(node.left)
            right_excess = dfs(node.right)
            
            # Handle left excess
            if left_excess != 0:
                self.moves += abs(left_excess)
                direction = "from" if left_excess > 0 else "to"
                self.move_list.append(
                    f"Move {abs(left_excess)} coins {direction} left child"
                )
            
            # Handle right excess
            if right_excess != 0:
                self.moves += abs(right_excess)
                direction = "from" if right_excess > 0 else "to"
                self.move_list.append(
                    f"Move {abs(right_excess)} coins {direction} right child"
                )
            
            excess = node.val + left_excess + right_excess - 1
            return excess
        
        dfs(root)
        return self.moves, self.move_list
```

### 🔍 Dry Run

```
Tree:   1
       / \
      0   2

dfs(left child 0):
  left_excess = 0, right_excess = 0
  excess = 0 + 0 + 0 - 1 = -1 (needs 1 coin)
  moves += |-1| = 1
  return -1

dfs(right child 2):
  left_excess = 0, right_excess = 0
  excess = 2 + 0 + 0 - 1 = 1 (has 1 extra)
  moves += |1| = 1
  return 1

dfs(root 1):
  left_excess = -1, right_excess = 1
  excess = 1 + (-1) + 1 - 1 = 0 (balanced)
  moves += |0| = 0
  return 0

Total moves: 1 + 1 = 2

Explanation:
  - Left needs 1: root gives 1 → move count = 1
  - Right has extra 1: root takes 1 → move count = 1
  - Total = 2
```

### ⏱️ Complexity Analysis

| Metric | Complexity | Notes |
|--------|------------|-------|
| Time | O(n) | Visit each node once |
| Space | O(h) | Recursion stack |

---

## Problem 6: Vertical Order Traversal

**LeetCode 987 - Hard**

### Problem Statement
Return vertical order traversal of binary tree. Nodes in same column and row are sorted by value.

```
Input: root = [3,9,20,null,null,15,7]
Output: [[9],[3,15],[20],[7]]
```

### 🎯 Intuition
Track **(column, row, value)** for each node:
- **Column**: left child = col-1, right child = col+1
- **Row**: depth level
- Sort by: column, then row, then value

Use DFS/BFS to traverse, hash map to group by column.

### 📊 Visual Representation

```
Tree:       3(0,0)
           / \
     9(-1,1)  20(1,1)
             /  \
       15(0,2)  7(2,2)

Column assignment:
  col -1: [9]
  col 0: [3, 15]
  col 1: [20]
  col 2: [7]

Sorting within each column by (row, value):
  col 0: [(0,3), (2,15)] → [3, 15]

Output: [[9], [3,15], [20], [7]]
```

### Approach 1: DFS with Sorting

```python
from collections import defaultdict

class Solution:
    """
    DFS with (col, row, val) tracking and sorting.
    
    Logic:
    - Track column, row, and value for each node
    - Group by column
    - Sort by row, then value
    
    Time: O(n log n)
    Space: O(n)
    """
    
    def verticalTraversal(self, root):
        # Map: column -> [(row, val)]
        columns = defaultdict(list)
        
        def dfs(node, row, col):
            if not node:
                return
            
            columns[col].append((row, node.val))
            dfs(node.left, row + 1, col - 1)
            dfs(node.right, row + 1, col + 1)
        
        dfs(root, 0, 0)
        
        # Sort columns and within each column sort by (row, val)
        result = []
        for col in sorted(columns.keys()):
            # Sort by row, then value
            column_nodes = sorted(columns[col])
            result.append([val for row, val in column_nodes])
        
        return result

# Example usage
root = TreeNode(3)
root.left = TreeNode(9)
root.right = TreeNode(20, TreeNode(15), TreeNode(7))
sol = Solution()
print(sol.verticalTraversal(root))
# Output: [[9], [3,15], [20], [7]]
```

### Approach 2: BFS with Sorting

```python
from collections import deque, defaultdict

class Solution_BFS:
    """
    BFS level-by-level with sorting.
    
    Time: O(n log n)
    Space: O(n)
    """
    
    def verticalTraversal(self, root):
        if not root:
            return []
        
        # Map: column -> [(row, val)]
        columns = defaultdict(list)
        
        # BFS with (node, row, col)
        queue = deque([(root, 0, 0)])
        
        while queue:
            node, row, col = queue.popleft()
            columns[col].append((row, node.val))
            
            if node.left:
                queue.append((node.left, row + 1, col - 1))
            if node.right:
                queue.append((node.right, row + 1, col + 1))
        
        # Sort and build result
        result = []
        for col in sorted(columns.keys()):
            column_nodes = sorted(columns[col])
            result.append([val for row, val in column_nodes])
        
        return result
```

### 🔍 Dry Run

```
Tree:   1(0,0)
       / \
  2(-1,1) 3(1,1)
     /       \
4(-2,2)     5(2,2)

DFS traversal:

Visit 1: columns[0] = [(0,1)]
Visit 2: columns[-1] = [(1,2)]
Visit 4: columns[-2] = [(2,4)]
Visit 3: columns[1] = [(1,3)]
Visit 5: columns[2] = [(2,5)]

Columns after traversal:
  -2: [(2,4)]
  -1: [(1,2)]
   0: [(0,1)]
   1: [(1,3)]
   2: [(2,5)]

Sort columns: -2, -1, 0, 1, 2
Result: [[4], [2], [1], [3], [5]]
```

### ⏱️ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| DFS | O(n log n) | O(n) | ⭐ Clean |
| BFS | O(n log n) | O(n) | Level-order |

---

## Problem 7: Count Complete Tree Nodes

**LeetCode 222 - Hard**

### Problem Statement
Given **complete binary tree**, count the nodes.

```
Input: root = [1,2,3,4,5,6]
Output: 6
```

### 🎯 Intuition
Exploit **complete tree** property:
- If left height == right height → perfect tree → nodes = 2^h - 1
- Otherwise → recurse on subtrees

**Key:** Complete tree means all levels filled except possibly last, which is filled from left.

### 📊 Visual Representation

```
Complete Tree:
          1
        /   \
       2     3
      / \   /
     4   5 6

Left height (leftmost path):
  1 → 2 → 4 → height = 3

Right height (rightmost path):
  1 → 3 → 6 → height = 3

Heights equal → Perfect tree!
Nodes = 2^3 - 1 = 7... wait, only 6 nodes?

Let's reconsider:
Right height (rightmost in left subtree):
  1 → 2 → 5 → height = 3

Actually need to check different subtrees:

For node 1:
  Left subtree height = 2 (to node 4)
  Right subtree height = 2 (to node 6)
  Not perfect at root level

For node 2:
  Left height = 1, right height = 1
  Perfect! nodes = 2^2 - 1 = 3

For node 3:
  Left height = 1, right height = 0
  Not perfect, recurse
```

### Solution

```python
class Solution:
    """
    Exploit complete tree property for O(log²n) time.
    
    Logic:
    - Check if tree is perfect (left_height == right_height)
    - If perfect: nodes = 2^height - 1
    - Otherwise: recurse on left and right
    
    Time: O((log n)²)
    Space: O(log n) recursion
    """
    
    def countNodes(self, root):
        if not root:
            return 0
        
        def get_height(node):
            """Get height by going left."""
            height = 0
            while node:
                height += 1
                node = node.left
            return height
        
        left_height = get_height(root.left)
        right_height = get_height(root.right)
        
        if left_height == right_height:
            # Left subtree is perfect
            return (1 << left_height) + self.countNodes(root.right)
        else:
            # Right subtree is perfect (one level less)
            return (1 << right_height) + self.countNodes(root.left)

# Example usage
root = TreeNode(1)
root.left = TreeNode(2, TreeNode(4), TreeNode(5))
root.right = TreeNode(3, TreeNode(6))
sol = Solution()
print(sol.countNodes(root))  # Output: 6
```

### Alternative: Binary Search

```python
class Solution_BinarySearch:
    """
    Use binary search to find last node.
    
    Logic:
    - Get tree height
    - Binary search on last level: [0, 2^h - 1]
    - Check if node exists using path
    
    Time: O((log n)²)
    Space: O(1)
    """
    
    def countNodes(self, root):
        if not root:
            return 0
        
        # Get height
        height = 0
        node = root
        while node.left:
            height += 1
            node = node.left
        
        if height == 0:
            return 1
        
        # Binary search on last level
        def exists(idx):
            """Check if node at index idx exists in last level."""
            left, right = 0, (1 << height) - 1
            node = root
            
            for _ in range(height):
                mid = (left + right) // 2
                if idx <= mid:
                    node = node.left
                    right = mid
                else:
                    node = node.right
                    left = mid + 1
            
            return node is not None
        
        # Binary search for rightmost existing node
        left, right = 0, (1 << height) - 1
        while left <= right:
            mid = (left + right) // 2
            if exists(mid):
                left = mid + 1
            else:
                right = mid - 1
        
        # Total nodes = all complete levels + last level
        return (1 << height) - 1 + left
```

### 🔍 Dry Run

```
Tree:     1
        /   \
       2     3
      / \   /
     4   5 6

Height calculation:
  Left path: 1 → 2 → 4 (height = 3)

countNodes(1):
  left_height = height(2) = 2
  right_height = height(3) = 2
  Equal! Left subtree is perfect.
  return 2^2 + countNodes(3)
  return 4 + countNodes(3)

countNodes(3):
  left_height = height(6) = 1
  right_height = height(null) = 0
  Not equal! Right subtree is perfect (empty).
  return 2^0 + countNodes(6)
  return 1 + countNodes(6)

countNodes(6):
  left_height = 0, right_height = 0
  Equal! Perfect (leaf).
  return 2^0 = 1

Backtrack:
  countNodes(3) = 1 + 1 = 2
  countNodes(1) = 4 + 2 = 6 ✓
```

### ⏱️ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Height-based | O((log n)²) | O(log n) | ⭐ Elegant |
| Binary Search | O((log n)²) | O(1) | Space optimal |
| Naive | O(n) | O(log n) | Don't use! |

---

## 🎯 Pattern Summary

### Core Binary Tree Patterns Covered

1. **Serialization**
   - Problems: Serialize/Deserialize
   - Pattern: Pre-order with null markers, or BFS
   - Use: Save/restore tree structure

2. **Path Problems**
   - Problems: Maximum Path Sum
   - Pattern: Post-order, track global optimum
   - Use: Find maximum/minimum path values

3. **BST Recovery**
   - Problems: Recover BST
   - Pattern: In-order traversal, find violations
   - Use: Fix or validate BST properties

4. **Greedy Placement**
   - Problems: Binary Tree Cameras
   - Pattern: Post-order with state tracking
   - Use: Minimize resources (cameras, colors)

5. **Coin/Resource Distribution**
   - Problems: Distribute Coins
   - Pattern: Calculate excess/deficit bottom-up
   - Use: Balance resources across tree

6. **Coordinate-based Traversal**
   - Problems: Vertical Order
   - Pattern: Track (x, y) coordinates
   - Use: Non-standard traversal orders

7. **Complete Tree Optimization**
   - Problems: Count Nodes
   - Pattern: Exploit structural properties
   - Use: Better than O(n) when possible

### Complexity Patterns

- **Most problems**: O(n) time, O(h) space
- **Complete tree**: Can optimize to O((log n)²)
- **Morris traversal**: O(1) space for in-order

### When to Use Each Pattern

✅ **Serialization**: When need to save/transfer tree
✅ **Path Problems**: DFS with global tracking
✅ **BST Operations**: In-order traversal
✅ **Greedy**: Post-order with states
✅ **Distribution**: Calculate excess bottom-up
✅ **Coordinates**: Hash map + sorting
✅ **Complete Tree**: Check heights, use perfect tree formula

### Interview Tips

1. **Ask about tree type**: BST? Complete? Full?
2. **Consider traversal order**: Pre, in, post, level?
3. **Track global state**: Use class variable or return tuple
4. **Post-order for bottom-up**: Most tree problems benefit
5. **Space optimization**: Morris if O(1) space needed

---

## 🎓 Key Takeaways

1. **Post-order** is king for bottom-up problems (cameras, coins)
2. **In-order** essential for BST problems
3. **Pre-order** natural for serialization
4. **State tracking** crucial for greedy algorithms
5. **Complete tree** properties enable optimization
6. **Global variables** simplify many tree problems

Master these patterns and binary tree problems become straightforward! 🌳🚀

