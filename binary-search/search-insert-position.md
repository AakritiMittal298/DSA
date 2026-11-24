# Search Insert Position

**LeetCode:** [35. Search Insert Position](https://leetcode.com/problems/search-insert-position/)

**Pattern:** Binary Search — Insertion Position

## 1. Problem Statement

Given a sorted array of **distinct** integers `nums` and a target value `target`, return the index if the target is found. If it is not found, return the index where it would be inserted in order.

The solution must run in **\(O(\log n)\)** time.

### Examples

**Example 1**

```text
Input:  nums = [1, 3, 5, 6], target = 5
Output: 2
```

**Explanation:** The target `5` is present at index `2`.

**Example 2**

```text
Input:  nums = [1, 3, 5, 6], target = 2
Output: 1
```

**Explanation:** The target `2` is not present. It should be inserted between `1` and `3`, at index `1`.

**Example 3**

```text
Input:  nums = [1, 3, 5, 6], target = 7
Output: 4
```

**Explanation:** The target is greater than every element, so it should be inserted at the end. The insertion index is `4`.

## 2. Approach — Binary Search

Because the array is sorted, we can use binary search instead of checking each element one by one.

1. Initialize `l = 0` and `r = nums.length - 1`.
2. Calculate the middle index.
3. If `nums[mid] == target`, return `mid` because the target is already present.
4. If `nums[mid] < target`, the target or its insertion position must be on the right side, so update `l = mid + 1`.
5. Otherwise, move to the left side by updating `r = mid - 1`.
6. If the loop finishes without finding the target, return `l`.

### Java Code

```java
class Solution {
    public int searchInsert(int[] nums, int target) {

        int l = 0, r = nums.length - 1;

        while (l <= r) {

            int mid = l + (r - l) / 2;

            if (nums[mid] == target) {
                return mid;
            }
            else if (nums[mid] < target) {
                l = mid + 1;
            }
            else {
                r = mid - 1;
            }
        }

        return l;
    }
}
```

## 3. Why do we return `l`?

This is the most important part of this question.

When the binary-search loop terminates, `l` points to the position where the target should be inserted.

- Every index before `l` contains a value smaller than the target.
- Every index from `l) onward (when such an index exists) contains a value greater than the target.

In other words, `l` is the first position at which the target can be placed while keeping the array sorted.

### Example: Target is in the middle

```text
nums   = [1, 3, 5, 6]
target = 2

Initial: l = 0, r = 3

mid = 0 + (3 - 0) / 2 = 1
nums[1] = 3

3 > 2, so r = mid - 1 = 0

Now: l = 0, r = 0

mid = 0 + (0 - 0) / 2 = 0
nums[0] = 1

1 < 2, so l = mid + 1 = 1

Now: l = 1, r = 0

The loop stops because l > r.

Return l = 1.
```

The correct insertion position is index `1`:

```text
[1, 2, 3, 5, 6]
    ^
  index 1
```

### Example: Target is greater than all elements

```text
nums   = [1, 3, 5, 6]
target = 7
```

Every comparison moves `l` to the right. Eventually, `l = 4` and `r = 3`. The loop stops and we return `4`, which is the position just after the last element.

### Why do we calculate `mid` this way?

```java
int mid = l + (r - l) / 2;
```

A commonly written alternative is:

```java
int mid = (l + r) / 2;
```

However, `l + r` can overflow the maximum value of Java's `int` when both indices are large.

The safer formula:

1. `r - l` calculates the distance between the boundaries.
2. Dividing by `2` gives half of that distance.
3. Adding it to `l` gives the middle index.

This avoids adding the two potentially large indices directly.

**Remember:** Prefer `l + (r - l) / 2` in binary search.

## 4. Complexity Analysis

- **Time Complexity:** \(O(\log n)\) — the search space is approximately halved in every iteration.
- **Space Complexity:** \(O(1)\) — only a constant number of variables are used.

## 5. Important Takeaways

- The array is sorted, so binary search is applicable.
- If the target exists, return its index immediately.
- If the target does not exist, `l` gives the correct insertion position.
- If the target is smaller than all elements, return `0`.
- If the target is greater than all elements, return `nums.length`.
- This is closely related to finding the **lower bound**: the first index where the array value is greater than or equal to the target.
