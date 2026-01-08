# 🔗 Linked List - Comprehensive Cheatsheet

## 📚 Table of Contents
1. [Core Concepts](#core-concepts)
2. [Types of Linked Lists](#types-of-linked-lists)
3. [Common Patterns](#common-patterns)
4. [Essential Techniques](#essential-techniques)
5. [Dry Run Examples](#dry-run-examples)
6. [Time & Space Complexity](#time--space-complexity)

---

## Core Concepts

### What is a Linked List?
A linear data structure where elements (nodes) are stored in separate memory locations and connected using pointers/references.

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next
```

### Advantages
✅ Dynamic size  
✅ Easy insertion/deletion  
✅ No memory wastage  

### Disadvantages
❌ No random access  
❌ Extra memory for pointers  
❌ Not cache friendly  

---

## Types of Linked Lists

### 1️⃣ Singly Linked List
Each node points to the next node.

```python
class Node:
    def __init__(self, data):
        self.data = data
        self.next = None

# Visual: 1 → 2 → 3 → 4 → None
```

**Basic Operations:**
```python
class LinkedList:
    def __init__(self):
        self.head = None
    
    # Insert at beginning - O(1)
    def insert_at_beginning(self, data):
        new_node = Node(data)
        new_node.next = self.head
        self.head = new_node
    
    # Insert at end - O(n)
    def insert_at_end(self, data):
        new_node = Node(data)
        if not self.head:
            self.head = new_node
            return
        
        current = self.head
        while current.next:
            current = current.next
        current.next = new_node
    
    # Delete node - O(n)
    def delete_node(self, key):
        current = self.head
        
        # Delete head
        if current and current.data == key:
            self.head = current.next
            return
        
        # Find node to delete
        prev = None
        while current and current.data != key:
            prev = current
            current = current.next
        
        if not current:
            return
        
        prev.next = current.next
    
    # Search - O(n)
    def search(self, key):
        current = self.head
        while current:
            if current.data == key:
                return True
            current = current.next
        return False
    
    # Print list - O(n)
    def print_list(self):
        current = self.head
        while current:
            print(current.data, end=" → ")
            current = current.next
        print("None")
```

---

### 2️⃣ Doubly Linked List
Each node has pointers to both next and previous nodes.

```python
class DNode:
    def __init__(self, data):
        self.data = data
        self.next = None
        self.prev = None

# Visual: None ← 1 ⇄ 2 ⇄ 3 ⇄ 4 → None
```

**Basic Operations:**
```python
class DoublyLinkedList:
    def __init__(self):
        self.head = None
    
    # Insert at beginning - O(1)
    def insert_at_beginning(self, data):
        new_node = DNode(data)
        new_node.next = self.head
        
        if self.head:
            self.head.prev = new_node
        
        self.head = new_node
    
    # Insert at end - O(n)
    def insert_at_end(self, data):
        new_node = DNode(data)
        
        if not self.head:
            self.head = new_node
            return
        
        current = self.head
        while current.next:
            current = current.next
        
        current.next = new_node
        new_node.prev = current
    
    # Delete node - O(n)
    def delete_node(self, node):
        if not node:
            return
        
        if node.prev:
            node.prev.next = node.next
        else:
            self.head = node.next
        
        if node.next:
            node.next.prev = node.prev
```

---

### 3️⃣ Circular Linked List
Last node points back to the head.

```python
# Visual: 1 → 2 → 3 → 4 → back to 1

def is_circular(head):
    if not head:
        return False
    
    slow = fast = head
    
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        
        if slow == fast:
            return True
    
    return False
```

---

## Common Patterns

### Pattern 1: Two Pointer / Fast-Slow Pointer
**Use Cases**: Cycle detection, finding middle, nth from end

```python
# Find middle of linked list
def find_middle(head):
    slow = fast = head
    
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    
    return slow

# Detect cycle
def has_cycle(head):
    slow = fast = head
    
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        
        if slow == fast:
            return True
    
    return False

# Find cycle start
def detect_cycle(head):
    slow = fast = head
    
    # Detect cycle
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        
        if slow == fast:
            break
    else:
        return None
    
    # Find start of cycle
    slow = head
    while slow != fast:
        slow = slow.next
        fast = fast.next
    
    return slow

# Nth node from end
def nth_from_end(head, n):
    fast = slow = head
    
    # Move fast n steps ahead
    for _ in range(n):
        if not fast:
            return None
        fast = fast.next
    
    # Move both together
    while fast:
        slow = slow.next
        fast = fast.next
    
    return slow
```

**Visual - Finding Middle:**
```
Step 1: S     F
        1 → 2 → 3 → 4 → 5 → None

Step 2:     S         F
        1 → 2 → 3 → 4 → 5 → None

Step 3:         S              F
        1 → 2 → 3 → 4 → 5 → None

Result: Middle = 3 ✓
```

---

### Pattern 2: Reversal
**Use Cases**: Reverse list, reverse in groups

```python
# Reverse entire list - Iterative
def reverse_list(head):
    prev = None
    current = head
    
    while current:
        next_node = current.next
        current.next = prev
        prev = current
        current = next_node
    
    return prev

# Reverse entire list - Recursive
def reverse_list_recursive(head):
    if not head or not head.next:
        return head
    
    new_head = reverse_list_recursive(head.next)
    head.next.next = head
    head.next = None
    
    return new_head

# Reverse in groups of k
def reverse_k_group(head, k):
    # Count nodes
    count = 0
    current = head
    while current and count < k:
        current = current.next
        count += 1
    
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
    
    # Recurse for remaining list
    head.next = reverse_k_group(current, k)
    
    return prev

# Reverse between positions left and right
def reverse_between(head, left, right):
    if not head or left == right:
        return head
    
    dummy = ListNode(0)
    dummy.next = head
    prev = dummy
    
    # Move to position left
    for _ in range(left - 1):
        prev = prev.next
    
    # Reverse from left to right
    current = prev.next
    for _ in range(right - left):
        temp = current.next
        current.next = temp.next
        temp.next = prev.next
        prev.next = temp
    
    return dummy.next
```

**Visual - Reverse List:**
```
Original: 1 → 2 → 3 → 4 → 5 → None

Step 1: None ← 1   2 → 3 → 4 → 5 → None
        prev  curr next

Step 2: None ← 1 ← 2   3 → 4 → 5 → None
              prev curr next

Step 3: None ← 1 ← 2 ← 3   4 → 5 → None
                    prev curr next

Final:  None ← 1 ← 2 ← 3 ← 4 ← 5
                              head
```

---

### Pattern 3: Merge
**Use Cases**: Merge two sorted lists, merge k sorted lists

```python
# Merge two sorted lists
def merge_two_lists(l1, l2):
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

# Merge k sorted lists using divide and conquer
def merge_k_lists(lists):
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

# Using Min Heap
import heapq

def merge_k_lists_heap(lists):
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
```

---

### Pattern 4: Reorder/Rearrange
**Use Cases**: Reorder list, odd-even linked list

```python
# Reorder list: L0 → Ln → L1 → Ln-1 → L2 → Ln-2 → ...
def reorder_list(head):
    if not head or not head.next:
        return
    
    # Find middle
    slow = fast = head
    while fast.next and fast.next.next:
        slow = slow.next
        fast = fast.next.next
    
    # Reverse second half
    second = slow.next
    slow.next = None
    second = reverse_list(second)
    
    # Merge two halves
    first = head
    while second:
        temp1 = first.next
        temp2 = second.next
        
        first.next = second
        second.next = temp1
        
        first = temp1
        second = temp2

# Odd-Even Linked List
def odd_even_list(head):
    if not head or not head.next:
        return head
    
    odd = head
    even = head.next
    even_head = even
    
    while even and even.next:
        odd.next = even.next
        odd = odd.next
        even.next = odd.next
        even = even.next
    
    odd.next = even_head
    return head

# Swap pairs
def swap_pairs(head):
    dummy = ListNode(0)
    dummy.next = head
    prev = dummy
    
    while prev.next and prev.next.next:
        first = prev.next
        second = first.next
        
        first.next = second.next
        second.next = first
        prev.next = second
        
        prev = first
    
    return dummy.next
```

**Visual - Reorder List:**
```
Original: 1 → 2 → 3 → 4 → 5

Step 1: Find middle
        1 → 2 → 3 → 4 → 5
                ↑ middle

Step 2: Reverse second half
        1 → 2 → 3    5 → 4
        
Step 3: Merge alternately
        1 → 5 → 2 → 4 → 3 ✓
```

---

### Pattern 5: Intersection & Union
**Use Cases**: Find intersection, remove duplicates

```python
# Find intersection of two linked lists
def get_intersection_node(headA, headB):
    if not headA or not headB:
        return None
    
    a, b = headA, headB
    
    # Traverse both lists
    while a != b:
        a = a.next if a else headB
        b = b.next if b else headA
    
    return a

# Remove duplicates from sorted list
def delete_duplicates(head):
    current = head
    
    while current and current.next:
        if current.val == current.next.val:
            current.next = current.next.next
        else:
            current = current.next
    
    return head

# Remove duplicates from unsorted list
def remove_duplicates_unsorted(head):
    seen = set()
    current = head
    prev = None
    
    while current:
        if current.val in seen:
            prev.next = current.next
        else:
            seen.add(current.val)
            prev = current
        current = current.next
    
    return head
```

---

### Pattern 6: Sorting
**Use Cases**: Sort list, insertion sort

```python
# Merge sort on linked list
def sort_list(head):
    if not head or not head.next:
        return head
    
    # Find middle
    slow = fast = head
    prev = None
    
    while fast and fast.next:
        prev = slow
        slow = slow.next
        fast = fast.next.next
    
    prev.next = None
    
    # Recursively sort both halves
    left = sort_list(head)
    right = sort_list(slow)
    
    # Merge
    return merge_two_lists(left, right)

# Insertion sort on linked list
def insertion_sort_list(head):
    dummy = ListNode(0)
    current = head
    
    while current:
        prev = dummy
        next_node = current.next
        
        # Find position to insert
        while prev.next and prev.next.val < current.val:
            prev = prev.next
        
        # Insert current
        current.next = prev.next
        prev.next = current
        
        current = next_node
    
    return dummy.next
```

---

## Essential Techniques

### 1. Dummy Node Pattern
Use a dummy node to simplify edge cases.

```python
def remove_elements(head, val):
    dummy = ListNode(0)
    dummy.next = head
    current = dummy
    
    while current.next:
        if current.next.val == val:
            current.next = current.next.next
        else:
            current = current.next
    
    return dummy.next
```

### 2. Runner Technique
Use two pointers moving at different speeds.

```python
def is_palindrome(head):
    # Find middle using fast-slow
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    
    # Reverse second half
    second = reverse_list(slow)
    
    # Compare
    first = head
    while second:
        if first.val != second.val:
            return False
        first = first.next
        second = second.next
    
    return True
```

### 3. Recursion
Useful for reversal and tree-like operations.

```python
def reverse_print(head):
    if not head:
        return
    
    reverse_print(head.next)
    print(head.val)
```

---

## 🎨 Dry Run Examples

### Example 1: Detect Cycle

```
List with cycle:
1 → 2 → 3 → 4 → 5
        ↑       ↓
        8 ← 7 ← 6

Floyd's Cycle Detection:

Step 1: S   F
        1 → 2 → 3 → 4 → 5
                        ↓
                        6

Step 2:     S       F
        1 → 2 → 3 → 4 → 5
                        ↓
                        6

Step 3:         S           F
        1 → 2 → 3 → 4 → 5
            ↑           ↓
            8 ← 7 ← 6

Step 4:             S   F
        They meet! Cycle detected ✓

Finding Cycle Start:
Reset slow to head
Move both one step at a time
They meet at start of cycle (node 3)
```

---

### Example 2: Reverse in K Groups

```
Original: 1 → 2 → 3 → 4 → 5 → 6 → 7 → 8
K = 3

Step 1: Reverse first 3
        3 → 2 → 1 → 4 → 5 → 6 → 7 → 8

Step 2: Reverse next 3
        3 → 2 → 1 → 6 → 5 → 4 → 7 → 8

Step 3: Last group has < 3 nodes, keep as is
        3 → 2 → 1 → 6 → 5 → 4 → 7 → 8 ✓
```

---

### Example 3: Merge Two Sorted Lists

```
L1: 1 → 3 → 5 → 7
L2: 2 → 4 → 6 → 8

Step 1: Compare 1 and 2
        Result: 1
        L1: 3 → 5 → 7
        L2: 2 → 4 → 6 → 8

Step 2: Compare 3 and 2
        Result: 1 → 2
        L1: 3 → 5 → 7
        L2: 4 → 6 → 8

Continue...
Final: 1 → 2 → 3 → 4 → 5 → 6 → 7 → 8 ✓
```

---

## ⏱️ Time & Space Complexity

| Operation | Singly LL | Doubly LL | Array |
|-----------|-----------|-----------|-------|
| Access | O(n) | O(n) | O(1) |
| Search | O(n) | O(n) | O(n) |
| Insert at head | O(1) | O(1) | O(n) |
| Insert at tail | O(n) | O(1)* | O(1) |
| Insert at middle | O(n) | O(n) | O(n) |
| Delete at head | O(1) | O(1) | O(n) |
| Delete at tail | O(n) | O(1)* | O(1) |
| Delete at middle | O(n) | O(n) | O(n) |

*with tail pointer

---

## 🎯 Common Pitfalls

1. **Forgetting to check for null/None**
   ```python
   # Wrong
   if head.next.val == x:
   
   # Right
   if head and head.next and head.next.val == x:
   ```

2. **Losing references**
   ```python
   # Wrong
   head = head.next  # Lost original head
   
   # Right
   current = head
   current = current.next
   ```

3. **Not handling edge cases**
   - Empty list
   - Single node
   - Two nodes

---

## 📊 Interview Patterns Recognition

| Problem Type | Pattern to Use |
|-------------|----------------|
| Find middle | Fast-slow pointer |
| Detect cycle | Floyd's algorithm |
| Nth from end | Two pointer with gap |
| Merge lists | Two pointer |
| Reverse | Three pointer |
| Palindrome | Fast-slow + Reverse |
| Sort | Merge sort |
| Remove duplicates | Hash set or two pointer |

---

## 🔗 Must-Know Problems

### Easy
- [206. Reverse Linked List](https://leetcode.com/problems/reverse-linked-list/)
- [21. Merge Two Sorted Lists](https://leetcode.com/problems/merge-two-sorted-lists/)
- [141. Linked List Cycle](https://leetcode.com/problems/linked-list-cycle/)
- [83. Remove Duplicates from Sorted List](https://leetcode.com/problems/remove-duplicates-from-sorted-list/)
- [876. Middle of the Linked List](https://leetcode.com/problems/middle-of-the-linked-list/)

### Medium
- [2. Add Two Numbers](https://leetcode.com/problems/add-two-numbers/)
- [19. Remove Nth Node From End](https://leetcode.com/problems/remove-nth-node-from-end-of-list/)
- [142. Linked List Cycle II](https://leetcode.com/problems/linked-list-cycle-ii/)
- [143. Reorder List](https://leetcode.com/problems/reorder-list/)
- [148. Sort List](https://leetcode.com/problems/sort-list/)
- [328. Odd Even Linked List](https://leetcode.com/problems/odd-even-linked-list/)

### Hard
- [23. Merge k Sorted Lists](https://leetcode.com/problems/merge-k-sorted-lists/)
- [25. Reverse Nodes in k-Group](https://leetcode.com/problems/reverse-nodes-in-k-group/)
- [146. LRU Cache](https://leetcode.com/problems/lru-cache/)

---

## 🎓 Pro Tips

1. **Always use dummy node** for problems involving head manipulation
2. **Draw diagrams** - visual representation helps avoid errors
3. **Check edge cases** - empty, single node, two nodes
4. **Practice pointer manipulation** - it's the core skill
5. **Master fast-slow pointer** - solves many problems elegantly
6. **Remember to return correct head** - especially after modifications

---

**Master these patterns and you'll ace any linked list interview question! 🚀**
