# Merge Sorted Array — Code Explanation

## Problem

You get two sorted arrays, `nums1` and `nums2`.

- `nums1` has length `m + n`. Its first `m` elements are real values, and the last `n` slots are `0` placeholders.
- `nums2` has `n` elements.

Merge `nums2` into `nums1` so that `nums1` ends up sorted. Do it **in place**, and don't return anything.

## The code

```javascript
var merge = function (nums1, m, nums2, n) {
  let i = m - 1,
    j = n - 1,
    k = m + n - 1;

  while (i >= 0 && j >= 0) {
    if (nums1[i] > nums2[j]) {
      nums1[k--] = nums1[i--];
    } else {
      nums1[k--] = nums2[j--];
    }
  }

  while (j >= 0) {
    nums1[k--] = nums2[j--];
  }
};
```

## Key idea: fill from the back

If you merged from the front, you would overwrite the real values in `nums1` before you had compared them.

The empty space in `nums1` is at the **end**. So we work backwards and put the **largest** remaining element at the end each time. Writing to position `k` never destroys an unprocessed element.

## The three pointers

| Pointer | Starts at   | Meaning                                     |
| ------- | ----------- | ------------------------------------------- |
| `i`     | `m - 1`     | Last real element of `nums1`                |
| `j`     | `n - 1`     | Last element of `nums2`                     |
| `k`     | `m + n - 1` | Next slot to fill in `nums1` (the very end) |

## Step by step

### 1. Main loop: `while (i >= 0 && j >= 0)`

While both arrays still have elements, compare the two current largest:

- If `nums1[i] > nums2[j]`, copy `nums1[i]` into `nums1[k]`. Then move `i` and `k` left.
- Otherwise, copy `nums2[j]` into `nums1[k]`. Then move `j` and `k` left.

`k--` and `i--` / `j--` are post-decrements. The current value is used first, then the pointer moves.

When the values are equal, the `else` branch runs, so the element from `nums2` is placed first. Either choice gives a correct sorted result.

### 2. Leftover loop: `while (j >= 0)`

If `nums2` still has elements after the main loop, they are all smaller than everything already placed. Copy them straight into the front of `nums1`.

There is no matching loop for leftover `nums1` elements. If `j < 0`, the remaining `nums1[0..i]` values are already in their correct positions, so nothing needs to move. This is very important point to remember.

## Trace: Example 1

`nums1 = [1, 2, 3, 0, 0, 0]`, `m = 3`, `nums2 = [2, 5, 6]`, `n = 3`

Start: `i = 2`, `j = 2`, `k = 5`

| Step | Compare      | Action         | `nums1` after   | i   | j   | k   |
| ---- | ------------ | -------------- | --------------- | --- | --- | --- |
| 1    | `3 > 6`? No  | `nums1[5] = 6` | `[1,2,3,0,0,6]` | 2   | 1   | 4   |
| 2    | `3 > 5`? No  | `nums1[4] = 5` | `[1,2,3,0,5,6]` | 2   | 0   | 3   |
| 3    | `3 > 2`? Yes | `nums1[3] = 3` | `[1,2,3,3,5,6]` | 1   | 0   | 2   |
| 4    | `2 > 2`? No  | `nums1[2] = 2` | `[1,2,2,3,5,6]` | 1   | -1  | 1   |

Now `j < 0`, so the main loop ends. The leftover loop doesn't run. The result is:

```
[1, 2, 2, 3, 5, 6]
```

## Trace: Example 2 (edge case)

`nums1 = [1]`, `m = 1`, `nums2 = []`, `n = 0`

- `i = 0`, `j = -1`, `k = 0`
- The main loop doesn't run because `j < 0`.
- The leftover loop doesn't run either.
- `nums1` is unchanged, so the output is `[1]`.

This shows the code handles an empty `nums2` without any special case.

## Complexity

- **Time: O(m + n).** Each element is placed exactly once.
- **Space: O(1).** Only three pointers are used, and the merge happens inside `nums1`.

## Why not the simpler approaches?

- **Copy `nums2` into `nums1`, then sort:** O((m+n) log(m+n)). It ignores the fact that both arrays are already sorted.
- **Merge from the front into a new array:** O(m+n) time but O(m+n) extra space. It also doesn't satisfy the in-place requirement.
- **Merge from the back (this solution):** O(m+n) time and O(1) space.

## Key takeaways

1. When there is free space at the end of an array, merge from the back.
2. Only the `nums2` leftovers need handling. Leftovers in `nums1` are already in place.
3. The pattern of three pointers moving in opposite directions to the usual one appears in many in-place array problems.
