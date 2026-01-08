# 🗺️ HashMap & HashSet - Comprehensive Cheatsheet

## 📚 Core Concepts

**HashMap**: Key-value pairs with O(1) average lookup, insert, delete.  
**HashSet**: Collection of unique elements with O(1) operations.

### When to Use
✅ **Fast lookups** by key  
✅ **Counting frequencies**  
✅ **Detecting duplicates**  
✅ **Caching/Memoization**  
✅ **Grouping data**  

---

## Python Hash Structures

### Dictionary (HashMap)
```python
# Creation
d = {}
d = dict()
d = {'key': 'value'}

# Operations
d['key'] = 'value'      # Insert/Update: O(1)
value = d['key']        # Access: O(1)
value = d.get('key', default)  # Safe access
del d['key']            # Delete: O(1)
'key' in d              # Check existence: O(1)

# Iteration
for key in d:           # Iterate keys
for value in d.values(): # Iterate values
for key, value in d.items(): # Iterate pairs

# Methods
d.keys()                # All keys
d.values()              # All values
d.items()               # All (key, value) pairs
d.pop('key')            # Remove and return
d.clear()               # Remove all
d.update({'k': 'v'})    # Merge dictionaries
```

### Set (HashSet)
```python
# Creation
s = set()
s = {1, 2, 3}

# Operations
s.add(elem)             # Add: O(1)
s.remove(elem)          # Remove (raises error): O(1)
s.discard(elem)         # Remove (no error): O(1)
elem in s               # Check: O(1)

# Set operations
s1 | s2                 # Union
s1 & s2                 # Intersection
s1 - s2                 # Difference
s1 ^ s2                 # Symmetric difference

# Methods
s.clear()               # Remove all
s.pop()                 # Remove arbitrary element
```

### Counter (from collections)
```python
from collections import Counter

# Creation
c = Counter([1, 2, 2, 3, 3, 3])
# Counter({3: 3, 2: 2, 1: 1})

# Operations
c['item']               # Get count
c.most_common(n)        # n most common elements
c.update([1, 2])        # Add elements
c.subtract([1, 2])      # Subtract elements
c1 + c2                 # Add counters
c1 - c2                 # Subtract counters
```

### DefaultDict
```python
from collections import defaultdict

# Automatically creates default value for missing keys
d = defaultdict(int)     # Default: 0
d = defaultdict(list)    # Default: []
d = defaultdict(set)     # Default: set()

# Usage
d['key'] += 1            # No KeyError!
d['key'].append(item)    # No KeyError!
```

---

## Pattern 1: Frequency Counting

### Most Common Element
```python
from collections import Counter

def mostCommonElement(arr):
    count = Counter(arr)
    return count.most_common(1)[0][0]

# Alternative: Manual counting
def mostCommon_manual(arr):
    count = {}
    max_count = 0
    result = None
    
    for num in arr:
        count[num] = count.get(num, 0) + 1
        if count[num] > max_count:
            max_count = count[num]
            result = num
    
    return result
```

### Top K Frequent Elements
```python
import heapq
from collections import Counter

def topKFrequent(nums, k):
    """Find k most frequent elements"""
    count = Counter(nums)
    
    # Method 1: Using heap
    return heapq.nlargest(k, count.keys(), key=count.get)
    
    # Method 2: Using bucket sort
    # bucket = [[] for _ in range(len(nums) + 1)]
    # for num, freq in count.items():
    #     bucket[freq].append(num)
    # 
    # result = []
    # for i in range(len(bucket) - 1, -1, -1):
    #     result.extend(bucket[i])
    #     if len(result) >= k:
    #         return result[:k]
```

### First Unique Character
```python
def firstUniqChar(s):
    """Find first non-repeating character"""
    count = {}
    
    # Count frequencies
    for char in s:
        count[char] = count.get(char, 0) + 1
    
    # Find first unique
    for i, char in enumerate(s):
        if count[char] == 1:
            return i
    
    return -1
```

---

## Pattern 2: Two Sum Variants

### Two Sum
```python
def twoSum(nums, target):
    """Find two numbers that add up to target"""
    seen = {}
    
    for i, num in enumerate(nums):
        complement = target - num
        
        if complement in seen:
            return [seen[complement], i]
        
        seen[num] = i
    
    return []
```

### Three Sum
```python
def threeSum(nums):
    """Find all unique triplets that sum to 0"""
    nums.sort()
    result = []
    
    for i in range(len(nums) - 2):
        # Skip duplicates
        if i > 0 and nums[i] == nums[i-1]:
            continue
        
        # Two sum on remaining array
        seen = set()
        target = -nums[i]
        
        for j in range(i + 1, len(nums)):
            complement = target - nums[j]
            
            if complement in seen:
                result.append([nums[i], complement, nums[j]])
                # Skip duplicates
                while j + 1 < len(nums) and nums[j] == nums[j+1]:
                    j += 1
            
            seen.add(nums[j])
    
    return result
```

