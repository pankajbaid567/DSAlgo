# 🌳 Binary Trees - Comprehensive Cheatsheet

## 📚 Table of Contents
1. [Core Concepts](#core-concepts)
2. [Tree Traversals](#tree-traversals)
3. [Common Patterns](#common-patterns)
4. [Essential Algorithms](#essential-algorithms)
5. [Dry Run Examples](#dry-run-examples)
6. [Time & Space Complexity](#time--space-complexity)

---

## Core Concepts

### What is a Binary Tree?
A tree data structure where each node has at most two children (left and right).

```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right
```

### Types of Binary Trees

#### 1. Full Binary Tree
Every node has 0 or 2 children.
```
      1
    /   \
   2     3
  / \
 4   5
```

#### 2. Complete Binary Tree
All levels filled except possibly last, which is filled left to right.
```
      1
    /   \
   2     3
  / \   /
 4   5 6
```

#### 3. Perfect Binary Tree
All internal nodes have 2 children, all leaves at same level.
```
      1
    /   \
   2     3
  / \   / \
 4   5 6   7
```

#### 4. Binary Search Tree (BST)
Left subtree < node < Right subtree
```
      8
    /   \
   3     10
  / \      \
 1   6      14
```

---

## Tree Traversals

### 1. Depth First Search (DFS)

#### Inorder (Left → Root → Right)
**Use**: BST gives sorted order
```python
def inorder(root):
    if not root:
        return []
    
    result = []
    result.extend(inorder(root.left))
    result.append(root.val)
    result.extend(inorder(root.right))
    return result

# Iterative using stack
def inorder_iterative(root):
    result = []
    stack = []
    current = root
    
    while current or stack:
        while current:
            stack.append(current)
            current = current.left
        
        current = stack.pop()
        result.append(current.val)
        current = current.right
    
    return result

# Morris Traversal (O(1) space)
def inorder_morris(root):
    result = []
    current = root
    
    while current:
        if not current.left:
            result.append(current.val)
            current = current.right
        else:
            # Find predecessor
            pred = current.left
            while pred.right and pred.right != current:
                pred = pred.right
            
            if not pred.right:
                pred.right = current
                current = current.left
            else:
                pred.right = None
                result.append(current.val)
                current = current.right
    
    return result
```

#### Preorder (Root → Left → Right)
**Use**: Copy tree, prefix expression
```python
def preorder(root):
    if not root:
        return []
    
    result = [root.val]
    result.extend(preorder(root.left))
    result.extend(preorder(root.right))
    return result

# Iterative
def preorder_iterative(root):
    if not root:
        return []
    
    result = []
    stack = [root]
    
    while stack:
        node = stack.pop()
        result.append(node.val)
        
        if node.right:
            stack.append(node.right)
        if node.left:
            stack.append(node.left)
    
    return result
```

#### Postorder (Left → Right → Root)
**Use**: Delete tree, postfix expression
```python
def postorder(root):
    if not root:
        return []
    
    result = []
    result.extend(postorder(root.left))
    result.extend(postorder(root.right))
    result.append(root.val)
    return result

# Iterative using 2 stacks
def postorder_iterative(root):
    if not root:
        return []
    
    stack1 = [root]
    stack2 = []
    
    while stack1:
        node = stack1.pop()
        stack2.append(node)
        
        if node.left:
            stack1.append(node.left)
        if node.right:
            stack1.append(node.right)
    
    return [node.val for node in reversed(stack2)]
```

### 2. Breadth First Search (BFS) - Level Order

```python
from collections import deque

def level_order(root):
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

# Zigzag level order
def zigzag_level_order(root):
    if not root:
        return []
    
    result = []
    queue = deque([root])
    left_to_right = True
    
    while queue:
        level_size = len(queue)
        level = deque()
        
        for _ in range(level_size):
            node = queue.popleft()
            
            if left_to_right:
                level.append(node.val)
            else:
                level.appendleft(node.val)
            
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
        
        result.append(list(level))
        left_to_right = not left_to_right
    
    return result
```

---

## Common Patterns

### Pattern 1: Tree Property Checking

```python
# Check if tree is balanced
def is_balanced(root):
    def height(node):
        if not node:
            return 0
        
        left = height(node.left)
        if left == -1:
            return -1
        
        right = height(node.right)
        if right == -1:
            return -1
        
        if abs(left - right) > 1:
            return -1
        
        return max(left, right) + 1
    
    return height(root) != -1

# Check if tree is symmetric
def is_symmetric(root):
    def is_mirror(t1, t2):
        if not t1 and not t2:
            return True
        if not t1 or not t2:
            return False
        
        return (t1.val == t2.val and 
                is_mirror(t1.left, t2.right) and 
                is_mirror(t1.right, t2.left))
    
    return is_mirror(root, root)

# Check if valid BST
def is_valid_bst(root):
    def validate(node, low, high):
        if not node:
            return True
        
        if node.val <= low or node.val >= high:
            return False
        
        return (validate(node.left, low, node.val) and 
                validate(node.right, node.val, high))
    
    return validate(root, float('-inf'), float('inf'))
```

### Pattern 2: Path Problems

```python
# Binary tree paths
def binary_tree_paths(root):
    if not root:
        return []
    
    paths = []
    
    def dfs(node, path):
        if not node.left and not node.right:
            paths.append(path + str(node.val))
            return
        
        if node.left:
            dfs(node.left, path + str(node.val) + "->")
        if node.right:
            dfs(node.right, path + str(node.val) + "->")
    
    dfs(root, "")
    return paths

# Path sum
def has_path_sum(root, target_sum):
    if not root:
        return False
    
    if not root.left and not root.right:
        return root.val == target_sum
    
    return (has_path_sum(root.left, target_sum - root.val) or 
            has_path_sum(root.right, target_sum - root.val))

# All paths with sum
def path_sum(root, target_sum):
    result = []
    
    def dfs(node, remaining, path):
        if not node:
            return
        
        path.append(node.val)
        
        if not node.left and not node.right and remaining == node.val:
            result.append(path[:])
        
        dfs(node.left, remaining - node.val, path)
        dfs(node.right, remaining - node.val, path)
        
        path.pop()
    
    dfs(root, target_sum, [])
    return result

# Maximum path sum
def max_path_sum(root):
    max_sum = float('-inf')
    
    def max_gain(node):
        nonlocal max_sum
        
        if not node:
            return 0
        
        left_gain = max(max_gain(node.left), 0)
        right_gain = max(max_gain(node.right), 0)
        
        price_newpath = node.val + left_gain + right_gain
        max_sum = max(max_sum, price_newpath)
        
        return node.val + max(left_gain, right_gain)
    
    max_gain(root)
    return max_sum
```

### Pattern 3: Tree Construction

```python
# From inorder and preorder
def build_tree_pre_in(preorder, inorder):
    if not preorder or not inorder:
        return None
    
    root_val = preorder[0]
    root = TreeNode(root_val)
    
    mid = inorder.index(root_val)
    
    root.left = build_tree_pre_in(preorder[1:mid+1], inorder[:mid])
    root.right = build_tree_pre_in(preorder[mid+1:], inorder[mid+1:])
    
    return root

# From inorder and postorder
def build_tree_post_in(inorder, postorder):
    if not inorder or not postorder:
        return None
    
    root_val = postorder[-1]
    root = TreeNode(root_val)
    
    mid = inorder.index(root_val)
    
    root.left = build_tree_post_in(inorder[:mid], postorder[:mid])
    root.right = build_tree_post_in(inorder[mid+1:], postorder[mid:-1])
    
    return root
```

### Pattern 4: Lowest Common Ancestor (LCA)

```python
# LCA in binary tree
def lowest_common_ancestor(root, p, q):
    if not root or root == p or root == q:
        return root
    
    left = lowest_common_ancestor(root.left, p, q)
    right = lowest_common_ancestor(root.right, p, q)
    
    if left and right:
        return root
    
    return left if left else right

# LCA in BST
def lowest_common_ancestor_bst(root, p, q):
    while root:
        if p.val < root.val and q.val < root.val:
            root = root.left
        elif p.val > root.val and q.val > root.val:
            root = root.right
        else:
            return root
```

### Pattern 5: View Patterns

```python
# Right view
def right_side_view(root):
    if not root:
        return []
    
    result = []
    queue = deque([root])
    
    while queue:
        level_size = len(queue)
        
        for i in range(level_size):
            node = queue.popleft()
            
            if i == level_size - 1:
                result.append(node.val)
            
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
    
    return result

# Vertical order traversal
def vertical_order(root):
    if not root:
        return []
    
    column_table = {}
    queue = deque([(root, 0)])
    
    while queue:
        node, column = queue.popleft()
        
        if column not in column_table:
            column_table[column] = []
        column_table[column].append(node.val)
        
        if node.left:
            queue.append((node.left, column - 1))
        if node.right:
            queue.append((node.right, column + 1))
    
    return [column_table[x] for x in sorted(column_table.keys())]

# Boundary traversal
def boundary_traversal(root):
    if not root:
        return []
    
    result = [root.val]
    
    # Left boundary
    def left_boundary(node):
        if not node or (not node.left and not node.right):
            return
        result.append(node.val)
        if node.left:
            left_boundary(node.left)
        else:
            left_boundary(node.right)
    
    # Leaves
    def leaves(node):
        if not node:
            return
        if not node.left and not node.right:
            result.append(node.val)
            return
        leaves(node.left)
        leaves(node.right)
    
    # Right boundary
    def right_boundary(node):
        if not node or (not node.left and not node.right):
            return
        if node.right:
            right_boundary(node.right)
        else:
            right_boundary(node.left)
        result.append(node.val)
    
    left_boundary(root.left)
    leaves(root.left)
    leaves(root.right)
    right_boundary(root.right)
    
    return result
```

### Pattern 6: Diameter & Width

```python
# Diameter of tree
def diameter_of_binary_tree(root):
    diameter = 0
    
    def height(node):
        nonlocal diameter
        
        if not node:
            return 0
        
        left = height(node.left)
        right = height(node.right)
        
        diameter = max(diameter, left + right)
        
        return max(left, right) + 1
    
    height(root)
    return diameter

# Maximum width
def width_of_binary_tree(root):
    if not root:
        return 0
    
    max_width = 0
    queue = deque([(root, 0)])
    
    while queue:
        level_size = len(queue)
        _, level_start = queue[0]
        
        for i in range(level_size):
            node, index = queue.popleft()
            
            if node.left:
                queue.append((node.left, 2 * index))
            if node.right:
                queue.append((node.right, 2 * index + 1))
        
        max_width = max(max_width, index - level_start + 1)
    
    return max_width
```

---

## 🎨 Dry Run Example

### Tree Traversals Dry Run

```
Tree:
        1
       / \
      2   3
     / \
    4   5

Preorder (Root-Left-Right):
Visit: 1 → 2 → 4 → 5 → 3
Result: [1, 2, 4, 5, 3]

Inorder (Left-Root-Right):
Visit: 4 → 2 → 5 → 1 → 3
Result: [4, 2, 5, 1, 3]

Postorder (Left-Right-Root):
Visit: 4 → 5 → 2 → 3 → 1
Result: [4, 5, 2, 3, 1]

Level Order:
Level 0: [1]
Level 1: [2, 3]
Level 2: [4, 5]
Result: [[1], [2, 3], [4, 5]]
```

---

## ⏱️ Time & Space Complexity

| Operation | Time | Space |
|-----------|------|-------|
| Traversal (any) | O(n) | O(h) recursive, O(n) iterative |
| Search | O(h) BST, O(n) general | O(h) |
| Insert | O(h) BST, O(n) general | O(h) |
| Delete | O(h) BST, O(n) general | O(h) |
| Height | O(n) | O(h) |
| LCA | O(n) | O(h) |
| Serialize | O(n) | O(n) |

h = height of tree  
For balanced tree: h = log n  
For skewed tree: h = n

---

## 🎯 Must-Know Problems

### Easy
- [104. Maximum Depth of Binary Tree](https://leetcode.com/problems/maximum-depth-of-binary-tree/)
- [226. Invert Binary Tree](https://leetcode.com/problems/invert-binary-tree/)
- [100. Same Tree](https://leetcode.com/problems/same-tree/)
- [101. Symmetric Tree](https://leetcode.com/problems/symmetric-tree/)
- [543. Diameter of Binary Tree](https://leetcode.com/problems/diameter-of-binary-tree/)

### Medium
- [102. Binary Tree Level Order Traversal](https://leetcode.com/problems/binary-tree-level-order-traversal/)
- [103. Binary Tree Zigzag Level Order](https://leetcode.com/problems/binary-tree-zigzag-level-order-traversal/)
- [105. Construct Binary Tree from Preorder and Inorder](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)
- [114. Flatten Binary Tree to Linked List](https://leetcode.com/problems/flatten-binary-tree-to-linked-list/)
- [236. Lowest Common Ancestor](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/)
- [297. Serialize and Deserialize Binary Tree](https://leetcode.com/problems/serialize-and-deserialize-binary-tree/)

### Hard
- [124. Binary Tree Maximum Path Sum](https://leetcode.com/problems/binary-tree-maximum-path-sum/)
- [145. Binary Tree Postorder Traversal](https://leetcode.com/problems/binary-tree-postorder-traversal/) (Iterative)
- [987. Vertical Order Traversal](https://leetcode.com/problems/vertical-order-traversal-of-a-binary-tree/)

---

## 🎓 Pro Tips

1. **Always check for null**: Most bugs come from not checking if node is null
2. **Recursion is natural**: Trees are recursive structures
3. **Use helper functions**: Pass additional parameters for cleaner code
4. **Draw it out**: Visual representation helps immensely
5. **Master traversals**: They're the foundation of all tree problems
6. **BST properties**: Remember left < root < right for optimizations

---

**Master these patterns and tree problems become intuitive! 🌳**
