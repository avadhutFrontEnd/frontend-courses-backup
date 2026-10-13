---
title: "Greedy"
source: https://www.geekyfrontend.in/learn/complete-frontend-kit/greedy
course: Frontend System Design Kit
tags: [geeky-frontend, frontend-system-design-kit]
---

# Greedy

*4 min read · Updated 16 Jul 2026 Topic 64 of 77 Bookmark Mark done 23G · Topic 64 of 77*

**Source:** [https://www.geekyfrontend.in/learn/complete-frontend-kit/greedy](https://www.geekyfrontend.in/learn/complete-frontend-kit/greedy)

---

# 23G. Greedy (10 problems)

**Pattern recognition:** "At each step, pick the locally optimal choice and it leads to the global optimum."

---

## 23.81 Activity Selection / Meeting Rooms

javascript

```
function maxActivities(activities) {
  activities.sort((a, b) => a.end - b.end);
  let count = 1, lastEnd = activities[0].end;
  for (let i = 1; i < activities.length; i++) {
    if (activities[i].start >= lastEnd) { count++; lastEnd = activities[i].end; }
  }
  return count;
}
```

## 23.82 Jump Game

javascript

```
function canJump(nums) {
  let farthest = 0;
  for (let i = 0; i <= farthest && i < nums.length; i++) {
    farthest = Math.max(farthest, i + nums[i]);
  }
  return farthest >= nums.length - 1;
}
```

## 23.83 Jump Game II

**Approach:** Greedily track the farthest reachable index within the current jump's range; when you reach the end of that range, spend a jump and extend the boundary to the farthest seen.

javascript

```
function jump(nums) {
  let jumps = 0, curEnd = 0, farthest = 0;
  for (let i = 0; i < nums.length - 1; i++) {
    farthest = Math.max(farthest, i + nums[i]);
    if (i === curEnd) {
      jumps++;
      curEnd = farthest;
    }
  }
  return jumps;
}
```

## 23.84 Task Scheduler

**Approach:** Greedily schedule the most frequent task first, laying out (maxFreq - 1) cooldown frames of size n+1 and appending the tasks that tie for the max, taking the larger of that arrangement and the raw task count.

javascript

```
function leastInterval(tasks, n) {
  const counts = {};
  let maxFreq = 0;
  for (const t of tasks) {
    counts[t] = (counts[t] || 0) + 1;
    maxFreq = Math.max(maxFreq, counts[t]);
  }
  let maxCount = 0;
  for (const t in counts) if (counts[t] === maxFreq) maxCount++;
  const intervals = (maxFreq - 1) * (n + 1) + maxCount;
  return Math.max(intervals, tasks.length);
}
```

## 23.85 Merge Intervals

javascript

```
function merge(intervals) {
  intervals.sort((a, b) => a[0] - b[0]);
  const result = [intervals[0]];
  for (let i = 1; i < intervals.length; i++) {
    const last = result[result.length - 1];
    if (intervals[i][0] <= last[1]) last[1] = Math.max(last[1], intervals[i][1]);
    else result.push(intervals[i]);
  }
  return result;
}
```

## 23.86 Non-overlapping Intervals

**Approach:** Sort by end time and greedily keep the interval that finishes earliest, counting any later interval that starts before the kept interval's end as a removal.

javascript

```
function eraseOverlapIntervals(intervals) {
  if (intervals.length === 0) return 0;
  intervals.sort((a, b) => a[1] - b[1]);
  let count = 0, prevEnd = intervals[0][1];
  for (let i = 1; i < intervals.length; i++) {
    if (intervals[i][0] < prevEnd) {
      count++;
    } else {
      prevEnd = intervals[i][1];
    }
  }
  return count;
}
```

## 23.87 Gas Station

**Approach:** If the total surplus of gas minus cost is non-negative a solution exists; greedily reset the start to the next station whenever the running tank drops below zero, since no station up to that point can be a valid start.

javascript

```
function canCompleteCircuit(gas, cost) {
  let total = 0, tank = 0, start = 0;
  for (let i = 0; i < gas.length; i++) {
    const diff = gas[i] - cost[i];
    total += diff;
    tank += diff;
    if (tank < 0) {
      start = i + 1;
      tank = 0;
    }
  }
  return total >= 0 ? start : -1;
}
```

## 23.88 Assign Cookies

**Approach:** Sort both greed factors and cookie sizes ascending, then greedily give the smallest sufficient cookie to the least greedy remaining child.

javascript

```
function findContentChildren(g, s) {
  g.sort((a, b) => a - b);
  s.sort((a, b) => a - b);
  let child = 0, cookie = 0;
  while (child < g.length && cookie < s.length) {
    if (s[cookie] >= g[child]) child++;
    cookie++;
  }
  return child;
}
```

## 23.89 Partition Labels

javascript

```
function partitionLabels(s) {
  const last = {};
  for (let i = 0; i < s.length; i++) last[s[i]] = i;
  const result = [];
  let start = 0, end = 0;
  for (let i = 0; i < s.length; i++) {
    end = Math.max(end, last[s[i]]);
    if (i === end) { result.push(end - start + 1); start = end + 1; }
  }
  return result;
}
```

## 23.90 Minimum Arrows to Burst Balloons

**Approach:** Sort balloons by end coordinate and greedily shoot an arrow at the current earliest end, only spending a new arrow when a balloon starts after that end.

javascript

```
function findMinArrowShots(points) {
  if (points.length === 0) return 0;
  points.sort((a, b) => a[1] - b[1]);
  let arrows = 1, end = points[0][1];
  for (let i = 1; i < points.length; i++) {
    if (points[i][0] > end) {
      arrows++;
      end = points[i][1];
    }
  }
  return arrows;
}
```

---

## Learn More

- [Greedy algorithms (GeeksforGeeks) geeksforgeeks.org](https://www.geeksforgeeks.org/greedy-algorithms/)
- [Greedy algorithms explained YouTube](https://www.youtube.com/watch?v=lfQvPHGtu6Q)
- [Greedy in-depth for interviews YouTube](https://www.youtube.com/watch?v=-WTslqPbj7I)
- [Greedy for beginners YouTube](https://www.youtube.com/watch?v=t_LNyLz1a9o)
