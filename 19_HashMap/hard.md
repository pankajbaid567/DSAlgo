# 🔥 HashMap - Hard Problems Collection

A comprehensive collection of challenging HashMap problems with advanced data structure designs, caching algorithms, and detailed explanations.

---

## 📚 Table of Contents

1. [LRU Cache](#problem-1-lru-cache)
2. [LFU Cache](#problem-2-lfu-cache)
3. [All O`one Data Structure](#problem-3-all-oone-data-structure)
4. [Design Search Autocomplete System](#problem-4-design-search-autocomplete-system)
5. [Time Based Key-Value Store](#problem-5-time-based-key-value-store)
6. [Insert Delete GetRandom O(1)](#problem-6-insert-delete-getrandom-o1)
7. [Pattern Summary](#-pattern-summary)

---

## Problem 1: LRU Cache

**LeetCode 146 - Medium/Hard**

### Problem Statement
Design LRU (Least Recently Used) cache with O(1) get and put operations.

```
Input:
["LRUCache", "put", "put", "get", "put", "get", "put", "get", "get", "get"]
[[2], [1,1], [2,2], [1], [3,3], [2], [4,4], [1], [3], [4]]

Output:
[null, null, null, 1, null, -1, null, -1, 3, 4]
```

### 🎯 Intuition
**HashMap + Doubly Linked List:**
- HashMap: O(1) access to nodes
- Doubly Linked List: O(1) add/remove at both ends
- Most recent at head, least recent at tail
- On access: move to head
- On capacity: remove tail

### 📊 Visual Representation

```
LRU Cache (capacity=2):

Initial: head ↔ tail

After put(1,1):
  head ↔ [1:1] ↔ tail

After put(2,2):
  head ↔ [2:2] ↔ [1:1] ↔ tail

After get(1):  // Move 1 to front
  head ↔ [1:1] ↔ [2:2] ↔ tail

After put(3,3):  // Remove LRU (2)
  head ↔ [3:3] ↔ [1:1] ↔ tail
```

### Solution

```python
class Node:
    def __init__(self, key=0, val=0):
        self.key = key
        self.val = val
        self.prev = None
        self.next = None

class LRUCache:
    """
    LRU Cache using HashMap + Doubly Linked List.
    
    Operations:
    - get(key): O(1)
    - put(key, value): O(1)
    
    Space: O(capacity)
    """
    
    def __init__(self, capacity: int):
        self.capacity = capacity
        self.cache = {}  # key -> Node
        
        # Dummy head and tail
        self.head = Node()
        self.tail = Node()
        self.head.next = self.tail
        self.tail.prev = self.head
    
    def _remove(self, node):
        """Remove node from list."""
        prev = node.prev
        nxt = node.next
        prev.next = nxt
        nxt.prev = prev
    
    def _add_to_head(self, node):
        """Add node right after head."""
        node.prev = self.head
        node.next = self.head.next
        self.head.next.prev = node
        self.head.next = node
    
    def _move_to_head(self, node):
        """Move existing node to head."""
        self._remove(node)
        self._add_to_head(node)
    
    def get(self, key: int) -> int:
        if key not in self.cache:
            return -1
        
        node = self.cache[key]
        self._move_to_head(node)
        return node.val
    
    def put(self, key: int, value: int) -> None:
        if key in self.cache:
            # Update existing
            node = self.cache[key]
            node.val = value
            self._move_to_head(node)
        else:
            # Add new
            node = Node(key, value)
            self.cache[key] = node
            self._add_to_head(node)
            
            # Check capacity
            if len(self.cache) > self.capacity:
                # Remove LRU (tail.prev)
                lru = self.tail.prev
                self._remove(lru)
                del self.cache[lru.key]

# Example usage
lru = LRUCache(2)
lru.put(1, 1)
lru.put(2, 2)
print(lru.get(1))    # 1
lru.put(3, 3)
print(lru.get(2))    # -1
```

### Alternative: OrderedDict

```python
from collections import OrderedDict

class LRUCache:
    """
    Using Python's OrderedDict.
    
    Time: O(1) for get and put
    Space: O(capacity)
    """
    
    def __init__(self, capacity: int):
        self.cache = OrderedDict()
        self.capacity = capacity
    
    def get(self, key: int) -> int:
        if key not in self.cache:
            return -1
        
        # Move to end (most recent)
        self.cache.move_to_end(key)
        return self.cache[key]
    
    def put(self, key: int, value: int) -> None:
        if key in self.cache:
            self.cache.move_to_end(key)
        
        self.cache[key] = value
        
        if len(self.cache) > self.capacity:
            # Remove first (least recent)
            self.cache.popitem(last=False)
```

### ⏱️ Complexity Analysis

| Operation | Time | Space | Notes |
|-----------|------|-------|-------|
| get | O(1) | - | HashMap lookup + list ops |
| put | O(1) | - | HashMap insert + list ops |
| Space | - | O(capacity) | Store at most capacity items |

---

## Problem 2: LFU Cache

**LeetCode 460 - Hard**

### Problem Statement
Design LFU (Least Frequently Used) cache with O(1) operations. If tie, evict least recently used.

```
Input:
["LFUCache", "put", "put", "get", "put", "get", "get", "put", "get", "get", "get"]
[[2], [1,1], [2,2], [1], [3,3], [2], [3], [4,4], [1], [3], [4]]

Output:
[null, null, null, 1, null, -1, 3, null, -1, 3, 4]
```

### 🎯 Intuition
**Three data structures:**
1. HashMap: key → (value, frequency)
2. HashMap: frequency → ordered list of keys
3. Variable: min_frequency

### Solution

```python
from collections import defaultdict

class Node:
    def __init__(self, key, val):
        self.key = key
        self.val = val
        self.freq = 1
        self.prev = None
        self.next = None

class DLL:
    """Doubly Linked List for maintaining order."""
    def __init__(self):
        self.head = Node(0, 0)
        self.tail = Node(0, 0)
        self.head.next = self.tail
        self.tail.prev = self.head
        self.size = 0
    
    def add(self, node):
        """Add to head (most recent)."""
        node.next = self.head.next
        node.prev = self.head
        self.head.next.prev = node
        self.head.next = node
        self.size += 1
    
    def remove(self, node):
        """Remove node."""
        node.prev.next = node.next
        node.next.prev = node.prev
        self.size -= 1
    
    def remove_tail(self):
        """Remove and return tail (least recent)."""
        if self.size == 0:
            return None
        node = self.tail.prev
        self.remove(node)
        return node

class LFUCache:
    """
    LFU Cache with O(1) operations.
    
    Data structures:
    - key_node: key -> Node
    - freq_list: frequency -> DLL of nodes
    - min_freq: minimum frequency
    
    Time: O(1) for all operations
    Space: O(capacity)
    """
    
    def __init__(self, capacity: int):
        self.capacity = capacity
        self.min_freq = 0
        self.key_node = {}  # key -> Node
        self.freq_list = defaultdict(DLL)  # freq -> DLL
    
    def _update_freq(self, node):
        """Update node frequency."""
        freq = node.freq
        
        # Remove from current frequency list
        self.freq_list[freq].remove(node)
        
        # Update min_freq if needed
        if freq == self.min_freq and self.freq_list[freq].size == 0:
            self.min_freq += 1
        
        # Add to new frequency list
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
            # Update existing
            node = self.key_node[key]
            node.val = value
            self._update_freq(node)
        else:
            # Add new
            if len(self.key_node) >= self.capacity:
                # Evict LFU (and LRU among LFU)
                lfu_list = self.freq_list[self.min_freq]
                remove_node = lfu_list.remove_tail()
                del self.key_node[remove_node.key]
            
            # Add new node
            node = Node(key, value)
            self.key_node[key] = node
            self.freq_list[1].add(node)
            self.min_freq = 1

# Example usage
lfu = LFUCache(2)
lfu.put(1, 1)
lfu.put(2, 2)
print(lfu.get(1))    # 1
lfu.put(3, 3)        # Evict key 2
print(lfu.get(2))    # -1
print(lfu.get(3))    # 3
```

### ⏱️ Complexity Analysis

| Operation | Time | Space | Notes |
|-----------|------|-------|-------|
| get | O(1) | - | ⭐ Constant time |
| put | O(1) | - | ⭐ Constant time |
| Space | - | O(capacity) | Multiple structures |

---

## Problem 3: All O`one Data Structure

**LeetCode 432 - Hard**

### Problem Statement
Design data structure supporting:
- inc(key): O(1)
- dec(key): O(1)
- getMaxKey(): O(1)
- getMinKey(): O(1)

### Solution

```python
class Node:
    def __init__(self, count):
        self.count = count
        self.keys = set()
        self.prev = None
        self.next = None

class AllOne:
    """
    All O(1) data structure.
    
    Structure:
    - HashMap: key -> count
    - Doubly Linked List: sorted by count
    - Each node has set of keys with that count
    
    Time: O(1) for all operations
    Space: O(n) where n = unique keys
    """
    
    def __init__(self):
        self.key_count = {}  # key -> count
        self.count_node = {}  # count -> Node
        
        # Dummy head and tail
        self.head = Node(float('-inf'))
        self.tail = Node(float('inf'))
        self.head.next = self.tail
        self.tail.prev = self.head
    
    def _add_node_after(self, new_node, prev_node):
        """Insert new_node after prev_node."""
        new_node.prev = prev_node
        new_node.next = prev_node.next
        prev_node.next.prev = new_node
        prev_node.next = new_node
        self.count_node[new_node.count] = new_node
    
    def _remove_node(self, node):
        """Remove node from list."""
        node.prev.next = node.next
        node.next.prev = node.prev
        del self.count_node[node.count]
    
    def inc(self, key: str) -> None:
        if key not in self.key_count:
            # New key with count 1
            self.key_count[key] = 1
            
            if 1 not in self.count_node:
                self._add_node_after(Node(1), self.head)
            
            self.count_node[1].keys.add(key)
        else:
            # Increment existing key
            count = self.key_count[key]
            node = self.count_node[count]
            node.keys.remove(key)
            
            # Move to next count
            new_count = count + 1
            self.key_count[key] = new_count
            
            if new_count not in self.count_node:
                self._add_node_after(Node(new_count), node)
            
            self.count_node[new_count].keys.add(key)
            
            # Remove old node if empty
            if len(node.keys) == 0:
                self._remove_node(node)
    
    def dec(self, key: str) -> None:
        if key not in self.key_count:
            return
        
        count = self.key_count[key]
        node = self.count_node[count]
        node.keys.remove(key)
        
        if count == 1:
            # Remove key completely
            del self.key_count[key]
        else:
            # Move to previous count
            new_count = count - 1
            self.key_count[key] = new_count
            
            if new_count not in self.count_node:
                self._add_node_after(Node(new_count), node.prev)
            
            self.count_node[new_count].keys.add(key)
        
        # Remove old node if empty
        if len(node.keys) == 0:
            self._remove_node(node)
    
    def getMaxKey(self) -> str:
        if self.tail.prev == self.head:
            return ""
        return next(iter(self.tail.prev.keys))
    
    def getMinKey(self) -> str:
        if self.head.next == self.tail:
            return ""
        return next(iter(self.head.next.keys))

# Example usage
obj = AllOne()
obj.inc("hello")
obj.inc("hello")
print(obj.getMaxKey())  # "hello"
print(obj.getMinKey())  # "hello"
obj.inc("world")
print(obj.getMinKey())  # "world"
```

### ⏱️ Complexity Analysis

| Operation | Time | Notes |
|-----------|------|-------|
| inc | O(1) | ⭐ Constant |
| dec | O(1) | ⭐ Constant |
| getMaxKey | O(1) | ⭐ Constant |
| getMinKey | O(1) | ⭐ Constant |

---

## Problem 4: Design Search Autocomplete System

**LeetCode 642 - Hard**

### Problem Statement
Design search autocomplete returning top 3 results by frequency (tie-break by lexicographic order).

### Solution

```python
from collections import defaultdict
import heapq

class AutocompleteSystem:
    """
    Trie + Heap for autocomplete.
    
    Operations:
    - input(c): O(k log k) where k = candidates
    
    Space: O(n*m) where n = sentences, m = avg length
    """
    
    def __init__(self, sentences, times):
        self.trie = {}
        self.freq = defaultdict(int)
        self.current = ""
        
        # Build trie
        for sentence, time in zip(sentences, times):
            self.freq[sentence] = time
            self._add_to_trie(sentence)
    
    def _add_to_trie(self, sentence):
        """Add sentence to trie."""
        node = self.trie
        for char in sentence:
            if char not in node:
                node[char] = {}
            node = node[char]
        node['#'] = sentence
    
    def _get_all_sentences(self, node):
        """Get all sentences from trie node."""
        results = []
        
        if '#' in node:
            results.append(node['#'])
        
        for key in node:
            if key != '#':
                results.extend(self._get_all_sentences(node[key]))
        
        return results
    
    def input(self, c: str):
        if c == '#':
            # Save current sentence
            self.freq[self.current] += 1
            self._add_to_trie(self.current)
            self.current = ""
            return []
        
        self.current += c
        
        # Find candidates
        node = self.trie
        for char in self.current:
            if char not in node:
                return []
            node = node[char]
        
        candidates = self._get_all_sentences(node)
        
        # Sort by frequency (desc) then lexicographically
        # Use heap with custom comparator
        heap = []
        for sentence in candidates:
            heapq.heappush(heap, (-self.freq[sentence], sentence))
        
        # Get top 3
        result = []
        for _ in range(min(3, len(heap))):
            freq, sentence = heapq.heappop(heap)
            result.append(sentence)
        
        return result

# Example usage
system = AutocompleteSystem(
    ["i love you", "island", "ironman", "i love leetcode"],
    [5, 3, 2, 2]
)
print(system.input('i'))  # ["i love you", "island", "i love leetcode"]
print(system.input(' '))  # ["i love you", "i love leetcode"]
print(system.input('a'))  # []
print(system.input('#'))  # []
```

### ⏱️ Complexity Analysis

| Operation | Time | Space | Notes |
|-----------|------|-------|-------|
| input | O(k log k) | O(n*m) | k = candidates |

---

## Problem 5: Time Based Key-Value Store

**LeetCode 981 - Medium/Hard**

### Problem Statement
Store key-value pairs with timestamp, retrieve value at given timestamp (latest ≤ timestamp).

### Solution

```python
from collections import defaultdict
import bisect

class TimeMap:
    """
    HashMap + Binary Search.
    
    Operations:
    - set: O(1)
    - get: O(log n) where n = timestamps for key
    
    Space: O(n*m) where n = keys, m = timestamps per key
    """
    
    def __init__(self):
        self.store = defaultdict(list)  # key -> [(timestamp, value), ...]
    
    def set(self, key: str, value: str, timestamp: int) -> None:
        self.store[key].append((timestamp, value))
    
    def get(self, key: str, timestamp: int) -> str:
        if key not in self.store:
            return ""
        
        pairs = self.store[key]
        
        # Binary search for largest timestamp <= given timestamp
        idx = bisect.bisect_right(pairs, (timestamp, chr(127)))
        
        if idx == 0:
            return ""
        
        return pairs[idx - 1][1]

# Example usage
tm = TimeMap()
tm.set("foo", "bar", 1)
print(tm.get("foo", 1))   # "bar"
print(tm.get("foo", 3))   # "bar"
tm.set("foo", "bar2", 4)
print(tm.get("foo", 4))   # "bar2"
print(tm.get("foo", 5))   # "bar2"
```

### ⏱️ Complexity Analysis

| Operation | Time | Notes |
|-----------|------|-------|
| set | O(1) | Append to list |
| get | O(log n) | Binary search |

---

## Problem 6: Insert Delete GetRandom O(1)

**LeetCode 380 - Medium/Hard**

### Problem Statement
Design data structure with O(1) insert, delete, and getRandom.

### Solution

```python
import random

class RandomizedSet:
    """
    HashMap + Dynamic Array.
    
    Operations:
    - insert: O(1) average
    - remove: O(1) average
    - getRandom: O(1)
    
    Space: O(n)
    """
    
    def __init__(self):
        self.val_to_idx = {}  # value -> index in list
        self.vals = []
    
    def insert(self, val: int) -> bool:
        if val in self.val_to_idx:
            return False
        
        self.val_to_idx[val] = len(self.vals)
        self.vals.append(val)
        return True
    
    def remove(self, val: int) -> bool:
        if val not in self.val_to_idx:
            return False
        
        # Swap with last element and pop
        idx = self.val_to_idx[val]
        last_val = self.vals[-1]
        
        self.vals[idx] = last_val
        self.val_to_idx[last_val] = idx
        
        self.vals.pop()
        del self.val_to_idx[val]
        
        return True
    
    def getRandom(self) -> int:
        return random.choice(self.vals)

# Example usage
rs = RandomizedSet()
print(rs.insert(1))    # True
print(rs.remove(2))    # False
print(rs.insert(2))    # True
print(rs.getRandom())  # 1 or 2
print(rs.remove(1))    # True
print(rs.getRandom())  # 2
```

### With Duplicates (LC 381)

```python
from collections import defaultdict
import random

class RandomizedCollection:
    """
    Allow duplicates while maintaining O(1) operations.
    
    Space: O(n)
    """
    
    def __init__(self):
        self.val_to_indices = defaultdict(set)
        self.vals = []
    
    def insert(self, val: int) -> bool:
        self.val_to_indices[val].add(len(self.vals))
        self.vals.append(val)
        return len(self.val_to_indices[val]) == 1
    
    def remove(self, val: int) -> bool:
        if not self.val_to_indices[val]:
            return False
        
        # Get any index of val
        remove_idx = self.val_to_indices[val].pop()
        last_val = self.vals[-1]
        
        # Swap with last
        self.vals[remove_idx] = last_val
        self.val_to_indices[last_val].add(remove_idx)
        self.val_to_indices[last_val].discard(len(self.vals) - 1)
        
        self.vals.pop()
        return True
    
    def getRandom(self) -> int:
        return random.choice(self.vals)
```

### ⏱️ Complexity Analysis

| Operation | Time | Notes |
|-----------|------|-------|
| insert | O(1) | ⭐ Average case |
| remove | O(1) | ⭐ Average case |
| getRandom | O(1) | ⭐ Constant |

---

## 🎯 Pattern Summary

### Core HashMap Design Patterns

1. **Cache with Eviction** - LRU/LFU with multiple data structures
2. **Frequency Tracking** - HashMap + Linked List/Buckets
3. **Trie + HashMap** - Autocomplete, prefix matching
4. **HashMap + Array** - Random access with O(1) ops
5. **HashMap + Binary Search** - Time-based queries

### Problem Categories

| Category | Problems | Key Technique |
|----------|----------|---------------|
| Caching | LRU, LFU | HashMap + DLL |
| Frequency | All O'one | HashMap + sorted buckets |
| Prefix Search | Autocomplete | Trie + heap |
| Time-based | TimeMap | HashMap + binary search |
| Random Access | Insert/Delete/Random | HashMap + array |

### Design Patterns

```python
# 1. LRU Pattern (HashMap + DLL)
class LRU:
    def __init__(self):
        self.cache = {}
        self.dll = DoublyLinkedList()

# 2. Frequency Bucketing
class FrequencyTracker:
    def __init__(self):
        self.freq_buckets = {}  # freq -> items
        self.item_freq = {}     # item -> freq

# 3. HashMap + Array for Random
class RandomDS:
    def __init__(self):
        self.map = {}   # val -> index
        self.list = []  # values

# 4. Time-based with Binary Search
class TimeBased:
    def __init__(self):
        self.data = {}  # key -> [(time, val), ...]
```

### Common Techniques

1. **Doubly Linked List** - O(1) add/remove at both ends
2. **Dummy Nodes** - Simplify edge cases
3. **Swap with Last** - O(1) delete from array
4. **Multiple HashMaps** - Track bidirectional mappings
5. **Binary Search on Sorted** - Time-based queries

### Interview Tips

1. **Identify requirements:** What operations need O(1)?
2. **Choose structure:** Single HashMap sufficient? Need ordering?
3. **Handle edge cases:** Empty, capacity, duplicates
4. **Trade-offs:** Time vs space complexity
5. **Test thoroughly:** Edge cases, capacity limits

Master these HashMap design patterns for system design success! 🚀