### Four Sum
```python
def fourSum(nums, target):
    """Find all unique quadruplets"""
    nums.sort()
    result = []
    n = len(nums)
    
    for i in range(n - 3):
        if i > 0 and nums[i] == nums[i-1]:
            continue
        
        for j in range(i + 1, n - 2):
            if j > i + 1 and nums[j] == nums[j-1]:
                continue
            
            # Two pointers for remaining two elements
            left, right = j + 1, n - 1
            
            while left < right:
                total = nums[i] + nums[j] + nums[left] + nums[right]
                
                if total == target:
                    result.append([nums[i], nums[j], nums[left], nums[right]])
                    
                    while left < right and nums[left] == nums[left+1]:
                        left += 1
                    while left < right and nums[right] == nums[right-1]:
                        right -= 1
                    
                    left += 1
                    right -= 1
                elif total < target:
                    left += 1
                else:
                    right -= 1
    
    return result
```

---

## Pattern 3: Grouping & Mapping

### Group Anagrams
```python
from collections import defaultdict

def groupAnagrams(strs):
    """Group strings that are anagrams"""
    groups = defaultdict(list)
    
    for s in strs:
        # Sort string as key
        key = ''.join(sorted(s))
        groups[key].append(s)
    
    return list(groups.values())

# Alternative: Use character count as key
def groupAnagrams_count(strs):
    groups = defaultdict(list)
    
    for s in strs:
        count = [0] * 26
        for char in s:
            count[ord(char) - ord('a')] += 1
        
        key = tuple(count)
        groups[key].append(s)
    
    return list(groups.values())
```

### Isomorphic Strings
```python
def isIsomorphic(s, t):
    """Check if strings are isomorphic"""
    if len(s) != len(t):
        return False
    
    s_to_t = {}
    t_to_s = {}
    
    for c1, c2 in zip(s, t):
        if c1 in s_to_t:
            if s_to_t[c1] != c2:
                return False
        else:
            s_to_t[c1] = c2
        
        if c2 in t_to_s:
            if t_to_s[c2] != c1:
                return False
        else:
            t_to_s[c2] = c1
    
    return True
```

### Word Pattern
```python
def wordPattern(pattern, s):
    """Check if string follows pattern"""
    words = s.split()
    
    if len(pattern) != len(words):
        return False
    
    char_to_word = {}
    word_to_char = {}
    
    for char, word in zip(pattern, words):
        if char in char_to_word:
            if char_to_word[char] != word:
                return False
        else:
            char_to_word[char] = word
        
        if word in word_to_char:
            if word_to_char[word] != char:
                return False
        else:
            word_to_char[word] = char
    
    return True
```

---

## Pattern 4: Subarray/Substring Problems

### Longest Substring Without Repeating Characters
```python
def lengthOfLongestSubstring(s):
    """Find length of longest substring without repeating characters"""
    char_index = {}
    max_len = 0
    start = 0
    
    for end, char in enumerate(s):
        if char in char_index and char_index[char] >= start:
            start = char_index[char] + 1
        
        char_index[char] = end
        max_len = max(max_len, end - start + 1)
    
    return max_len
```

### Subarray Sum Equals K
```python
def subarraySum(nums, k):
    """Count subarrays with sum k"""
    count = 0
    prefix_sum = 0
    sum_count = {0: 1}
    
    for num in nums:
        prefix_sum += num
        
        if prefix_sum - k in sum_count:
            count += sum_count[prefix_sum - k]
        
        sum_count[prefix_sum] = sum_count.get(prefix_sum, 0) + 1
    
    return count
```

### Continuous Subarray Sum
```python
def checkSubarraySum(nums, k):
    """Check if subarray sum is multiple of k"""
    remainder_index = {0: -1}
    total = 0
    
    for i, num in enumerate(nums):
        total += num
        remainder = total % k
        
        if remainder in remainder_index:
            if i - remainder_index[remainder] > 1:
                return True
        else:
            remainder_index[remainder] = i
    
    return False
```

---

## Pattern 5: LRU Cache

### LRU Cache Implementation
```python
class Node:
    def __init__(self, key, val):
        self.key = key
        self.val = val
        self.prev = None
        self.next = None

class LRUCache:
    def __init__(self, capacity):
        self.capacity = capacity
        self.cache = {}  # key -> Node
        
        # Dummy head and tail
        self.head = Node(0, 0)
        self.tail = Node(0, 0)
        self.head.next = self.tail
        self.tail.prev = self.head
    
    def _remove(self, node):
        """Remove node from list"""
        prev, nxt = node.prev, node.next
        prev.next = nxt
        nxt.prev = prev
    
    def _add(self, node):
        """Add node before tail"""
        prev = self.tail.prev
        prev.next = node
        node.prev = prev
        node.next = self.tail
        self.tail.prev = node
    
    def get(self, key):
        if key in self.cache:
            node = self.cache[key]
            self._remove(node)
            self._add(node)
            return node.val
        return -1
    
    def put(self, key, value):
        if key in self.cache:
            self._remove(self.cache[key])
        
        node = Node(key, value)
        self._add(node)
        self.cache[key] = node
        
        if len(self.cache) > self.capacity:
            # Remove LRU
            lru = self.head.next
            self._remove(lru)
            del self.cache[lru.key]
```

