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

Instead of writing two separate binary-search functions, we write one reusable `search()` function and control its behaviour using a boolean parameter named `findFirst`. Before searching, `searchRange()` handles a null or empty array. It also checks whether the first occurrence exists; if not, it immediately returns `[-1, -1]` without running the second binary search.

- `findFirst = true`: when the target is found, store its index in `ans` and continue searching in the **left half** using `r = mid - 1`. This finds the first occurrence.
- `findFirst = false`: when the target is found, store its index in `ans` and continue searching in the **right half** using `l = mid + 1`. This finds the last occurrence.

### Java Code

```java
class Solution {
    public int[] searchRange(int[] nums, int target) {

        if (nums == null || nums.length == 0) {
            return new int[]{-1, -1};
        }

        int first = search(nums, target, true);

        // If target does not exist, there is no need to search for last.
        if (first == -1) {
            return new int[]{-1, -1};
        }

        int last = search(nums, target, false);

        return new int[]{first, last};
    }

    public static int search(int[] nums, int target, boolean findFirst) {

        int l = 0, r = nums.length - 1, ans = -1;

        while (l <= r) {

            // Safe middle-index calculation
            int mid = l + (r - l) / 2;

            if (nums[mid] == target) {
                ans = mid;

                if (findFirst) {
                    // Save this occurrence and continue left
                    r = mid - 1;
                } else {
                    // Save this occurrence and continue right
                    l = mid + 1;
                }

            } else if (nums[mid] < target) {
                l = mid + 1;
            } else {
                r = mid - 1;
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
