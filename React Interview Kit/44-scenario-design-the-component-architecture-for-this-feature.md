---
title: "Scenario: 'Design the Component Architecture for This Feature'"
source: https://www.geekyfrontend.in/learn/react-interview-kit/scenario-design-the-component-architecture-for-this-feature
course: React Interview Kit
tags: [geeky-frontend, react-interview-kit]
---

# Scenario: "Design the Component Architecture for This Feature"

*2 min read · Updated 24 Mar 2026 Topic 44 of 62 Bookmark Mark done 10 · Topic 44 of 62*

**Source:** [https://www.geekyfrontend.in/learn/react-interview-kit/scenario-design-the-component-architecture-for-this-feature](https://www.geekyfrontend.in/learn/react-interview-kit/scenario-design-the-component-architecture-for-this-feature)

---

# 10.2 - Scenario: "Design the Component Architecture for This Feature"

---

## 🎯 Why This Question Exists

This tests your ability to think about component structure before writing code. Interviewers want to see if you can break a feature into well-organized, reusable pieces.

---

## The Framework: A 4-Step Process

### Example: "Design the component architecture for an e-commerce product listing page"

**Step 1: Identify the major sections of the UI**

- Header with search bar and cart icon
- Filter sidebar (category, price range, rating)
- Product grid (product cards with image, name, price, add-to-cart)
- Pagination or infinite scroll
- Sort dropdown (price, popularity, newest)

**Step 2: Define the component tree**

javascript

```
ProductListingPage
  SearchBar
  FilterSidebar
    CategoryFilter
    PriceRangeFilter
    RatingFilter
  SortDropdown
  ProductGrid
    ProductCard (repeated)
      ProductImage
      ProductInfo
      AddToCartButton
  Pagination
```

**Step 3: Determine where state lives**

- **Search query, active filters, sort order, current page:** in ProductListingPage (affects API call)
- **Cart items:** in a CartContext (global, used by Header and AddToCartButton)
- **Product data:** fetched via a `useProducts` custom hook that combines filters + sort + page

**Step 4: Identify data flow and communication**

- FilterSidebar calls `onFilterChange` callback to update parent state
- ProductCard calls `onAddToCart` which dispatches to CartContext
- URL params stay in sync with filters for shareable links (using useSearchParams)

---

## 🗣️ Sample Answer

> "I'd start by identifying the major UI sections: header with search, a filter sidebar, the product grid, and pagination. Then I'd build a component tree with ProductListingPage as the parent owning the filter, sort, and page state. FilterSidebar and SortDropdown communicate back to the parent through callbacks. ProductGrid receives the filtered products as props. Cart state lives in a CartContext so both the header's cart icon and the AddToCartButton can access it. I'd extract the API call into a useProducts custom hook and sync filter state with URL params so users can share filtered views."

---

## Learn More

- [Thinking in React react.dev](https://react.dev/learn/thinking-in-react)
- [Structure your React app: small → large YouTube](https://www.youtube.com/watch?v=QhSXNY8sy_0)
- [The architecture of a scalable React app YouTube](https://www.youtube.com/watch?v=qqvJR1tTBbs)
- [5 React architecture styles YouTube](https://www.youtube.com/watch?v=eoPPTKdXs0U)