---

## Pattern 6: Designing Data Structures

### Design HashMap
```python
class MyHashMap:
    def __init__(self):
        self.size = 1000
        self.buckets = [[] for _ in range(self.size)]
    
    def _hash(self, key):
        return key % self.size
    
    def put(self, key, value):
        bucket = self.buckets[self._hash(key)]
        
        for i, (k, v) in enumerate(bucket):
            if k == key:
                bucket[i] = (key, value)
                return
        
        bucket.append((key, value))
    
    def get(self, key):
        bucket = self.buckets[self._hash(key)]
        
        for k, v in bucket:
            if k == key:
                return v
        
        return -1
    
    def remove(self, key):
        bucket = self.buckets[self._hash(key)]
        
        for i, (k, v) in enumerate(bucket):
            if k == key:
                del bucket[i]
                return
```

### Random Pick with Weight
```python
import random
import bisect

class Solution:
    def __init__(self, w):
        self.prefix_sums = []
        total = 0
        
        for weight in w:
            total += weight
            self.prefix_sums.append(total)
        
        self.total = total
    
    def pickIndex(self):
        target = random.randint(1, self.total)
        # Binary search for target
        return bisect.bisect_left(self.prefix_sums, target)
```

---

## Pattern 7: Union-Find with HashMap

### Accounts Merge
```python
from collections import defaultdict

def accountsMerge(accounts):
    """Merge accounts with common emails"""
    email_to_name = {}
    graph = defaultdict(set)
    
    # Build graph
    for account in accounts:
        name = account[0]
        first_email = account[1]
        
        for email in account[1:]:
            email_to_name[email] = name
            graph[first_email].add(email)
            graph[email].add(first_email)
    
    # DFS to find connected components
    visited = set()
    result = []
    
    def dfs(email, component):
        visited.add(email)
        component.add(email)
        
        for neighbor in graph[email]:
            if neighbor not in visited:
                dfs(neighbor, component)
    
    for email in email_to_name:
        if email not in visited:
            component = set()
            dfs(email, component)
            result.append([email_to_name[email]] + sorted(component))
    
    return result
```

---

## 🎨 Dry Run Example

### Two Sum
```
Input: nums = [2, 7, 11, 15], target = 9

Step 1: i=0, num=2
  complement = 9 - 2 = 7
  7 not in seen
  seen = {2: 0}

Step 2: i=1, num=7
  complement = 9 - 7 = 2
  2 in seen! ✓
  return [0, 1]

Result: [0, 1]
```

---

## ⏱️ Complexity Analysis

| Operation | Average | Worst |
|-----------|---------|-------|
| Insert | O(1) | O(n) |
| Delete | O(1) | O(n) |
| Search | O(1) | O(n) |
| Space | O(n) | O(n) |

**Note**: Worst case happens with hash collisions (rare with good hash function).

---

## 🎯 Must-Know Problems

### Easy
- [1. Two Sum](https://leetcode.com/problems/two-sum/)
- [217. Contains Duplicate](https://leetcode.com/problems/contains-duplicate/)
- [242. Valid Anagram](https://leetcode.com/problems/valid-anagram/)
- [387. First Unique Character](https://leetcode.com/problems/first-unique-character-in-a-string/)

### Medium
- [3. Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/)
- [49. Group Anagrams](https://leetcode.com/problems/group-anagrams/)
- [146. LRU Cache](https://leetcode.com/problems/lru-cache/)
- [347. Top K Frequent Elements](https://leetcode.com/problems/top-k-frequent-elements/)
- [560. Subarray Sum Equals K](https://leetcode.com/problems/subarray-sum-equals-k/)

### Hard
- [76. Minimum Window Substring](https://leetcode.com/problems/minimum-window-substring/)
- [149. Max Points on a Line](https://leetcode.com/problems/max-points-on-a-line/)

---

## 💡 Pro Tips

1. **Use Counter**: Simplifies frequency counting
2. **DefaultDict**: Avoid KeyError with default values
3. **Tuple as key**: For multi-dimensional keys
4. **Set for O(1) lookup**: When you only need existence check
5. **HashMap + LinkedList**: For LRU cache
6. **Prefix sum + HashMap**: For subarray sum problems

---

## 🔥 Common Patterns

```python
# Pattern 1: Frequency counter
count = {}
for item in items:
    count[item] = count.get(item, 0) + 1

# Pattern 2: Two sum
seen = {}
for i, num in enumerate(nums):
    if target - num in seen:
        return [seen[target - num], i]
    seen[num] = i

# Pattern 3: Group by key
from collections import defaultdict
groups = defaultdict(list)
for item in items:
    key = compute_key(item)
    groups[key].append(item)

# Pattern 4: Sliding window with HashMap
window = {}
for i, item in enumerate(items):
    window[item] = i
    if len(window) > k:
        # Shrink window
        pass
```

---

**Master HashMap patterns - they're everywhere in interviews! 🗺️**
