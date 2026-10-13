# Node.js Backend Kit — Index

All lessons in reading order. Lessons 04–70 are the original course backup; **new** marks lessons written for this kit (01–03 fill the missing intro slots, 71–117 extend the course), and **+ additional topics** marks original lessons with an appended `## Additional topics` section.

Reports and maps: [CORRECTIONS.md](CORRECTIONS.md) · [ASSET_MAP.md](ASSET_MAP.md) · [ASSET_ISSUES.md](ASSET_ISSUES.md) · [MISSING_DIAGRAMS.md](MISSING_DIAGRAMS.md) · [REPORT-PROMPT-1.md](REPORT-PROMPT-1.md) · [REPORT-PROMPT-2.md](REPORT-PROMPT-2.md) · [REPORT-PROMPT-3.md](REPORT-PROMPT-3.md) · [REPORT-PROMPT-4.md](REPORT-PROMPT-4.md) · [REPORT-PROMPT-5.md](REPORT-PROMPT-5.md) · [REPORT-PROMPT-7.md](REPORT-PROMPT-7.md)

## Foundations

1. [What Node.js Is: Runtime, Layers and Lifecycle](01-what-nodejs-is.md) — **new**
2. [V8: How Node Runs Your JavaScript](02-v8-the-javascript-engine.md) — **new**
3. [Installing and Managing Node](03-installing-and-managing-node.md) — **new**
4. [The Global Object, process, and Environment](04-the-global-object-process-and-environment.md) — **+ additional topics**
5. [Running Node: Scripts, the REPL, the CLI, and nvm](05-running-node-scripts-the-repl-the-cli-and-nvm.md) — **+ additional topics**

## Event loop internals

6. [The Event Loop Phases](06-the-event-loop-phases.md) — **+ additional topics**
7. [libuv & the Thread Pool](07-libuv-the-thread-pool.md) — **+ additional topics**
8. [process.nextTick vs setImmediate](08-processnexttick-vs-setimmediate.md)
9. [Microtasks vs Macrotasks](09-microtasks-vs-macrotasks.md) — **+ additional topics**
10. [Blocking the Event Loop](10-blocking-the-event-loop.md) — **+ additional topics**

## Modules and npm

11. [CommonJS Modules](11-commonjs-modules.md) — **+ additional topics**
12. [ES Modules](12-es-modules.md) — **+ additional topics**
13. [Module Resolution](13-module-resolution.md) — **+ additional topics**
14. [package.json & Semantic Versioning](14-packagejson-semantic-versioning.md) — **+ additional topics**
15. [npm & Scripts](15-npm-scripts.md) — **+ additional topics**

## Async patterns

16. [Callbacks](16-callbacks.md) — **+ additional topics**
17. [Promises](17-promises.md) — **+ additional topics**
18. [Async / Await](18-async-await.md) — **+ additional topics**
19. [Error Handling in Async Code](19-error-handling-in-async-code.md) — **+ additional topics**
20. [EventEmitter](20-eventemitter.md) — **+ additional topics**

## Core modules

21. [The fs Module: Reading & Writing Files](21-the-fs-module-reading-writing-files.md) — **+ additional topics**
22. [path, os & URL: Portable Filesystem and Environment Info](22-path-os-url-portable-filesystem-and-environment-info.md) — **+ additional topics**
23. [Buffer: Working with Binary Data](23-buffer-working-with-binary-data.md) — **+ additional topics**
24. [crypto: Hashing, HMAC, Random & Encryption](24-crypto-hashing-hmac-random-encryption.md) — **+ additional topics**
25. [Timers: setTimeout, setInterval, setImmediate & the Event Loop](25-timers-settimeout-setinterval-setimmediate-the-event-loop.md) — **+ additional topics**

## Streams

26. [Streams Overview](26-streams-overview.md)
27. [Readable & Writable Streams](27-readable-writable-streams.md) — **+ additional topics**
28. [Transform & Duplex Streams](28-transform-duplex-streams.md) — **+ additional topics**
29. [Backpressure, pipe & pipeline](29-backpressure-pipe-pipeline.md) — **+ additional topics**

## Raw HTTP

