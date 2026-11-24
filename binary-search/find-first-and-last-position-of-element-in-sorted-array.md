# Find First and Last Position of Element in Sorted Array

**LeetCode:** [34. Find First and Last Position of Element in Sorted Array](https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/)

**Pattern:** Binary Search — Find Boundary

## 1. Problem Statement

Given an array of integers `nums` sorted in non-decreasing order and an integer `target`, find the starting and ending position of `target` in the array.

If `target` is not present, return `[-1, -1]`.

The solution must run in **(O(\log n))** time.

### Example

```text
Input:
nums = [5, 7, 7, 8, 8, 10]
target = 8

Output:
[3, 4]
```

**Explanation:** The target `8` occurs at indices `3` and `4`. Therefore, the first position is `3` and the last position is `4`.

Another example:

```text
Input:
nums = [5, 7, 7, 8, 8, 10]
target = 6

Output:
[-1, -1]
```

## 2. Approach — Binary Search Using One Helper Function

We use **two binary searches**:

1. Find the first occurrence of `target`.
2. Find the last occurrence of `target`.

Instead of writing two separate binary-search functions, we write one helper function and control its behaviour using a boolean parameter named `findFirst`.

- `findFirst = true`: when the target is found, store its index and continue searching in the **left half**. This helps us find the first occurrence.
- `findFirst = false`: when the target is found, store its index and continue searching in the **right half**. This helps us find the last occurrence.

### Java Code

```java
class Solution {

    public int[] searchRange(int[] nums, int target) {

        int first = findPosition(nums, target, true);
        int last  = findPosition(nums, target, false);

        return new int[]{first, last};
    }

    private int findPosition(int[] nums, int target, boolean findFirst) {

        int low = 0;
        int high = nums.length - 1;
        int ans = -1;

        while (low <= high) {

            // Calculate the middle index safely
            int mid = low + (high - low) / 2;

            if (nums[mid] == target) {

                // Store the current occurrence
                ans = mid;

                if (findFirst) {
                    // Search further in the left half
                    high = mid - 1;
                } else {
                    // Search further in the right half
                    low = mid + 1;
                }

            } else if (nums[mid] < target) {

                // Target can only be on the right side
                low = mid + 1;

            } else {

                // Target can only be on the left side
                high = mid - 1;
            }
        }

        return ans;
    }
}
```

### Why do we calculate mid this way?

```java
int mid = low + (high - low) / 2;
```

A commonly written formula is:

```java
int mid = (low + high) / 2;
```

But this can cause **integer overflow** when `low` and `high` are both large, because their sum may exceed Java's maximum `int` value.

The safer formula is:

```java
low + (high - low) / 2
```

It works in three steps:

1. `high - low` calculates the distance between the two boundaries.
2. Dividing by `2` gives half of that distance.
3. Adding it to `low` gives the middle index.

The result stays between `low` and `high` without adding the two large indices directly.

**Remember:** Prefer `low + (high - low) / 2` in binary search to avoid possible integer overflow.

### Key Logic to Remember

| Condition | Action |
|---|---|
| `nums[mid] == target` and `findFirst == true` | Save `mid`, then `high = mid - 1` |
| `nums[mid] == target` and `findFirst == false` | Save `mid`, then `low = mid + 1` |
| `nums[mid] < target` | `low = mid + 1` |
| `nums[mid] > target` | `high = mid - 1` |

### Complexity Analysis

- **Time Complexity:** (O(\log n)) — we perform two binary searches, each taking logarithmic time.
- **Space Complexity:** (O(1)) — only a constant number of variables are used.

### Important Edge Cases

- Target occurs only once: return the same index twice.
- Target occurs multiple times: return the first and last indices.
- Target is absent: return `[-1, -1]`.
- Empty array: return `[-1, -1]`.
- All elements are the target: return `[0, n - 1]`.
