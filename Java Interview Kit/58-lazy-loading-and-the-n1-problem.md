---
title: "Lazy Loading and the N+1 Problem"
source: https://www.geekyfrontend.in/learn/java-interview-kit/lazy-loading-and-n-plus-one
course: Java Interview Kit
tags: [geeky-frontend, java-interview-kit]
---

# Lazy Loading and the N+1 Problem

*8 min read · Updated 28 Jul 2026 Topic 58 of 65 Bookmark Mark done Why it matters N · Topic 58 of 65*

**Source:** [https://www.geekyfrontend.in/learn/java-interview-kit/lazy-loading-and-n-plus-one](https://www.geekyfrontend.in/learn/java-interview-kit/lazy-loading-and-n-plus-one)

---

## Why it matters

N+1 is the most common performance bug in Java applications, and the most invisible: the code is clean, the tests pass, and it is fast in development with five rows of test data. In production, a page that should issue one query issues four hundred. Every JPA interview covers it, and being able to name four different fixes — and say why `EAGER` is not one of them — is a strong answer.

## The concept, simply

**Lazy loading** means an association is not fetched until touched; Hibernate hands you a proxy and issues a query when you use it. That is the right default — but touching a lazy association inside a loop issues one query *per iteration*.

The N+1 problem: one query to fetch the list, then one MORE per element
for (Order o : orders) { o.getCustomer().getName(); } ← the loop is innocent; the mapping is not

✘ lazy association, accessed in a loop

SELECT \* FROM orders ← 1 query

SELECT \* FROM customers WHERE id = 1

SELECT \* FROM customers WHERE id = 2

… once per order ← N queries
100 orders → 101 round trips. Fast in dev with 5 rows.

✔ four fixes, best first

JOIN FETCH in JPQL
…FROM Order o JOIN FETCH o.customer

@EntityGraph on the repository method
declarative, no query string

@BatchSize(size = 50)
N queries → N/50 — good for collections

a DTO projection — fetch only what you need

⚠ EAGER is NOT the fix
— it just moves the N+1 to every query that touches the entity, whether you need the association or not.
Keep associations LAZY and fetch eagerly per query. Detect it by logging SQL and counting statements in tests.

One query for the list, then N more. The loop looks innocent — the cost is entirely in how the association was mapped and fetched.

## How it works

The defaults are worth memorising because they are inconsistent: **`@ManyToOne` and `@OneToOne` are EAGER by default; `@OneToMany` and `@ManyToMany` are LAZY.** That surprises almost everyone, and the eager to-one default is a common hidden cause of over-fetching.

The four fixes, in order of preference:

| Fix | When |
| --- | --- |
| `JOIN FETCH` in JPQL | You control the query and know what you need |
| `@EntityGraph` | Declarative, on a derived repository method |
| `@BatchSize` | Collections — turns N queries into N/size |
| DTO projection | You only need a few columns — best of all |

**`EAGER` is not a fix.** It moves the problem: the association is now fetched by *every* query touching the entity, whether or not that code path needs it. The correct approach is to keep everything `LAZY` and fetch eagerly per query.

## Code examples

java

```
// ✘ N+1. One query for orders, then one per order for the customer.
List<Order> orders = repository.findByStatus(NEW);
for (Order o : orders) log.info(o.getCustomer().getName());

// ✔ 1. JOIN FETCH — one query, explicit
@Query("SELECT o FROM Order o JOIN FETCH o.customer WHERE o.status = :status")
List<Order> findByStatusWithCustomer(@Param("status") OrderStatus status);

// ✔ 2. @EntityGraph — same effect, declarative, works with derived methods
@EntityGraph(attributePaths = {"customer", "lines"})
List<Order> findByStatus(OrderStatus status);

// ✔ 3. @BatchSize on the association or entity — N queries become N/50
@OneToMany(mappedBy = "order")
@BatchSize(size = 50)
private List<OrderLine> lines;

// ✔ 4. A projection — fetch two columns instead of whole object graphs
@Query("SELECT new com.app.OrderSummary(o.reference, c.name) " +
       "FROM Order o JOIN o.customer c WHERE o.status = :status")
List<OrderSummary> findSummaries(@Param("status") OrderStatus status);
```

The trap in fetch-joining more than one collection:

java

```
// ✘ MultipleBagFetchException — you cannot JOIN FETCH two List collections.
@Query("SELECT o FROM Order o JOIN FETCH o.lines JOIN FETCH o.payments")

// ✔ Option A: make them Sets rather than Lists.
// ✔ Option B: fetch one and use @BatchSize for the other.
// ✔ Option C: two queries — the persistence context stitches them together,
//   because the identity guarantee means the second query populates the
//   SAME objects the first returned.

// ⚠ Also: JOIN FETCH plus pagination silently loads EVERYTHING into memory
//   and paginates there. Hibernate logs
//   "firstResult/maxResults specified with collection fetch; applying in memory"
//   — treat that warning as an error. Use @BatchSize or two queries instead.
```

Catching it before production, which is the answer interviewers want:

properties

```
# Development only — never in production, it is enormous and slow.
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true
logging.level.org.hibernate.SQL=DEBUG

# Better: fail the build. Datasource-proxy or the Hypersistence utilities
# can assert a query COUNT in a test:
#   assertSelectCount(1);
# That turns "someone will notice eventually" into a failing test.
```

## Under the hood

Hibernate implements lazy loading with **proxies** — a generated subclass for a to-one association, a lazy wrapper for a collection. This is why entity classes and their getters must not be `final`, and why `getClass()` in an entity's `equals` fails against a proxy.

**Why the problem is invisible in tests:** with ten rows, 11 queries against an in-memory H2 take microseconds. With ten thousand rows against a database across a network, 10,001 round trips at even 1 ms each is over ten seconds. The bug does not scale linearly into visibility — it arrives all at once with real data volume. That is the argument for asserting query counts rather than relying on noticing.

**The wider lesson** is worth stating in an interview: an ORM hides the database, and hiding it is exactly why these problems occur. The habit that prevents nearly all of them is simply to look at the generated SQL during development — most JPA performance bugs are visible in the log the first time the code runs.

## Common mistakes

- **Switching to `EAGER` to fix N+1.** Moves the cost to every query instead.
- **Not knowing `@ManyToOne` is eager by default.** Silent over-fetching everywhere.
- **Fetch-joining two collections.** `MultipleBagFetchException`.
- **Combining `JOIN FETCH` with pagination.** Paginates in memory after loading everything.
- **Never looking at the SQL.** Nearly every JPA performance bug is visible in the log.
- **Returning entities from controllers.** Serialisation touches lazy associations and triggers N+1 in the web layer.
- **Relying on tests with tiny datasets.** Assert query counts instead.

## Interview lens

> **Q: "What is the N+1 problem?"**  
> A: One query loads a list of N entities, then touching a lazy association on each issues one more query per element — N+1 in total. The loop looks completely innocent; the cost comes from the mapping and the fetch strategy. It is invisible in development because ten rows against an in-memory database is fast, and catastrophic in production with real volume and network latency.

> **Q: "How do you fix it?"**  
> A: Four ways. `JOIN FETCH` in a JPQL query when I control the query. `@EntityGraph` for the same effect declaratively on a derived method. `@BatchSize`, which turns N queries into N divided by the batch size — good for collections. Or a DTO projection that selects only the columns needed, which is usually best because it avoids loading entities at all.

> **Q: "Why not just make it EAGER?"**  
> A: Because eager fetching cannot be turned off per query. The association is then loaded by every query that touches the entity, including the many code paths that never use it — so you have replaced N+1 on one screen with permanent over-fetching everywhere. The right model is LAZY by default, fetched eagerly per query where needed.

> **Q: "What are the default fetch types?"**  
> A: `@ManyToOne` and `@OneToOne` are EAGER; `@OneToMany` and `@ManyToMany` are LAZY. The to-one default catches people out constantly — it means every load of an entity silently drags its parents along unless you set `fetch = FetchType.LAZY` explicitly.

> **Q: "How would you detect it?"**  
> A: Enable SQL logging in development and actually read it — most of these bugs are obvious the first time the code runs. Better, assert query counts in integration tests with something like datasource-proxy, so a regression fails the build rather than waiting to be noticed. In production, an APM tool showing queries per request surfaces it quickly.

> **Q: "What happens if you fetch-join a collection with pagination?"**  
> A: Hibernate cannot apply the limit in SQL without losing rows, so it loads the entire result set into memory and paginates there — and logs a warning saying exactly that. On a large table it is an `OutOfMemoryError` waiting to happen. Use `@BatchSize` or a two-query approach, where the second query populates the same objects thanks to the persistence context's identity guarantee.

## Cheat-card

- N+1 = 1 query for the list + **1 per element** for a lazy association.
- Defaults: **`@ManyToOne`/`@OneToOne` EAGER**, `@OneToMany`/`@ManyToMany` LAZY.
- Fixes: **`JOIN FETCH` · `@EntityGraph` · `@BatchSize` · DTO projection**.
- **`EAGER` is not a fix** — it over-fetches on every query instead.
- Two collection fetch-joins → **`MultipleBagFetchException`** (use Sets, or batch one).
- **`JOIN FETCH` + pagination = in-memory pagination.** Treat the warning as an error.
- Detect with **SQL logging in dev + query-count assertions in tests**.
- Lazy loading uses **proxies** → entities and getters must not be `final`.

---

## Learn More

- [JPA query methods and fetching — Spring Data JPA docs.spring.io](https://docs.spring.io/spring-data/jpa/reference/jpa/query-methods.html)
- [Hibernate ORM user guide — fetching hibernate.org](https://hibernate.org/orm/documentation/)
- [Interview Q&A | Hibernate N+1 Problem & Fix - The Bug You Didn’t Know You Had @Javatechie Java Techie](https://www.youtube.com/watch?v=zwF5ADW6eKs)
- [JOIN FETCH with Hibernate - The end of n+1 select issues Thorben Janssen](https://www.youtube.com/watch?v=QAaTLLYdHJ0)
