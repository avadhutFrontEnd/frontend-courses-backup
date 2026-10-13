---
title: "Bit manipulation"
source: https://www.geekyfrontend.in/learn/complete-frontend-kit/bit-manipulation
course: Frontend System Design Kit
tags: [geeky-frontend, frontend-system-design-kit]
---

# Bit manipulation

*2 min read · Updated 16 Jul 2026 Topic 69 of 77 Bookmark Mark done 23M · Topic 69 of 77*

**Source:** [https://www.geekyfrontend.in/learn/complete-frontend-kit/bit-manipulation](https://www.geekyfrontend.in/learn/complete-frontend-kit/bit-manipulation)

---

# 23M. Bit Manipulation (5 problems)

**Pattern recognition:** XOR for pairing, single bits, powers of two, counting set bits.

---

## 23.163 Single Number (XOR)

`a ^ a = 0`, `a ^ 0 = a`. XOR all elements — pairs cancel, single remains.

javascript

```
function singleNumber(nums) {
  return nums.reduce((acc, num) => acc ^ num, 0);
}
```

---

## 23.164 Number of 1 Bits (Hamming Weight)

javascript

```
function hammingWeight(n) {
  let count = 0;
  while (n) { count += n & 1; n >>>= 1; }
  return count;
}
// Or: n & (n-1) removes lowest set bit — count iterations until n = 0
```

---

## 23.165 Power of Two

**Approach:** Use the n & (n - 1) trick, which clears the lowest set bit; a power of two has exactly one set bit, so the result is zero.

javascript

```
function isPowerOfTwo(n) {
  return n > 0 && (n & (n - 1)) === 0;
}
```

A power of two has exactly one set bit: `n > 0 && (n & (n - 1)) === 0`.

---

## 23.166 Counting Bits (for 0 to n)

**Approach:** Dynamic programming with dp[i] = dp[i >> 1] + (i & 1), reusing the bit count of i with its lowest bit dropped.

javascript

```
function countBits(n) {
  const dp = new Array(n + 1).fill(0);
  for (let i = 1; i <= n; i++) {
    dp[i] = dp[i >> 1] + (i & 1);
  }
  return dp;
}
```

`dp[i] = dp[i >> 1] + (i & 1)`. Number of 1-bits in i = bits in i/2 + whether last bit is set.

---

## 23.167 Missing Number (0 to n, one missing)

**Approach:** XOR all indices, all values, and n together; matching pairs cancel out and the leftover is the missing number.

javascript

```
function missingNumber(nums) {
  let missing = nums.length;
  for (let i = 0; i < nums.length; i++) {
    missing ^= i ^ nums[i];
  }
  return missing;
}
```

XOR 0..n with all array elements — pairs cancel, missing remains. Or: `n*(n+1)/2 - arraySum`.

---

## Learn More

- [Bitwise algorithms (GeeksforGeeks) geeksforgeeks.org](https://www.geeksforgeeks.org/bitwise-algorithms/)
- [Bit manipulation: 5 must-know tricks YouTube](https://www.youtube.com/watch?v=M7n-1dXNsnY)
- [Master bit manipulation YouTube](https://www.youtube.com/watch?v=JcHiYWeLJdE)
- [Bit manipulation (HackerRank) YouTube](https://www.youtube.com/watch?v=NLKQEOgBAnw)