30. [The http module & createServer](30-the-http-module-createserver.md) — **+ additional topics**
31. [Parsing requests & writing responses](31-parsing-requests-writing-responses.md) — **+ additional topics**
32. [Manual routing & serving JSON](32-manual-routing-serving-json.md) — **+ additional topics**
33. [HTTP vs HTTPS, TLS, HTTP/2 & CORS](33-http-vs-https-tls-http2-cors.md) — **+ additional topics**

## Express

34. [Express basics: what it is and why a framework](34-express-basics-what-it-is-and-why-a-framework.md) — **+ additional topics**
35. [Routing: params, query, and Routers](35-routing-params-query-and-routers.md) — **+ additional topics**
36. [Middleware: the pipeline, next(), and order](36-middleware-the-pipeline-next-and-order.md) — **+ additional topics**
37. [Error handling: the 4-arg handler and async errors](37-error-handling-the-4-arg-handler-and-async-errors.md) — **+ additional topics**
38. [The req and res objects: reading and responding](38-the-req-and-res-objects-reading-and-responding.md) — **+ additional topics**

## REST API design

39. [REST Principles: Constraints, Resources & Statelessness](39-rest-principles-constraints-resources-statelessness.md) — **+ additional topics**
40. [CRUD, HTTP Verbs, Status Codes & Idempotency](40-crud-http-verbs-status-codes-idempotency.md) — **+ additional topics**
41. [Validation, Pagination, Filtering & Sorting](41-validation-pagination-filtering-sorting.md) — **+ additional topics**
42. [API Versioning, Error Shape, Rate Limiting & Docs](42-api-versioning-error-shape-rate-limiting-docs.md) — **+ additional topics**

## Databases

43. [MongoDB & Mongoose](43-mongodb-amp-mongoose.md) — **+ additional topics**
44. [Schema Design & Data Modeling](44-schema-design-amp-data-modeling.md) — **+ additional topics**
45. [SQL Basics from Node](45-sql-basics-from-node.md) — **+ additional topics**
46. [Connection Pooling](46-connection-pooling.md) — **+ additional topics**
47. [Transactions & ACID](47-transactions-amp-acid.md) — **+ additional topics**

## Auth and security

48. [Authentication Basics](48-authentication-basics.md)
49. [JSON Web Tokens (JWT)](49-json-web-tokens-jwt.md)
50. [Sessions & Cookies](50-sessions-cookies.md)
51. [Password Hashing](51-password-hashing.md)
52. [Web Security & OWASP](52-web-security-owasp.md)

## Reliability

53. [Error Handling Patterns](53-error-handling-patterns.md)
54. [Uncaught Exceptions & Unhandled Rejections](54-uncaught-exceptions-unhandled-rejections.md)
55. [Debugging Node.js](55-debugging-nodejs.md)
56. [Logging](56-logging.md)

## Testing

57. [Testing Fundamentals](57-testing-fundamentals.md)
58. [Unit Testing](58-unit-testing.md)
59. [Integration Testing](59-integration-testing.md)
60. [Mocking & Coverage](60-mocking-amp-coverage.md)

## Scaling and operations

61. [The cluster module](61-the-cluster-module.md) — **+ additional topics**
62. [Worker threads](62-worker-threads.md) — **+ additional topics**
63. [Child processes](63-child-processes.md) — **+ additional topics**
64. [Caching with Redis](64-caching-with-redis.md) — **+ additional topics**
65. [PM2 & Docker](65-pm2-docker.md) — **+ additional topics**
66. [Graceful shutdown & monitoring](66-graceful-shutdown-monitoring.md) — **+ additional topics**

## Advanced architecture

67. [Real-time with WebSockets](67-real-time-with-websockets.md) — **+ additional topics**
68. [GraphQL basics](68-graphql-basics.md) — **+ additional topics**
69. [Microservices in Node](69-microservices-in-node.md) — **+ additional topics**
70. [Message queues & async jobs](70-message-queues-async-jobs.md) — **+ additional topics**

## Modern Node and tooling

71. [TypeScript with Node](71-typescript-with-node.md) — **new**
72. [Modern Node Features: A Guided Tour](72-modern-node-features-tour.md) — **new**
73. [AbortController and AbortSignal: Cancellation in Node](73-abortcontroller-and-abortsignal.md) — **new**
74. [Monorepos and Workspaces](74-monorepos-and-workspaces.md) — **new**

