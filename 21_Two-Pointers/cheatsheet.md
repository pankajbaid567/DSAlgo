# 🎯 Two Pointers & Sliding Window - Comprehensive Cheatsheet

## 📚 Core Concept

**Two Pointers**: Use two indices to traverse array/string, reducing time complexity from O(n²) to O(n).

**Sliding Window**: Maintain a window that slides through array/string to find optimal subarray.

---

## Pattern Classification

### 1️⃣ Two Pointers from Both Ends
### 2️⃣ Fast-Slow Pointers
### 3️⃣ Fixed-Size Sliding Window
### 4️⃣ Dynamic-Size Sliding Window
### 5️⃣ Multiple Arrays Two Pointers

---

## Pattern 1: Two Pointers from Both Ends

### Template
```python
def two_pointers_both_ends(arr):
    left, right = 0, len(arr) - 1
    
    while left < right:
        # Process current state
        if condition:
            # Move left pointer
            left += 1
        else:
            # Move right pointer
            right -= 1
    
    return result
```

### Common Problems

#### Two Sum (Sorted Array)
```python
def twoSum(numbers, target):
    left, right = 0, len(numbers) - 1
    
    while left < right:
        current_sum = numbers[left] + numbers[right]
        
        if current_sum == target:
            return [left + 1, right + 1]  # 1-indexed
        elif current_sum < target:
            left += 1
        else:
            right -= 1
    
    return []
```

#### Container With Most Water
```python
def maxArea(height):
    left, right = 0, len(height) - 1
    max_water = 0
    
    while left < right:
        width = right - left
        h = min(height[left], height[right])
        max_water = max(max_water, width * h)
        
        # Move pointer with smaller height
        if height[left] < height[right]:
            left += 1
        else:
            right -= 1
    
    return max_water
```

#### Valid Palindrome
```python
def isPalindrome(s):
    left, right = 0, len(s) - 1
    
    while left < right:
        # Skip non-alphanumeric
        while left < right and not s[left].isalnum():
            left += 1
        while left < right and not s[right].isalnum():
            right -= 1
        
        if s[left].lower() != s[right].lower():
            return False
        
        left += 1
        right -= 1
    
    return True
```

#### 3Sum
```python
def threeSum(nums):
    nums.sort()
    result = []
    
    for i in range(len(nums) - 2):
        # Skip duplicates
        if i > 0 and nums[i] == nums[i-1]:
            continue
        
        left, right = i + 1, len(nums) - 1
        target = -nums[i]
        
        while left < right:
            current_sum = nums[left] + nums[right]
            
            if current_sum == target:
                result.append([nums[i], nums[left], nums[right]])
                
                # Skip duplicates
                while left < right and nums[left] == nums[left+1]:
                    left += 1
                while left < right and nums[right] == nums[right-1]:
                    right -= 1
                
                left += 1
                right -= 1
            elif current_sum < target:
                left += 1
            else:
                right -= 1
    
    return result
```

---

## Pattern 2: Fast-Slow Pointers (Floyd's Algorithm)

### Template
```python
def fast_slow_pointers(arr):
    slow = fast = 0
    
    while fast < len(arr) and fast + 1 < len(arr):
        slow += 1
        fast += 2
        
        if slow == fast:
            # Cycle detected or meet point
            break
    
    return result
```

### Common Problems

#### Linked List Cycle Detection
```python
def hasCycle(head):
    if not head:
        return False
    
    slow = fast = head
    
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        
        if slow == fast:
            return True
    
    return False

def detectCycle(head):
    if not head:
        return None
    
    slow = fast = head
    
    # Detect cycle
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        
        if slow == fast:
            break
    else:
        return None
    
    # Find cycle start
    slow = head
    while slow != fast:
        slow = slow.next
        fast = fast.next
    
    return slow
```

#### Middle of Linked List
```python
def middleNode(head):
    slow = fast = head
    
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    
    return slow
```

#### Happy Number
```python
def isHappy(n):
    def get_next(num):
        total = 0
        while num > 0:
            digit = num % 10
            total += digit * digit
            num //= 10
        return total
    
    slow = fast = n
    
    while True:
        slow = get_next(slow)
        fast = get_next(get_next(fast))
        
        if fast == 1:
            return True
        if slow == fast:
            return False
```

---

## Pattern 3: Fixed-Size Sliding Window

### Template
```python
def fixed_window(arr, k):
    if len(arr) < k:
        return []
    
    # Initialize first window
    window_sum = sum(arr[:k])
    result = [process(window_sum)]
    
    # Slide window
    for i in range(k, len(arr)):
        window_sum += arr[i] - arr[i-k]
        result.append(process(window_sum))
    
    return result
```

