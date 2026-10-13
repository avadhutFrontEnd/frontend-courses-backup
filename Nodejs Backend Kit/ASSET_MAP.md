# ASSET_MAP

Inventory of every SVG under `assets/` (the only directory named `assets` in this kit). Built by reading each SVG's text labels (`<text>`/`<tspan>`; the files have no `<title>`, `<desc>` or `aria-label`). All 108 parse as XML and none contains `<script>`.

**Naming pattern:** `i<NN>_<k>_<hash>.svg` — `NN` is the lesson's position in the course backup (`_backup_manifest.json` index), i.e. **lesson number − 3** (`i01` → lesson 04, `i67` → lesson 70); `k` is the figure number within that lesson; `hash` is a content hash (two pairs share a hash and are byte-identical).

**Embedding convention in the lessons:** a Markdown image whose path is `assets/<file>.svg` (relative to the lesson) with descriptive alt text, a blank line, then a one-sentence plain caption. Figures added in the 2026-10-07 repair pass use the requested `*Figure: … Look at: …*` caption instead.

| File path | What it depicts | Concept keywords | Lessons that already embed it | Status |
|---|---|---|---|---|
| assets/i01_01_9bdc8d89.svg | The process object: your program's control panel | process, program's, control, process.argv, command-line, arguments, process.env, environment | 04 | ok |
| assets/i02_01_d8b026e1.svg | Three ways to run Node.js | Three, Node.js, Run, app.js, everyday, REPL, interactive, testing | 05 | ok |
| assets/i02_02_ba74adeb.svg | nvm: switch Node versions per project | Node, versions, project, version, manager, LTS, Legacy | 05 | issue: version labels Node 18/20 are end-of-life — see ASSET_ISSUES.md |
| assets/i03_01_d58df36e.svg | The Event Loop: Six Phases per Tick — Each phase drains its own queue, then hands off to the next. The loop repeats while work remains. | Event, Loop, Six, Phases, Tick, repeats, remains, setTimeout | 06 | ok |
| assets/i03_02_f6f7cf00.svg | Inside One Phase: Drain the Queue — A phase runs its callbacks until the queue empties or a hard limit is hit, then moves on. | Inside, Phase, Drain, Queue, callbacks, empties, fs.read, After | 06 | ok |
| assets/i04_01_4d0a93cc.svg | Node's Threads: One JS Thread, Many Helpers — Your JavaScript is single-threaded. libuv and the OS give Node real concurrency underneath. | Node's, Threads, Thread, Many, Helpers, JavaScript, single-threaded, Node | 01, 07 | ok |
| assets/i04_02_51406ab8.svg | Thread Pool vs Kernel: What Goes Where — Not all async is equal. Some work is truly non-blocking; some is faked with worker threads. | Thread, Pool, Kernel, What, Goes, Where, Some, non-blocking | 07, 81 | ok |
| assets/i05_01_f4f3de83.svg | nextTick vs Promise vs Timer vs Immediate — Priority from a running callback: nextTick first, then promises, then macrotask phases. | nextTick, Promise, Timer, Immediate, Priority, running, callback, promises | 08 | ok |
| assets/i05_02_6c3fcc09.svg | One Loop Turn, Step by Step — Microtasks squeeze in after every single macrotask callback — not once per phase. | Loop, Turn, Step, Microtasks, squeeze, macrotask, callback, nextTick | 08 | ok |
| assets/i06_01_94834faf.svg | Microtasks vs Macrotasks — One macrotask runs, then ALL microtasks drain, then the next macrotask. Repeat. | Microtasks, Macrotasks, macrotask, Repeat, setTimeout, setInterval, setImmediate, I/O | 09 | issue: "one macrotask per turn" wording is imprecise — see ASSET_ISSUES.md |
| assets/i06_02_6c3fcc09.svg | One Loop Turn, Step by Step — Microtasks squeeze in after every single macrotask callback — not once per phase. | Loop, Turn, Step, Microtasks, squeeze, macrotask, callback, nextTick | 09 | ok (byte-identical to assets/i05_02_6c3fcc09.svg) |
| assets/i07_01_f1d1847e.svg | A Blocked Loop Serves No One — While synchronous CPU work runs, every other request waits. Latency piles up behind it. | Blocked, Loop, Serves, While, synchronous, CPU, request, Latency | 10 | ok |
| assets/i07_02_3ff080ab.svg | Measuring Event Loop Lag — Lag = how late a timer fires vs when it was due. Rising lag means the loop is falling behind. | Measuring, Event, Loop, Lag, Rising, falling, actually, perf_hooks.monitorEventLoopDelay | 10, 115 | ok |
| assets/i08_01_d4aee3f6.svg | The Module Wrapper Function — Node silently wraps every file before running it | Module, Wrapper, Function, Node, silently, running, math.js, module.exports | 11 | ok |
| assets/i08_02_41a518d2.svg | require() Caches by Resolved Path — A module runs once; later requires reuse the same exports | require, Caches, Resolved, Path, requires, exports, require('./db, require.cache | 11 | ok |
| assets/i09_01_73d3711b.svg | CommonJS vs ES Modules — Two module systems that coexist in modern Node | CommonJS, Modules, Two, systems, coexist, Node, CJS, Files | 12 | issue (low): "Load: import — asynchronous" and "Files: .js (default)" rows are now incomplete (require(esm), syntax detection) — see ASSET_ISSUES.md |
| assets/i10_01_27074a5e.svg | How Node Resolves a Specifier — require('x') / import 'x' — three kinds of specifier | How, Node, Resolves, Specifier, require('x, Relative, absolute, dir/index.js | 13, 74 | ok |
| assets/i10_02_e60cb885.svg | The "exports" Field & Conditions — A package controls exactly what and how it can be imported | exports, Field, Conditions, package, controls, exactly, imported, package.json | 13 | ok |
| assets/i11_01_68bcf72b.svg | Semantic Versioning & Ranges | Semantic, Versioning, Ranges, MAJOR, MINOR, PATCH, breaking, feature | 14 | ok |
| assets/i12_01_90755c45.svg | npm Script Lifecycle Hooks — Running npm run build fires three scripts in order | Script, Lifecycle, Hooks, Running, scripts, prebuild, actually, postbuild | 15 | ok |
| assets/i13_01_a8b892c3.svg | Error-first callback flow | Error-first, callback, Caller, fs.readFile(path, Async, cb(err, invoked, failure | 16 | ok |
| assets/i13_02_38a50215.svg | Callback hell: the rightward drift — getUser(id, (e, user) => { | Callback, rightward, getUser(id, getOrders(user, getItems(orders, charge(items, repeats, pyramid | 16 | ok |
| assets/i14_01_9c7501db.svg | Promise states are one-way | Promise, one-way, pending, settled, fulfilled, resolve(value, receives, rejected | 17 | ok |
| assets/i14_02_11c5707a.svg | Promise callbacks run as microtasks | Promise, callbacks, microtasks, Call, completion, Microtask, queueMicrotask, Macrotask | 17 | ok |
| assets/i14_03_e1dd7f93.svg | Four Promise combinators | Four, Promise, combinators, Promise.all, rejects, immediately, fail-fast, running | 17 | ok |
| assets/i15_01_55bcdfc8.svg | await pauses, it does not block | getUser, console.log(u.name, Reads, top-to-bottom, synchronous, Engine, getUser().then(u, Suspends | 18 | ok |
| assets/i15_02_11c5707a.svg | Promise callbacks run as microtasks | Promise, callbacks, microtasks, Call, completion, Microtask, queueMicrotask, Macrotask | 18 | ok (byte-identical to assets/i14_02_11c5707a.svg) |
| assets/i15_03_7088db7b.svg | Sequential vs parallel await — Sequential — await in a row (300ms) | Sequential, parallel, previous, Promise.all, Start, together, slowest | 18 | ok |
| assets/i16_01_5be5f216.svg | Where async errors surface | Where, surface, Callback, arrives, try/catch, Promise, rejection, travels | 19 | ok |
| assets/i17_01_18b4f9c1.svg | EventEmitter: emit fans out to listeners | EventEmitter, listeners, emitter, emit('data, payload, listener, on('data, once('data | 20 | ok |
| assets/i18_01_2357b581.svg | Three ways to read a file | Three, readFileSync, returns, fs.readFile, offloads, cb(err, fs/promises, readFile | 21 | ok |
| assets/i18_02_ee4d6eee.svg | Why sync I/O hurts a server | Why, I/O, readFileSync, everyone, readFile, resumes, Async, serving | 21 | ok |
| assets/i19_01_425594bf.svg | Anatomy of a path | Anatomy, home/app/data/report.csv, dirname, home/app/data, basename, report.csv, path.parse, returns | 22 | ok |
| assets/i20_01_b2609b12.svg | A Buffer is fixed raw bytes — Buffer.from("Hi") — UTF-8 encodes each character to bytes | Buffer, Buffer.from, UTF-8, encodes, character, allocated, alloc(n, zero-filled | 23 | ok |
| assets/i20_02_639480ec.svg | Same bytes, different views | Same, different, Buffer, Node, e6f6465, digests, Tm9kZQ, transport | 23 | ok |
| assets/i21_01_b5b96da8.svg | Hash vs HMAC vs password hash | Hash, HMAC, password, createHash, SHA-256, integrity, one-way, passwords | 24 | ok |
| assets/i22_01_094ff453.svg | Where timers fire in the event loop | Where, setTimeout, setInterval, I/O, callbacks, setImmediate, Between, process.nextTick | 25 | issue: microtasks drain "between every phase" (they drain after every callback) — see ASSET_ISSUES.md |
| assets/i23_01_537b76f3.svg | Streaming vs. Buffering the Whole File — A 4 GB file — read all at once, or in small chunks | Streaming, Buffering, Whole, File, Buffer, Load, RAM, High | 26 | ok |
| assets/i23_02_ec427c97.svg | The Four Stream Types | Four, Stream, Types, Readable, Source, HTTP, request, process.stdin | 26, 28, 76 | ok |
| assets/i24_01_c085ea4d.svg | Readable Modes: Paused vs. Flowing | Readable, Modes, Paused, Flowing, on('readable, stream.read, Data, Default | 27 | ok |
| assets/i24_02_e1ea38ee.svg | highWaterMark: the Internal Buffer Limit — write() returns a boolean based on buffered bytes vs. highWaterMark | highWaterMark, Internal, Buffer, Limit, returns, boolean, buffered, default | 27 | issue: says "default 16 KB" (outdated since Node 22) — see ASSET_ISSUES.md |
| assets/i25_01_8f41e854.svg | A Transform Chain (compress a file) | Transform, Chain, compress, Readable, file.txt, encrypt, Writable, file.txt.gz.enc | 28, 80 | ok |
| assets/i26_01_e8e417ed.svg | Backpressure: the Slow Sink Pushes Back | Backpressure, Slow, Sink, Pushes, Back, Fast, Readable, SSD | 29, 76 | ok |
| assets/i26_02_6159e80e.svg | pipe() vs. pipeline() on Error | pipeline, Error, a.pipe(b).pipe(c, destroyed, handles, sockets, pipeline(a, automatically | 29 | ok |
| assets/i27_01_9768f8c5.svg | The HTTP request / response lifecycle | HTTP, request, response, lifecycle, Client, browser, http.Server, createServer | 30 | ok |
| assets/i27_02_74703027.svg | createServer sits on the event loop | createServer, http.createServer(fn, listen(3000, request, incoming, connection, TCP, lifecycle | 30 | ok |
| assets/i28_01_dca6aee2.svg | Anatomy of an HTTP request | Anatomy, HTTP, request, Raw, POST, HTTP/1.1, Host, api.example.com | 31 | ok |
| assets/i29_01_610f0065.svg | Manual routing: match method + path | Manual, routing, Incoming, request, GET, api/users, URL(req.url, pathname | 32 | ok |
| assets/i30_01_003d9731.svg | The TLS handshake (simplified) | TLS, handshake, simplified, Client, Server, ClientHello, versions, ciphers | 33 | ok |
| assets/i30_02_0d3eb5e7.svg | HTTP/1.1 vs HTTP/2 over one connection | HTTP/1.1, HTTP/2, connection, keep-alive, request, response, Serial, head-of-line | 33, 75 | ok |
| assets/i31_01_536a2185.svg | Raw http vs Express: who does the work? | Raw, Express, URL, yourself, headers, middleware, helpers, control | 34 | ok |
| assets/i31_02_2b6379fc.svg | A request's journey through an Express app | request's, journey, through, Express, Client, HTTP, request, Middleware | 34 | ok |
| assets/i32_01_1d9139e2.svg | Anatomy of a route | Anatomy, app.get, HTTP, users/:id, pattern, handler, function, req.params.id | 35 | ok |
| assets/i32_02_bfe22857.svg | Mounting Routers on a base path | Mounting, Routers, app.use('/api/users, usersRouter, mounted, api/users, router.get('/, GET | 35, 93 | ok |
| assets/i33_01_7f62f706.svg | The middleware chain (call next to go deeper) | middleware, express.json, authenticate, handler, res.json(data, request, travels, response | 36 | ok |
| assets/i33_02_4aebd34f.svg | What next() decides | What, decides, middleware, res.send, res.json, responds, registration, next(err | 36, 42 | ok |
| assets/i34_01_6ef3f077.svg | How an error skips ahead | How, middleware, handler, next(err, SKIPPED, arguments, straight, Express | 37, 94 | ok |
| assets/i35_01_be4c0901.svg | req reads the request, res writes the response | request, response, incoming, req.params, users/:id, req.query, req.body, req.headers | 38, 92 | ok |
| assets/i36_01_4e8d3a7b.svg | The six REST constraints | REST, constraints, Client-Server, separate, storage, Stateless, requests, Cacheable | 39 | ok |
| assets/i36_02_af6f2f0c.svg | REST vs RPC vs GraphQL | REST, RPC, GraphQL, GET, users/42, endpoints, HTTP, friendly | 39 | ok |
| assets/i37_01_0ff846bf.svg | CRUD to HTTP verbs and status codes | CRUD, HTTP, Success, Idempotent, Read, GET, Create, POST | 40 | ok |
| assets/i37_02_b53e0eaa.svg | The five status-code families | status-code, families, Info, Continue, Success, Created, Content, Redirect | 40 | ok |
| assets/i38_01_5fdc1a01.svg | Validation at the edge | Validation, Request, Validate, Joi, Controller, Never, unvalidated, business | 41 | ok |
| assets/i38_02_a09365f2.svg | Offset vs cursor pagination | Offset, pagination, SQL, offsets, skips/repeats, Cursor, eyJpZCI6MTAw, WHERE | 41 | ok |
| assets/i39_01_e26206d8.svg | Three ways to version an API | Three, version, API, URI, v1/users, visible, REST, Custom | 42 | ok |
| assets/i40_01_26cef54d.svg | From your code down to MongoDB | MongoDB, User.find, doc.save, Mongoose, Model, validation, casting, BSON | 43 | ok |
| assets/i41_01_7b178f88.svg | Embedding vs referencing | Embedding, referencing, Embed, everything, Best, together, bounded, Reference | 44, 98 | ok |
| assets/i41_02_00012497.svg | With an index vs without | without, collection, Reads, document, O(n, examined, Index, B-tree | 44, 99 | issue (low): "~20 docs examined, O(log n)" mixes B-tree page visits with documents examined — see ASSET_ISSUES.md |
| assets/i42_01_2e6040e8.svg | Relational tables vs document store | Relational, document, SQL, Ada, x.com, Lin, user_id, Fixed | 45 | ok |
| assets/i43_01_0827e931.svg | How a connection pool works | How, connection, Incoming, requests, Pool, Reused, reopened, Database | 46, 115 | ok |
| assets/i44_01_e052008d.svg | ACID: the four guarantees | ACID, guarantees, Atomicity, half-writes, Consistency, Invariants, Isolation, Concurrent | 47 | ok |
| assets/i44_02_e440b57b.svg | Commit path vs rollback path | Commit, rollback, BEGIN, startTxn, account, durable, nothing, Either | 47 | ok |
| assets/i45_01_9a0bb0ae.svg | Authentication vs Authorization | Authentication, Authorization, AuthN, Who, Verify, identity, credentials, password | 48 | ok |
| assets/i45_02_2d02dd9c.svg | Sessions (stateful) vs Tokens (stateless) | Sessions, stateful, Tokens, stateless, Server-side, Browser, Server, Redis | 48 | ok |
| assets/i46_01_b738841f.svg | Anatomy of a JSON Web Token | Anatomy, JSON, Web, Token, Header, HS256, JWT, algorithm | 49 | ok |
| assets/i46_02_11d4a2da.svg | Access + refresh token flow | Access, refresh, Client, browser, Auth, verifies, credentials, Call | 49 | ok |
| assets/i47_01_853bd6a9.svg | How a server-side session works | How, server-side, session, Browser, Cookie, Express, middleware, express-session | 50 | ok |
| assets/i48_01_656eb6b4.svg | Salted, slow password hashing | Salted, password, hashing, Plain, hunter2, KDF, b$12$Nq9k...saltandhash, separate | 51 | ok |
| assets/i49_01_f07cd3c6.svg | Common web risks in a Node app | Common, Node, Injection, SQL, NoSQL, command, Fix, parameterize | 52 | ok |
| assets/i49_02_4844fb21.svg | A request passing through defense layers | request, passing, through, defense, headers, throttle, CORS, allow-list | 52 | ok |
| assets/i50_01_c76af59d.svg | Anatomy of an Error object | Anatomy, Error, Error(message, instanceof, TypeError, subclass, message, captured | 53 | ok |
| assets/i50_02_ee5dc2c2.svg | Operational vs Programmer errors | Operational, Programmer, expected, running, network, timeout, connection, limited | 53 | ok |
| assets/i51_01_9a6c4bfc.svg | Crash-only: let it die, let it restart | Crash-only, restart, uncaughtException, unhandled, rejection, handler, cleanup, process.exit(1 | 54 | ok |
| assets/i52_01_45d1d270.svg | node --inspect and the DevTools protocol | inspect, DevTools, protocol, Node, process, WebSocket, Inspector, Chrome | 55 | ok |
| assets/i53_01_fe68c416.svg | Log levels: severity from trace to fatal — fatal · process is going down | Log, severity, process, request/op, attention, recoverable, suspicious, degraded | 56 | ok |
| assets/i53_02_6cd24834.svg | A correlation id threads one request | correlation, threads, request, Client, API, gateway, x-request-id, Auth | 56, 111 | ok |
| assets/i54_01_87bb7548.svg | The testing pyramid | testing, pyramid, E2E, Integration, Unit, isolated, confidence, Push | 57 | ok |
| assets/i54_02_dfd027b4.svg | What a test runner gives you | What, Jest, batteries, included, coverage, snapshots, ecosystem, ESM | 57 | ok |
| assets/i55_01_429a8718.svg | Arrange · Act · Assert | Arrange, Act, Assert, invocation, observable, behaviour, Three, obvious | 58 | ok |
| assets/i56_01_dc096a03.svg | How supertest drives your API | How, supertest, API, Test, request(app, post('/users, Ephemeral, middleware | 59, 95 | ok |
| assets/i57_01_d5bd1b3d.svg | Test doubles: spy, stub, mock | Test, doubles, Spy, records, function, observe, Stub, replaces | 60 | ok |
| assets/i57_02_c113eff1.svg | The four coverage metrics | coverage, metrics, Statement, weakest, Branch, matters, Function, Line | 60 | ok |
| assets/i58_01_a12f626e.svg | The cluster Primary / Worker Model — One primary forks N workers; the OS shares one listening socket across them. | cluster, Primary, Worker, Model, workers, listening, process, request | 61 | ok |
| assets/i58_02_e545dec8.svg | One Core vs All Cores — A single Node process uses one CPU core; cluster fans work out across every core. | Core, Cores, Node, process, CPU, cluster, Single, default | 61 | ok |
| assets/i59_01_9e938b47.svg | worker_threads vs cluster — Threads share one process & memory; cluster forks separate processes. | worker_threads, cluster, Threads, process, separate, processes, Thread, isolate | 62 | ok |
| assets/i59_02_11cad794.svg | Three Ways to Move Data to a Worker — Copy is safe but slow; transfer hands over ownership; shared is truly concurrent. | Three, Ways, Move, Data, Worker, Copy, transfer, ownership | 62 | ok |
| assets/i60_01_421bc833.svg | child_process: Which Method When — Four doors to the OS — they differ in buffering, shell use, and IPC. | child_process, Which, Method, When, Four, buffering, IPC, Streams | 63 | ok |
| assets/i61_01_cd28d2f3.svg | Cache-Aside (Lazy Loading) Flow — App checks Redis first; on a miss it reads the DB and back-fills the cache. | Cache-Aside, Lazy, Loading, Flow, App, Redis, back-fills, Node | 64 | ok |
| assets/i61_02_859a7119.svg | Write Strategies & the Three Cache Failures — How writes reach the store, and the three ways a cache betrays you under load. | Write, Strategies, Three, Cache, Failures, How, betrays, Write-through | 64 | ok |
| assets/i62_01_f49bfdf5.svg | Multi-Stage Docker Build for Node — Build in a fat stage; copy only the runtime artifacts into a slim final image. | Multi-Stage, Docker, Build, Node, runtime, artifacts, Stage, builder | 65 | issue: shows FROM node:20 / node:20-slim (EOL) — see ASSET_ISSUES.md |
| assets/i63_01_85ccfa6f.svg | Graceful Shutdown on SIGTERM — Stop taking new work, finish what's in flight, close resources, then exit 0. | Graceful, Shutdown, SIGTERM, Stop, resources, Orchestrator, rolling, scale-down | 66, 109 | ok |
| assets/i64_01_417ee1ac.svg | Real-time delivery: Polling vs SSE vs WebSocket | Real-time, delivery, Polling, SSE, WebSocket, HTTP, Client, GET | 67 | ok |
| assets/i64_02_89e8f619.svg | The WebSocket upgrade handshake | WebSocket, upgrade, handshake, Client, Browser, WebSocket(url, Server, Node | 67 | ok |
| assets/i65_01_de542c0f.svg | REST vs GraphQL: fetching one screen | REST, GraphQL, fetching, endpoints, GET, users/1, users/1/posts, posts/9/comments | 68 | ok |
| assets/i65_02_76f1c6dd.svg | How a GraphQL query is served | How, GraphQL, Query, document, Parse, Validate, against, checked | 68 | ok |
| assets/i66_01_b72019ff.svg | Monolith vs Microservices | Monolith, Microservices, deployable, process, Users, Orders, Payments, Email | 69 | ok |
| assets/i67_01_86b9ee37.svg | A message queue decouples producer from work | message, decouples, producer, API, returns, enqueue, Broker, Queue | 70 | ok |
| assets/i67_02_0347e3d7.svg | Work queue vs Pub/Sub | Work, Pub/Sub, competing, message, consumer, Producer, Queue, Worker | 70 | ok |
