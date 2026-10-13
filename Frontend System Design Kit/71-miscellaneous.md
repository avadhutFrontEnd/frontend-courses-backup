---
title: "Miscellaneous"
source: https://www.geekyfrontend.in/learn/complete-frontend-kit/miscellaneous
course: Frontend System Design Kit
tags: [geeky-frontend, frontend-system-design-kit]
---

# Miscellaneous

*2 min read · Updated 4 Jun 2026 Topic 71 of 77 Bookmark Mark done 23O · Topic 71 of 77*

**Source:** [https://www.geekyfrontend.in/learn/complete-frontend-kit/miscellaneous](https://www.geekyfrontend.in/learn/complete-frontend-kit/miscellaneous)

---

# 23O. Miscellaneous Patterns (5 problems)

**Standalone algorithms that don't fit neatly into one pattern category but are frequently asked.**

---

## 23.176 Kadane's Algorithm (Maximum Subarray Sum)

javascript

```
function maxSubArray(nums) {
  let maxSum = nums[0], currentSum = nums[0];
  for (let i = 1; i < nums.length; i++) {
    currentSum = Math.max(nums[i], currentSum + nums[i]);
    maxSum = Math.max(maxSum, currentSum);
  }
  return maxSum;
}
```

**Time:** O(n). **Space:** O(1).

---

## 23.177 Boyer-Moore Voting (Majority Element)

javascript

```
function majorityElement(nums) {
  let candidate = nums[0], count = 1;
  for (let i = 1; i < nums.length; i++) {
    if (count === 0) { candidate = nums[i]; count = 1; }
    else if (nums[i] === candidate) count++;
    else count--;
  }
  return candidate;
}
```

---

## 23.178 Reservoir Sampling (Random selection from stream)

Select k items from a stream of unknown length with equal probability:

javascript

```
function reservoirSample(stream, k) {
  const reservoir = stream.slice(0, k);
  for (let i = k; i < stream.length; i++) {
    const j = Math.floor(Math.random() * (i + 1));
    if (j < k) reservoir[j] = stream[i];
  }
  return reservoir;
}
```

---

## 23.179 Product of Array Except Self

Build left and right products. No division needed.

javascript

```
function productExceptSelf(nums) {
  const n = nums.length;
  const result = Array(n).fill(1);
  let left = 1;
  for (let i = 0; i < n; i++) { result[i] = left; left *= nums[i]; }
  let right = 1;
  for (let i = n - 1; i >= 0; i--) { result[i] *= right; right *= nums[i]; }
  return result;
}
```

**Time:** O(n). **Space:** O(1) excluding output.

---

## 23.180 Top K Frequent Elements

javascript

```
function topKFrequent(nums, k) {
  const freq = new Map();
  for (const num of nums) freq.set(num, (freq.get(num) || 0) + 1);

  // Bucket sort by frequency
  const buckets = Array.from({ length: nums.length + 1 }, () => []);
  for (const [num, count] of freq) buckets[count].push(num);

  const result = [];
  for (let i = buckets.length - 1; i >= 0 && result.length < k; i--) {
    result.push(...buckets[i]);
  }
  return result.slice(0, k);
}
```

**Time:** O(n). **Space:** O(n).

**Frontend connection:** "Show the top 10 most viewed products" — frequency counting + top-K extraction.

---

## Learn More

- [DSA roadmap (NeetCode) neetcode.io](https://neetcode.io/roadmap)
- [14 patterns to ace coding interviews YouTube](https://www.youtube.com/watch?v=uOefwtUoH4M)
- [Ace interviews by thinking in patterns YouTube](https://www.youtube.com/watch?v=U3RtWqodZ8U)
- [DSA patterns for LeetCode interviews YouTube](https://www.youtube.com/watch?v=Z_c4byLrNBU)