### Common Problems

#### Maximum Average Subarray
```python
def findMaxAverage(nums, k):
    current_sum = sum(nums[:k])
    max_sum = current_sum
    
    for i in range(k, len(nums)):
        current_sum += nums[i] - nums[i-k]
        max_sum = max(max_sum, current_sum)
    
    return max_sum / k
```

#### Sliding Window Maximum
```python
from collections import deque

def maxSlidingWindow(nums, k):
    if not nums:
        return []
    
    dq = deque()  # Store indices
    result = []
    
    for i in range(len(nums)):
        # Remove elements outside window
        while dq and dq[0] <= i - k:
            dq.popleft()
        
        # Remove smaller elements
        while dq and nums[dq[-1]] < nums[i]:
            dq.pop()
        
        dq.append(i)
        
        # Add to result
        if i >= k - 1:
            result.append(nums[dq[0]])
    
    return result
```

#### Contains Duplicate II
```python
def containsNearbyDuplicate(nums, k):
    window = set()
    
    for i in range(len(nums)):
        if nums[i] in window:
            return True
        
        window.add(nums[i])
        
        # Maintain window size
        if len(window) > k:
            window.remove(nums[i-k])
    
    return False
```

---

## Pattern 4: Dynamic-Size Sliding Window

### Template
```python
def dynamic_window(arr):
    left = 0
    result = 0
    window_state = {}
    
    for right in range(len(arr)):
        # Expand window
        update_window(arr[right])
        
        # Shrink window if invalid
        while not is_valid():
            remove_from_window(arr[left])
            left += 1
        
        # Update result
        result = max(result, right - left + 1)
    
    return result
```

### Common Problems

#### Longest Substring Without Repeating Characters
```python
def lengthOfLongestSubstring(s):
    char_set = set()
    left = 0
    max_len = 0
    
    for right in range(len(s)):
        # Shrink window while duplicate exists
        while s[right] in char_set:
            char_set.remove(s[left])
            left += 1
        
        char_set.add(s[right])
        max_len = max(max_len, right - left + 1)
    
    return max_len
```

#### Minimum Window Substring
```python
from collections import Counter

def minWindow(s, t):
    if not s or not t:
        return ""
    
    target_count = Counter(t)
    required = len(target_count)
    formed = 0
    
    window_count = {}
    left = 0
    min_len = float('inf')
    min_left = 0
    
    for right in range(len(s)):
        char = s[right]
        window_count[char] = window_count.get(char, 0) + 1
        
        if char in target_count and window_count[char] == target_count[char]:
            formed += 1
        
        # Shrink window
        while formed == required and left <= right:
            # Update result
            if right - left + 1 < min_len:
                min_len = right - left + 1
                min_left = left
            
            char = s[left]
            window_count[char] -= 1
            if char in target_count and window_count[char] < target_count[char]:
                formed -= 1
            
            left += 1
    
    return "" if min_len == float('inf') else s[min_left:min_left + min_len]
```

#### Longest Repeating Character Replacement
```python
def characterReplacement(s, k):
    count = {}
    left = 0
    max_count = 0
    max_len = 0
    
    for right in range(len(s)):
        count[s[right]] = count.get(s[right], 0) + 1
        max_count = max(max_count, count[s[right]])
        
        # If replacements needed > k, shrink
        while right - left + 1 - max_count > k:
            count[s[left]] -= 1
            left += 1
        
        max_len = max(max_len, right - left + 1)
    
    return max_len
```

#### Fruits Into Baskets
```python
def totalFruit(fruits):
    count = {}
    left = 0
    max_fruits = 0
    
    for right in range(len(fruits)):
        count[fruits[right]] = count.get(fruits[right], 0) + 1
        
        # More than 2 types
        while len(count) > 2:
            count[fruits[left]] -= 1
            if count[fruits[left]] == 0:
                del count[fruits[left]]
            left += 1
        
        max_fruits = max(max_fruits, right - left + 1)
    
    return max_fruits
```

---

## Pattern 5: Multiple Arrays Two Pointers

### Template
```python
def merge_arrays(arr1, arr2):
    i = j = 0
    result = []
    
    while i < len(arr1) and j < len(arr2):
        if condition:
            result.append(arr1[i])
            i += 1
        else:
            result.append(arr2[j])
            j += 1
    
    # Add remaining elements
    result.extend(arr1[i:])
    result.extend(arr2[j:])
    
    return result
```

### Common Problems

