# 🔥 Linked List - Hard Problems Collection

## Table of Contents
1. [Merge k Sorted Lists](#1-merge-k-sorted-lists)
2. [Reverse Nodes in k-Group](#2-reverse-nodes-in-k-group)
3. [LRU Cache](#3-lru-cache)
4. [Copy List with Random Pointer](#4-copy-list-with-random-pointer)
5. [Flatten a Multilevel Doubly Linked List](#5-flatten-a-multilevel-doubly-linked-list)
6. [LFU Cache](#6-lfu-cache)
7. [Merge In Between Linked Lists](#7-merge-in-between-linked-lists)
8. [Design Browser History](#8-design-browser-history)

---

## 1. Merge k Sorted Lists
**LeetCode**: [#23 - Merge k Sorted Lists](https://leetcode.com/problems/merge-k-sorted-lists/)

### Problem Statement
Merge k sorted linked lists and return it as one sorted list.

### Intuition
- Brute force: Merge lists one by one → O(kN) where N is total nodes
- Better: Use min-heap to always get minimum among k lists → O(N log k)
- Best: Divide and conquer (merge sort style) → O(N log k)

### Approach 1: Min Heap
1. Add first node from each list to min-heap
2. Extract minimum, add to result
3. Add next node from that list to heap
4. Repeat until heap is empty

### Visual Representation
```
Lists:
  1 → 4 → 5
  1 → 3 → 4
  2 → 6

Heap visualization (min-heap):
         1
       /   \
      1     2
     / \
    4   3

Extract 1, add 4: 1 → ...
Extract 1, add 3: 1 → 1 → ...
Extract 2, add 6: 1 → 1 → 2 → ...
Continue until done
```

### Dry Run
```
Lists: [1→4→5], [1→3→4], [2→6]

Initial heap: [(1, 0), (1, 1), (2, 2)]

Step 1: Pop (1, 0)
        Result: 1
        Push (4, 0)
        Heap: [(1, 1), (2, 2), (4, 0)]

Step 2: Pop (1, 1)
        Result: 1 → 1
        Push (3, 1)
        Heap: [(2, 2), (4, 0), (3, 1)]

Step 3: Pop (2, 2)
        Result: 1 → 1 → 2
        Push (6, 2)
        Heap: [(3, 1), (4, 0), (6, 2)]

Continue...
Final: 1 → 1 → 2 → 3 → 4 → 4 → 5 → 6 ✓
```

### Code Solution

```python
import heapq

class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

# Approach 1: Min Heap - O(N log k)
def mergeKLists_heap(lists):
    heap = []
    
    # Add first node from each list
    for i, node in enumerate(lists):
        if node:
            heapq.heappush(heap, (node.val, i, node))
    
    dummy = ListNode(0)
    current = dummy
    
    while heap:
        val, i, node = heapq.heappop(heap)
        current.next = node
        current = current.next
        
        if node.next:
            heapq.heappush(heap, (node.next.val, i, node.next))
    
    return dummy.next

# Approach 2: Divide and Conquer - O(N log k)
def mergeKLists_divide_conquer(lists):
    if not lists:
        return None
    
    def merge_two(l1, l2):
        dummy = ListNode(0)
        current = dummy
        
        while l1 and l2:
            if l1.val < l2.val:
                current.next = l1
                l1 = l1.next
            else:
                current.next = l2
                l2 = l2.next
            current = current.next
        
        current.next = l1 or l2
        return dummy.next
    
    while len(lists) > 1:
        merged = []
        for i in range(0, len(lists), 2):
            l1 = lists[i]
            l2 = lists[i + 1] if i + 1 < len(lists) else None
            merged.append(merge_two(l1, l2))
        lists = merged
    
    return lists[0]

# Approach 3: Sequential Merge - O(kN)
def mergeKLists_sequential(lists):
    if not lists:
        return None
    
    def merge_two(l1, l2):
        dummy = ListNode(0)
        current = dummy
        
        while l1 and l2:
            if l1.val < l2.val:
                current.next = l1
                l1 = l1.next
            else:
                current.next = l2
                l2 = l2.next
            current = current.next
        
        current.next = l1 or l2
        return dummy.next
    
    result = lists[0]
    for i in range(1, len(lists)):
        result = merge_two(result, lists[i])
    
    return result
```

**Time Complexity**: 
- Heap: O(N log k) where N = total nodes, k = number of lists
- Divide & Conquer: O(N log k)
- Sequential: O(kN)

**Space Complexity**: O(k) for heap, O(1) for others

---

## 2. Reverse Nodes in k-Group
**LeetCode**: [#25 - Reverse Nodes in k-Group](https://leetcode.com/problems/reverse-nodes-in-k-group/)

### Problem Statement
Reverse nodes of linked list k at a time. If remaining nodes < k, keep as is.

### Intuition
- Count if we have k nodes remaining
- If yes, reverse them
- Recursively process rest
- If no, return as is

### Visual Representation
```
Original: 1 → 2 → 3 → 4 → 5, k = 3

Step 1: Check first 3 nodes exist ✓
        Reverse: 3 → 2 → 1 → 4 → 5

Step 2: From node 4, check next 3 nodes
        Only 2 remain, keep as is
        
Final:  3 → 2 → 1 → 4 → 5 ✓

Diagram:
Before:  1 → 2 → 3 | 4 → 5
         └───────┘   └──┘
         reverse k   < k (keep)

After:   3 → 2 → 1 → 4 → 5
```

### Dry Run
```
Input: 1 → 2 → 3 → 4 → 5, k = 2

Step 1: Count first 2 nodes: 1, 2 ✓
        Reverse them:
        Before: 1 → 2 → 3 → 4 → 5
        After:  2 → 1 → 3 → 4 → 5

Step 2: Count next 2 nodes: 3, 4 ✓
        Reverse them:
        Before: 2 → 1 → 3 → 4 → 5
        After:  2 → 1 → 4 → 3 → 5

Step 3: Count next 2 nodes: only 5
        Keep as is

Final: 2 → 1 → 4 → 3 → 5 ✓
```

### Code Solution

```python
def reverseKGroup(head, k):
    # Count if k nodes exist
    count = 0
    current = head
    while current and count < k:
        current = current.next
        count += 1
    
    # If less than k nodes, return as is
    if count < k:
        return head
    
    # Reverse first k nodes
    prev = None
    current = head
    for _ in range(k):
        next_node = current.next
        current.next = prev
        prev = current
        current = next_node
    
    # Recursively reverse remaining list
    head.next = reverseKGroup(current, k)
    
    return prev

# Iterative approach
def reverseKGroup_iterative(head, k):
    dummy = ListNode(0)
    dummy.next = head
    
    prev_group = dummy
    
    while True:
        # Check if k nodes exist
        kth = prev_group
        for i in range(k):
            kth = kth.next
            if not kth:
                return dummy.next
        
        # Save next group start
        next_group = kth.next
        
        # Reverse current group
        prev = next_group
        current = prev_group.next
        
        for _ in range(k):
            temp = current.next
            current.next = prev
            prev = current
            current = temp
        
        # Connect with previous group
        temp = prev_group.next
        prev_group.next = prev
        prev_group = temp
    
    return dummy.next
```

**Time Complexity**: O(n)  
**Space Complexity**: O(1) iterative, O(n/k) recursive

---

## 3. LRU Cache
**LeetCode**: [#146 - LRU Cache](https://leetcode.com/problems/lru-cache/)

### Problem Statement
Design LRU (Least Recently Used) cache with O(1) get and put operations.

### Intuition
- Use HashMap for O(1) access
- Use Doubly Linked List for O(1) removal and addition
- Most recent at head, least recent at tail
- When capacity full, remove tail

### Visual Representation
```
LRU Cache (capacity = 3):

After operations:
put(1, 1): 1
put(2, 2): 2 ⇄ 1
put(3, 3): 3 ⇄ 2 ⇄ 1
get(1):    1 ⇄ 3 ⇄ 2  (move 1 to front)
put(4, 4): 4 ⇄ 1 ⇄ 3  (evict 2)

Structure:
┌─────────────────────────────┐
│  HashMap                    │
│  key → DLL Node             │
│                             │
│  Doubly Linked List         │
│  Head ⇄ ... ⇄ Tail          │
│  (Recent)  (LRU)            │
└─────────────────────────────┘
```

### Dry Run
```
Capacity = 2

put(1, 1):
  Cache: {1: 1}
  DLL: 1

put(2, 2):
  Cache: {1: 1, 2: 2}
  DLL: 2 ⇄ 1

get(1):
  Returns: 1
  DLL: 1 ⇄ 2 (move 1 to front)

put(3, 3):
  Capacity full, remove 2
  Cache: {1: 1, 3: 3}
  DLL: 3 ⇄ 1

get(2):
  Returns: -1 (not found)

put(4, 4):
  Remove 1
  Cache: {3: 3, 4: 4}
  DLL: 4 ⇄ 3
```

### Code Solution

```python
class DLLNode:
    def __init__(self, key=0, val=0):
        self.key = key
        self.val = val
        self.prev = None
        self.next = None

class LRUCache:
    def __init__(self, capacity: int):
        self.capacity = capacity
        self.cache = {}  # key -> DLLNode
        
        # Dummy head and tail
        self.head = DLLNode()
        self.tail = DLLNode()
        self.head.next = self.tail
        self.tail.prev = self.head
    
    def _remove(self, node):
        """Remove node from DLL"""
        prev_node = node.prev
        next_node = node.next
        prev_node.next = next_node
        next_node.prev = prev_node
    
    def _add_to_front(self, node):
        """Add node right after head"""
        node.prev = self.head
        node.next = self.head.next
        self.head.next.prev = node
        self.head.next = node
    
    def _move_to_front(self, node):
        """Move existing node to front"""
        self._remove(node)
        self._add_to_front(node)
    
    def get(self, key: int) -> int:
        if key not in self.cache:
            return -1
        
        node = self.cache[key]
        self._move_to_front(node)
        return node.val
    
    def put(self, key: int, value: int) -> None:
        if key in self.cache:
            # Update existing
            node = self.cache[key]
            node.val = value
            self._move_to_front(node)
        else:
            # Add new
            if len(self.cache) >= self.capacity:
                # Remove LRU (node before tail)
                lru = self.tail.prev
                self._remove(lru)
                del self.cache[lru.key]
            
            # Add new node
            new_node = DLLNode(key, value)
            self.cache[key] = new_node
            self._add_to_front(new_node)

# Usage
cache = LRUCache(2)
cache.put(1, 1)
cache.put(2, 2)
print(cache.get(1))     # returns 1
cache.put(3, 3)         # evicts key 2
print(cache.get(2))     # returns -1 (not found)
```

**Time Complexity**: O(1) for both get and put  
**Space Complexity**: O(capacity)

---

## 4. Copy List with Random Pointer
**LeetCode**: [#138 - Copy List with Random Pointer](https://leetcode.com/problems/copy-list-with-random-pointer/)

### Problem Statement
Deep copy linked list where each node has a random pointer.

### Intuition
- Can't simply copy because random pointers may point to nodes not yet created
- Solution 1: Use hashmap (old node → new node)
- Solution 2: Interweave nodes, then separate

### Visual Representation
```
Original:
1 → 2 → 3 → None
↓   ↓   ↓
3   1   None

Approach 2 (Interweaving):
Step 1: Insert copies
1 → 1' → 2 → 2' → 3 → 3' → None

Step 2: Set random pointers
1' random = 1.random.next
2' random = 2.random.next
3' random = 3.random.next

Step 3: Separate lists
1 → 2 → 3 → None
1' → 2' → 3' → None
```

### Code Solution

```python
class Node:
    def __init__(self, x: int, next=None, random=None):
        self.val = x
        self.next = next
        self.random = random

# Approach 1: HashMap - O(n) space
def copyRandomList_hashmap(head):
    if not head:
        return None
    
    old_to_new = {}
    
    # First pass: create all nodes
    current = head
    while current:
        old_to_new[current] = Node(current.val)
        current = current.next
    
    # Second pass: set next and random
    current = head
    while current:
        if current.next:
            old_to_new[current].next = old_to_new[current.next]
        if current.random:
            old_to_new[current].random = old_to_new[current.random]
        current = current.next
    
    return old_to_new[head]

# Approach 2: Interweaving - O(1) space
def copyRandomList_interweave(head):
    if not head:
        return None
    
    # Step 1: Create copies and interweave
    current = head
    while current:
        copy = Node(current.val)
        copy.next = current.next
        current.next = copy
        current = copy.next
    
    # Step 2: Set random pointers for copies
    current = head
    while current:
        if current.random:
            current.next.random = current.random.next
        current = current.next.next
    
    # Step 3: Separate the two lists
    current = head
    copy_head = head.next
    while current:
        copy = current.next
        current.next = copy.next
        if copy.next:
            copy.next = copy.next.next
        current = current.next
    
    return copy_head
```

**Time Complexity**: O(n)  
**Space Complexity**: O(n) for hashmap, O(1) for interweaving

---

## 5. Flatten a Multilevel Doubly Linked List
**LeetCode**: [#430 - Flatten a Multilevel Doubly Linked List](https://leetcode.com/problems/flatten-a-multilevel-doubly-linked-list/)

### Problem Statement
Flatten a multilevel doubly linked list where nodes can have child pointers.

### Visual Representation
```
Original:
1 ⇄ 2 ⇄ 3 ⇄ 4 ⇄ 5 ⇄ 6
    ↓
    7 ⇄ 8 ⇄ 9 ⇄ 10
        ↓
        11 ⇄ 12

Flattened:
1 ⇄ 2 ⇄ 7 ⇄ 8 ⇄ 11 ⇄ 12 ⇄ 9 ⇄ 10 ⇄ 3 ⇄ 4 ⇄ 5 ⇄ 6
```

### Code Solution

```python
class Node:
    def __init__(self, val, prev=None, next=None, child=None):
        self.val = val
        self.prev = prev
        self.next = next
        self.child = child

# Recursive approach
def flatten(head):
    if not head:
        return None
    
    def flatten_and_get_tail(node):
        current = node
        tail = None
        
        while current:
            next_node = current.next
            
            if current.child:
                # Flatten child list
                child_tail = flatten_and_get_tail(current.child)
                
                # Connect current to child
                current.next = current.child
                current.child.prev = current
                
                # Connect child tail to next
                if next_node:
                    child_tail.next = next_node
                    next_node.prev = child_tail
                
                current.child = None
                tail = child_tail
            else:
                tail = current
            
            current = next_node
        
        return tail
    
    flatten_and_get_tail(head)
    return head

# Iterative with stack
def flatten_stack(head):
    if not head:
        return None
    
    stack = [head]
    prev = None
    
    while stack:
        current = stack.pop()
        
        if prev:
            prev.next = current
            current.prev = prev
        
        if current.next:
            stack.append(current.next)
        
        if current.child:
            stack.append(current.child)
            current.child = None
        
        prev = current
    
    return head
```

**Time Complexity**: O(n)  
**Space Complexity**: O(1) recursive (excluding recursion stack), O(n) iterative

---

## 6. LFU Cache
**LeetCode**: [#460 - LFU Cache](https://leetcode.com/problems/lfu-cache/)

### Problem Statement
Design LFU (Least Frequently Used) cache. When tie, remove LRU.

### Intuition
- Track frequency of each key
- Maintain separate DLL for each frequency
- When tie in frequency, use LRU within that frequency

### Code Solution

```python
from collections import defaultdict

class DLLNode:
    def __init__(self, key=0, val=0):
        self.key = key
        self.val = val
        self.freq = 1
        self.prev = None
        self.next = None

class DLL:
    def __init__(self):
        self.head = DLLNode()
        self.tail = DLLNode()
        self.head.next = self.tail
        self.tail.prev = self.head
        self.size = 0
    
    def add(self, node):
        node.prev = self.head
        node.next = self.head.next
        self.head.next.prev = node
        self.head.next = node
        self.size += 1
    
    def remove(self, node):
        node.prev.next = node.next
        node.next.prev = node.prev
        self.size -= 1
    
    def remove_last(self):
        if self.size > 0:
            node = self.tail.prev
            self.remove(node)
            return node
        return None

class LFUCache:
    def __init__(self, capacity: int):
        self.capacity = capacity
        self.min_freq = 0
        self.key_node = {}  # key -> node
        self.freq_list = defaultdict(DLL)  # freq -> DLL
    
    def _update_freq(self, node):
        freq = node.freq
        self.freq_list[freq].remove(node)
        
        if self.freq_list[freq].size == 0:
            del self.freq_list[freq]
            if freq == self.min_freq:
                self.min_freq += 1
        
        node.freq += 1
        self.freq_list[node.freq].add(node)
    
    def get(self, key: int) -> int:
        if key not in self.key_node:
            return -1
        
        node = self.key_node[key]
        self._update_freq(node)
        return node.val
    
    def put(self, key: int, value: int) -> None:
        if self.capacity == 0:
            return
        
        if key in self.key_node:
            node = self.key_node[key]
            node.val = value
            self._update_freq(node)
        else:
            if len(self.key_node) >= self.capacity:
                lfu_dll = self.freq_list[self.min_freq]
                lfu_node = lfu_dll.remove_last()
                del self.key_node[lfu_node.key]
            
            new_node = DLLNode(key, value)
            self.key_node[key] = new_node
            self.freq_list[1].add(new_node)
            self.min_freq = 1
```

**Time Complexity**: O(1) for both get and put  
**Space Complexity**: O(capacity)

---

## 7. Design Browser History
**LeetCode**: [#1472 - Design Browser History](https://leetcode.com/problems/design-browser-history/)

### Problem Statement
Design browser history with visit, back, and forward operations.

### Code Solution

```python
class BrowserHistory:
    def __init__(self, homepage: str):
        self.history = [homepage]
        self.current = 0
    
    def visit(self, url: str) -> None:
        # Remove forward history
        self.history = self.history[:self.current + 1]
        self.history.append(url)
        self.current += 1
    
    def back(self, steps: int) -> str:
        self.current = max(0, self.current - steps)
        return self.history[self.current]
    
    def forward(self, steps: int) -> str:
        self.current = min(len(self.history) - 1, self.current + steps)
        return self.history[self.current]

# Using Doubly Linked List
class Node:
    def __init__(self, url):
        self.url = url
        self.prev = None
        self.next = None

class BrowserHistoryDLL:
    def __init__(self, homepage: str):
        self.current = Node(homepage)
    
    def visit(self, url: str) -> None:
        new_node = Node(url)
        self.current.next = new_node
        new_node.prev = self.current
        self.current = new_node
    
    def back(self, steps: int) -> str:
        while steps > 0 and self.current.prev:
            self.current = self.current.prev
            steps -= 1
        return self.current.url
    
    def forward(self, steps: int) -> str:
        while steps > 0 and self.current.next:
            self.current = self.current.next
            steps -= 1
        return self.current.url
```

**Time Complexity**: O(1) for visit, O(steps) for back/forward  
**Space Complexity**: O(n) where n is number of URLs visited

---

## 🎯 Key Takeaways

1. **Merge k Lists**: Use heap or divide-and-conquer
2. **Reverse in k Groups**: Count first, then reverse
3. **LRU Cache**: HashMap + Doubly Linked List
4. **Copy with Random**: HashMap or interweaving
5. **Flatten Multilevel**: DFS with stack or recursion
6. **LFU Cache**: Frequency buckets with LRU tie-breaking

## 📈 Interview Frequency
- **FAANG**: Very High (especially LRU Cache, Merge k Lists)
- **Startups**: High (focus on cache implementations)
- **FAANG++**: Master all patterns + variations

---

**These are must-know hard problems for top-tier interviews! 🚀**
