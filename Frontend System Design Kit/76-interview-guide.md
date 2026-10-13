---
title: "Interview guide"
source: https://www.geekyfrontend.in/learn/complete-frontend-kit/interview-guide
course: Frontend System Design Kit
tags: [geeky-frontend, frontend-system-design-kit]
---

# Interview guide

*5 min read · Updated 4 Jun 2026 Topic 76 of 77 Bookmark Mark done 25A · Topic 76 of 77*

**Source:** [https://www.geekyfrontend.in/learn/complete-frontend-kit/interview-guide](https://www.geekyfrontend.in/learn/complete-frontend-kit/interview-guide)

---

# 25A. Interview Guide

**Items:** 5

---

## 25.1 Frontend Interview Formats — What to Expect at Different Company Types

### Big Tech (Google, Amazon, Microsoft, Meta)

Typically 4-5 rounds over one day (virtual or on-site):

**Round 1: DSA/Coding (45-60 min)** — 1-2 LeetCode-style problems. For frontend, problems lean toward arrays, strings, trees, and hash maps. Usually medium difficulty.

**Round 2: Machine Coding (60-90 min)** — Build a UI component or small application. Evaluate: working code, component structure, edge cases, keyboard/accessibility, clean code.

**Round 3: Frontend System Design (45-60 min)** — Design a large-scale frontend application. "Design the frontend for Google Docs" or "Design Instagram's feed."

**Round 4: Behavioral (45 min)** — STAR-format answers. Leadership principles (Amazon), Googleyness (Google), or general culture fit.

**Round 5 (sometimes): Bar Raiser / Cross-functional** — Another behavioral or coding from a different team.

### Indian Product Companies (Flipkart, Swiggy, Razorpay, Cred, PhonePe)

**Round 1: Machine Coding (60-90 min)** — The highest-weight round. Build something functional. Often more complex than Big Tech machine coding.

**Round 2: Theory / Problem Solving (45-60 min)** — JavaScript concepts, browser fundamentals, and short coding problems (debounce, Promise.all).

**Round 3: System Design (45-60 min)** — For 3+ year candidates.

**Round 4: Behavioral / Hiring Manager (30-45 min)** — Culture fit, career goals.

### Funded Startups (Series A-C)

**Round 1: Take-home assignment (2-4 hours)** — Build a small project. Evaluated on code quality, architecture, testing.

**Round 2: Technical discussion (60 min)** — Walk through your take-home + theory.

**Round 3: Founder/CTO chat (30-45 min)** — Culture, product sense.

---

## 25.2 Behavioral Round — STAR Method and 7 Must-Prepare Questions

**Situation** — Context in 2-3 sentences. **Task** — YOUR responsibility. **Action** — What YOU did (60% of answer). **Result** — Quantified outcome.

### The 7 Questions

**1. "Tell me about a time you disagreed with a technical decision."** Show you can push back respectfully AND commit when overruled.

**2. "Describe a project you're most proud of."** Measurable impact. "I led" not "we did."

**3. "How do you handle tight deadlines?"** Prioritization: what you cut (scope), what you kept (quality), how you communicated trade-offs.

**4. "Tell me about a time you mentored someone."** Code reviews, pair programming, documentation. Even juniors have done this.

**5. "How do you prioritize when everything is urgent?"** Actual vs perceived urgency. Highest business impact first. Negotiate timelines for the rest.

**6. "Describe a time you had to learn something quickly."** Show your learning process, not just the outcome.

**7. "Tell me about a bug that was really hard to track down."** Systematic debugging process matters more than the bug itself.

**Tips:** Keep each answer under 3 minutes. Prepare 5-6 rotatable stories. Always end with what you learned.

---

## 25.3 Salary Negotiation

### Understanding Compensation

javascript

```
Total Compensation (TC) = Base + Bonus + Equity + Benefits

  Indian Product Company (SDE-2, 4 years):
    Base: ₹18-25 LPA | Bonus: ₹2-5 LPA | ESOPs: ₹3-10 LPA
    TC: ₹23-40 LPA

  Big Tech India (L4/SDE-2, 4 years):
    Base: ₹25-35 LPA | Bonus: ₹3-6 LPA | RSUs: ₹8-20 LPA
    TC: ₹36-60 LPA
```

### Research Before the Interview

[levels.fyi](http://levels.fyi), Glassdoor, AmbitionBox, Blind, Grapevine/Fishbowl.

### Negotiation Scripts

**"What's your current salary?":** "I'd prefer to focus on the value I'd bring and the market rate. Based on my research, the range is [X to Y]. I'd be comfortable in the upper half given my [specific relevant skill]."

**When given an offer:** "Thank you. I'd like time to review the full package. Can I get back by [date]?" — NEVER accept or negotiate on the spot.

**Counter-offer:** "Based on my experience with [skill], I was hoping the base could be closer to [X]. Is there flexibility?"

**What most candidates don't negotiate:** Signing bonus, ESOP grant size, review cycle timing, remote flexibility, title.

---

## 25.4 Resume and Portfolio

### The 10-Second Scan

Hiring managers check: current role/company, years of experience, tech stack (React, TypeScript, Next.js), impact numbers.

### Impact Descriptions

javascript

```
BAD: "Worked on the frontend of an e-commerce app using React and Redux."

  GOOD: "Built product search for a marketplace serving 2M monthly users.
  Implemented faceted search with instant filtering, reducing search-to-purchase
  time by 35%. Migrated Redux to TanStack Query, cutting bundle size by 28KB."
```

**Formula:** [Action verb] + [specific feature] + [scale] + [measurable impact]

**GitHub:** 2-3 polished projects with READMEs, live demos, tests.

**Portfolio site:** Fast (Lighthouse > 90), accessible, well-designed. The site itself is a portfolio piece.

---

## 25.5 30-Day Preparation Plan

### Track A: 1-3 Years Experience

javascript

```
Week 1: JavaScript core, HTML/CSS (Ch 1-3), 2 machine coding components
  Week 2: React hooks (Ch 16), TypeScript basics (Ch 4A), 3 MC components
  Week 3: Web fundamentals (Ch 5), Data structures (Ch 22), JS polyfills (Ch 17)
  Week 4: Web security (Ch 6), full MC mock, behavioral prep (Ch 25.2),
          quick revision (Ch 25B), mock interview, rest
```

### Track B: 3-6 Years Experience

javascript

```
Week 1: Rendering patterns (Ch 7), system design (Ch 11, 3 problems),
          design patterns (Ch 9)
  Week 2: Optimization (Ch 8), TypeScript advanced (Ch 4B-C),
          advanced MC components
  Week 3: System design (5-6 more problems), state management (Ch 10),
          testing + build tools (Ch 21-22)
  Week 4: JS problems SDE2 (Ch 18), Next.js (Ch 14), system design mock,
          MC mock, behavioral + salary research, revision, rest
```

**Daily:** 2-3 hours. Morning: read/study (1hr). Evening: practice/code (1-2hr).

---

## Learn More

- [Front-End Interview Playbook greatfrontend.com](https://www.greatfrontend.com/front-end-interview-playbook)
- [How to prepare for front-end interviews YouTube](https://www.youtube.com/watch?v=i6s4FhqlSwM)
- [Ultimate front-end interview prep guide YouTube](https://www.youtube.com/watch?v=i5gRxiQFupU)
- [How to prepare for FE coding interviews YouTube](https://www.youtube.com/watch?v=YM-9nPLqezQ)
