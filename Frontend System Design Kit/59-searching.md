---
title: "Searching"
source: https://www.geekyfrontend.in/learn/complete-frontend-kit/searching
course: Frontend System Design Kit
tags: [geeky-frontend, frontend-system-design-kit]
---

# Searching

*5 min read · Updated 16 Jul 2026 Topic 59 of 77 Bookmark Mark done 23B · Topic 59 of 77*

**Source:** [https://www.geekyfrontend.in/learn/complete-frontend-kit/searching](https://www.geekyfrontend.in/learn/complete-frontend-kit/searching)

---

# 23B. Searching Algorithms (8 problems)

**Pattern recognition:** "Find target in sorted data", "First/last occurrence", "Minimum/maximum that satisfies condition"

---

## 23.13 Linear Search

**Approach:** Scan each element left to right and return the first index whose value equals the target, or -1 if none match.

javascript

```
function linearSearch(arr, target) {
  for (let i = 0; i < arr.length; i++) {
    if (arr[i] === target) return i;
  }
  return -1;
}
```

O(n), use when data is unsorted. Iterate and compare each element.

---

## 23.14 Binary Search

javascript

```
function binarySearch(arr, target) {
  let lo = 0, hi = arr.length - 1;
  while (lo <= hi) {
    const mid = lo + Math.floor((hi - lo) / 2);
    if (arr[mid] === target) return mid;
    if (arr[mid] < target) lo = mid + 1;
    else hi = mid - 1;
  }
  return -1;
}
```

**Time:** O(log n). **Space:** O(1).

**Frontend connection:** Used in virtualized lists — given a scroll position, binary search finds the visible item index.

---

## 23.15 Binary Search on Answer

"Find the minimum/maximum value that satisfies a condition." Search a range of possible answers, not an array.

javascript

```
function shipWithinDays(weights, days) {
  let lo = Math.max(...weights), hi = weights.reduce((a, b) => a + b);
  while (lo < hi) {
    const mid = lo + Math.floor((hi - lo) / 2);
    if (canShip(weights, days, mid)) hi = mid;
    else lo = mid + 1;
  }
  return lo;
}

function canShip(weights, days, capacity) {
  let daysNeeded = 1, currentLoad = 0;
  for (const w of weights) {
    if (currentLoad + w > capacity) { daysNeeded++; currentLoad = 0; }
    currentLoad += w;
  }
  return daysNeeded <= days;
}
```

---

## 23.16 Search in Rotated Sorted Array

javascript

```
function search(nums, target) {
  let lo = 0, hi = nums.length - 1;
  while (lo <= hi) {
    const mid = lo + Math.floor((hi - lo) / 2);
    if (nums[mid] === target) return mid;
    if (nums[lo] <= nums[mid]) {
      if (target >= nums[lo] && target < nums[mid]) hi = mid - 1;
      else lo = mid + 1;
    } else {
      if (target > nums[mid] && target <= nums[hi]) lo = mid + 1;
      else hi = mid - 1;
    }
  }
  return -1;
}
```

---

## 23.17 First and Last Occurrence

javascript

```
function findFirst(arr, target) {
  let lo = 0, hi = arr.length - 1, result = -1;
  while (lo <= hi) {
    const mid = lo + Math.floor((hi - lo) / 2);
    if (arr[mid] === target) { result = mid; hi = mid - 1; }
    else if (arr[mid] < target) lo = mid + 1;
    else hi = mid - 1;
  }
  return result;
}

function findLast(arr, target) {
  let lo = 0, hi = arr.length - 1, result = -1;
  while (lo <= hi) {
    const mid = lo + Math.floor((hi - lo) / 2);
    if (arr[mid] === target) { result = mid; lo = mid + 1; }
    else if (arr[mid] < target) lo = mid + 1;
    else hi = mid - 1;
  }
  return result;
}
```

---

## 23.18 Search in 2D Matrix

**Approach:** Treat the row-sorted matrix as one flat sorted array and binary search it, mapping each mid index back to row/column via divide and modulo by the column count.

javascript

```
function searchMatrix(matrix, target) {
  if (!matrix.length || !matrix[0].length) return false;
  const rows = matrix.length, cols = matrix[0].length;
  let lo = 0, hi = rows * cols - 1;
  while (lo <= hi) {
    const mid = (lo + hi) >> 1;
    const val = matrix[Math.floor(mid / cols)][mid % cols];
    if (val === target) return true;
    if (val < target) lo = mid + 1;
    else hi = mid - 1;
  }
  return false;
}
```

Treat as flat sorted array. `row = mid / cols`, `col = mid % cols`. Standard binary search.

---

## 23.19 Exponential Search

**Approach:** Double an index bound until it passes the target to find a range in O(log i), then binary search within that bounded window.

javascript

```
function exponentialSearch(arr, target) {
  const n = arr.length;
  if (n === 0) return -1;
  if (arr[0] === target) return 0;
  let bound = 1;
  while (bound < n && arr[bound] < target) bound *= 2;
  let lo = Math.floor(bound / 2);
  let hi = Math.min(bound, n - 1);
  while (lo <= hi) {
    const mid = (lo + hi) >> 1;
    if (arr[mid] === target) return mid;
    if (arr[mid] < target) lo = mid + 1;
    else hi = mid - 1;
  }
  return -1;
}
```

Find range (double: 1, 2, 4, 8...), then binary search within. Useful for unbounded/infinite arrays.

---

## 23.20 Interpolation Search

**Approach:** Estimate the probe position by linearly interpolating where the target should lie between the current low and high values, then narrow the range like binary search.

javascript

```
function interpolationSearch(arr, target) {
  let lo = 0, hi = arr.length - 1;
  while (lo <= hi && target >= arr[lo] && target <= arr[hi]) {
    if (lo === hi) return arr[lo] === target ? lo : -1;
    const pos = lo + Math.floor(
      ((target - arr[lo]) * (hi - lo)) / (arr[hi] - arr[lo])
    );
    if (arr[pos] === target) return pos;
    if (arr[pos] < target) lo = pos + 1;
    else hi = pos - 1;
  }
  return -1;
}
```

Estimates position based on value distribution. O(log log n) for uniform data, O(n) worst.

---

## Learn More

- [Binary search (GeeksforGeeks) geeksforgeeks.org](https://www.geeksforgeeks.org/binary-search/)
- [Learn binary search in 10 minutes YouTube](https://www.youtube.com/watch?v=xrMppTpoqdw)
- [Binary search in 100 seconds YouTube](https://www.youtube.com/watch?v=MFhxShGxHWc)
- [Binary search explained visually YouTube](https://www.youtube.com/watch?v=iO_gdw4HZ84)