## Outbound HTTP, networking, core modules and Web APIs

75. [Outbound HTTP: fetch, undici and http.request](75-outbound-http-fetch-undici-and-http-request.md) — **new**
76. [TCP with net: Sockets, Framing and a Chat Server](76-tcp-with-net.md) — **new**
77. [UDP with dgram](77-udp-with-dgram.md) — **new**
78. [readline: Lines, Prompts and History](78-readline.md) — **new**
79. [util: inspect, format, types and More](79-util-module.md) — **new**
80. [zlib: gzip, deflate and Brotli](80-zlib-compression.md) — **new**
81. [dns: lookup vs resolve](81-dns-module.md) — **new**
82. [The vm Module (and Why It Is Not a Sandbox)](82-vm-module.md) — **new**
83. [Web Streams in Node](83-web-streams.md) — **new**
84. [Binary Web APIs: TextEncoder, Blob, File, atob/btoa and structuredClone](84-binary-web-apis.md) — **new**
85. [Web Crypto: crypto.subtle in Node](85-web-crypto.md) — **new**

## Express in depth

86. [Express App Settings and Locals](86-express-app-settings-and-locals.md) — **new**
87. [Serving Static Files with express.static](87-express-serving-static-files.md) — **new**
88. [Template Engines and Server-Side Rendering](88-template-engines-and-server-side-rendering.md) — **new**
89. [File Uploads in Express](89-file-uploads-in-express.md) — **new**
90. [Cookies in Express](90-cookies-in-express.md) — **new**
91. [File Responses: sendFile, download and attachment](91-express-file-responses.md) — **new**
92. [Express req and res: Complete API Reference](92-express-request-response-api-reference.md) — **new**
93. [Express Routing Extras](93-express-routing-extras.md) — **new**
94. [Migrating from Express 4 to Express 5](94-express-4-to-5-migration-guide.md) — **new**
95. [Structuring an Express Project](95-express-project-structure.md) — **new**
96. [Alternatives to Express: NestJS, Fastify, Koa and Hono](96-express-alternatives-nestjs-fastify-koa-hono.md) — **new**

## Databases in depth: MongoDB, ORMs, query builders and migrations

97. [MongoDB Aggregation Pipeline](97-mongodb-aggregation-pipeline.md) — **new**
98. [MongoDB Schema Patterns](98-mongodb-schema-patterns.md) — **new**
99. [MongoDB Indexes in Depth](99-mongodb-indexes-in-depth.md) — **new**
100. [MongoDB Sharding](100-mongodb-sharding.md) — **new**
101. [Prisma ORM](101-prisma-orm.md) — **new**
102. [Drizzle ORM](102-drizzle-orm.md) — **new**
103. [Sequelize and TypeORM](103-sequelize-and-typeorm.md) — **new**
104. [Knex Query Builder](104-knex-query-builder.md) — **new**
105. [Database Migrations and Seeding](105-database-migrations-and-seeding.md) — **new**
106. [Choosing Between Raw SQL, a Query Builder and an ORM](106-choosing-raw-sql-query-builder-or-orm.md) — **new**

## Production: CI/CD, deployment, observability, performance and runtimes

107. [CI/CD with GitHub Actions](107-ci-cd-with-github-actions.md) — **new**
108. [Docker Compose: App, Postgres and Redis](108-docker-compose.md) — **new**
109. [Kubernetes for Node Developers](109-kubernetes-for-node-developers.md) — **new**
110. [Cloud and Serverless Deployment](110-cloud-and-serverless-deployment.md) — **new**
111. [Observability with OpenTelemetry](111-observability-with-opentelemetry.md) — **new**
112. [APM and Alerting](112-apm-and-alerting.md) — **new**
113. [Scheduled Jobs](113-scheduled-jobs.md) — **new**
114. [Load Testing and Profiling](114-load-testing-and-profiling.md) — **new**
115. [Performance Tuning Checklist](115-performance-tuning-checklist.md) — **new**
116. [Internationalization in Node and Express](116-internationalization.md) — **new**
117. [Bun and Deno Compared with Node](117-bun-and-deno-compared-with-node.md) — **new**