#### Merge Sorted Arrays
```python
def merge(nums1, m, nums2, n):
    i, j, k = m - 1, n - 1, m + n - 1
    
    # Merge from end to avoid overwriting
    while i >= 0 and j >= 0:
        if nums1[i] > nums2[j]:
            nums1[k] = nums1[i]
            i -= 1
        else:
            nums1[k] = nums2[j]
            j -= 1
        k -= 1
    
    # Copy remaining from nums2
    while j >= 0:
        nums1[k] = nums2[j]
        j -= 1
        k -= 1
```

#### Intersection of Two Arrays
```python
def intersect(nums1, nums2):
    nums1.sort()
    nums2.sort()
    
    i = j = 0
    result = []
    
    while i < len(nums1) and j < len(nums2):
        if nums1[i] == nums2[j]:
            result.append(nums1[i])
            i += 1
            j += 1
        elif nums1[i] < nums2[j]:
            i += 1
        else:
            j += 1
    
    return result
```

---

## 🎨 Dry Run Example

### Longest Substring Without Repeating Characters

```
Input: s = "abcabcbb"

Step-by-step:
left=0, right=0: "a"     → char_set={'a'}, max_len=1
left=0, right=1: "ab"    → char_set={'a','b'}, max_len=2
left=0, right=2: "abc"   → char_set={'a','b','c'}, max_len=3
left=0, right=3: "abca"  → 'a' duplicate!
  left=1: remove 'a'     → char_set={'b','c'}
                         → add 'a', char_set={'b','c','a'}, max_len=3
left=1, right=4: "bcab"  → 'b' duplicate!
  left=2: remove 'b'     → char_set={'c','a'}
                         → add 'b', char_set={'c','a','b'}, max_len=3
left=2, right=5: "cabc"  → 'c' duplicate!
  left=3: remove 'c'     → char_set={'a','b'}
                         → add 'c', char_set={'a','b','c'}, max_len=3
left=3, right=6: "abcb"  → 'b' duplicate!
  left=4: remove 'a'     → char_set={'b','c'}
  left=5: remove 'b'     → char_set={'c'}
                         → add 'b', char_set={'c','b'}, max_len=3
left=5, right=7: "cbb"   → 'b' duplicate!
  left=6: remove 'c'     → char_set={'b'}
  left=7: remove 'b'     → char_set={}
                         → add 'b', char_set={'b'}, max_len=3

Result: 3 (substring "abc")
```

---

## ⏱️ Complexity Analysis

| Pattern | Time | Space |
|---------|------|-------|
| Two Pointers (Both Ends) | O(n) | O(1) |
| Fast-Slow Pointers | O(n) | O(1) |
| Fixed Sliding Window | O(n) | O(k) |
| Dynamic Sliding Window | O(n) | O(k) |
| Multiple Arrays | O(m + n) | O(1) |

---

## 🎯 Must-Know Problems

### Easy
- [167. Two Sum II](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/)
- [125. Valid Palindrome](https://leetcode.com/problems/valid-palindrome/)
- [283. Move Zeroes](https://leetcode.com/problems/move-zeroes/)
- [344. Reverse String](https://leetcode.com/problems/reverse-string/)

### Medium
- [3. Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/)
- [15. 3Sum](https://leetcode.com/problems/3sum/)
- [11. Container With Most Water](https://leetcode.com/problems/container-with-most-water/)
- [424. Longest Repeating Character Replacement](https://leetcode.com/problems/longest-repeating-character-replacement/)
- [438. Find All Anagrams in a String](https://leetcode.com/problems/find-all-anagrams-in-a-string/)
- [713. Subarray Product Less Than K](https://leetcode.com/problems/subarray-product-less-than-k/)

### Hard
- [76. Minimum Window Substring](https://leetcode.com/problems/minimum-window-substring/)
- [239. Sliding Window Maximum](https://leetcode.com/problems/sliding-window-maximum/)

---

## 💡 Pro Tips

1. **Choose the right pattern**:
   - Sorted array → Two pointers from both ends
   - Subarray/substring → Sliding window
   - Linked list → Fast-slow pointers

2. **Sliding window validity**:
   - Expand: Add right element
   - Shrink: Remove left element when invalid
   - Update result at valid state

3. **HashMap for frequency**: Use Counter/dict for character/element tracking

4. **Edge cases**:
   - Empty array/string
   - Window size larger than array
   - All duplicates
   - Single element

5. **When to expand vs shrink**:
   - Always expand right
   - Shrink left when condition violated

---

## 🔥 Common Mistakes

❌ **Not handling duplicates in 3Sum**  
✅ Skip duplicates explicitly

❌ **Wrong window shrinking condition**  
✅ Use while loop with clear condition

❌ **Forgetting to update result**  
✅ Update result inside/outside loop correctly

❌ **Off-by-one errors**  
✅ Be careful with inclusive/exclusive bounds

---

**Master these patterns and solve 80% of array/string problems efficiently! 🚀**
