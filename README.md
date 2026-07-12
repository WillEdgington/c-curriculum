# c-curriculum

A self-directed, project-based curriculum for learning C, structured in phases. Each phase covers the concepts needed to tackle a project slightly beyond current ability, forcing "learn as you go". Concepts are introduced before the project that needs them, with multiple resources and rabbit holes to dig into (if you choose to do so). The resources are loose and suggestive as the main emphasis is on the projects.

I believe the most important skill for anyone in software is to "learn how to learn". It is important to be able to seek out the knowledge and understanding of a tool or concept that you need for the thing you are building to the point where you can have complete ownership over your implementation. Like a mouse to cheese. This curriculum does not have the cheese; it just provides a few stinks.

I constructed this curriculum for my own personal use to learn C and low-level computing. Therefore, it is recommended to have some coding/computer science knowledge before working through this curriculum (just enough not to be intimidated and too confused by the concepts).

> Planned collaboratively with AI assistance and updated as I progress through and review each phase. Currently at [Phase 4](#phase-4---low-level-numerics-and-performance), anything further along has not been "battle tested" and refined by me yet.

---

## Contents

- **[Phase 1 - Foundations](#phase-1---foundations)**
- **[Phase 2 - Data Structures and the C Idiom](#phase-2---data-structures-and-the-c-idiom)**
  - **[Phase 2.5 - Bridging Data Structures and Systems](#phase-25---bridging-data-structures-and-systems)**
- **[Phase 3 - Systems Thinking](#phase-3---systems-thinking)**
- **[Phase 4 - Low-Level Numerics and Performance](#phase-4---low-level-numerics-and-performance)**
- **[Phase 5 - Neural Network from Scratch](#phase-5---neural-network-from-scratch)**
- **[Standalone - A Linear Programming Engine](#standalone---a-linear-programming-engine)**
- **[Phase 6 - Interpreter / Compiler](#phase-6---interpreter--compiler)**
- **[Phase 7 - Science Simulator (Open-ended)](#phase-7---science-simulator-open-ended)**

---

## Phase 1 - Foundations
**Goal:** Get comfortable enough with C syntax, memory, and tooling to build something small but real.

### Concepts
- Compilation model: preprocessor → compiler → linker, `gcc`/`clang`, `Makefile` basics
- Primitive types, operators, control flow
- Functions, scope, stack frames (mental model, not assembly)
- Arrays and pointer arithmetic (the core of Phase 1)
- Pointers to pointers, pointer-to-struct patterns
- Strings as `char` arrays, `<string.h>`
- Structs and basic typedef patterns
- Manual memory: `malloc`, `free`, `calloc`, heap vs stack
- Dynamic arrays: geometric growth with `realloc`
- Header files, multi-file projects, `#include` guards
- File I/O: `fopen`, `fread`, `fwrite`, `fclose`
- `<stdio.h>`, `<stdlib.h>`, `<string.h>` (the everyday headers)
- Basic debugging: `gdb`, `valgrind` for memory leaks
- Sanitizer flags: `-fsanitize=address` (ASan), `-fsanitize=undefined` (UBSan) - run these during development on every project

### Resources
- [*C Programming Full Course for free*](https://youtu.be/xND0t1pr3KY?si=HhqefSVryw13gdQV) (for setup and basics)
- [Learn Makefiles](https://makefiletutorial.com) (great for understanding `Makefile`, the build graph and automation)
- *The C Programming Language* (Kernighan & Ritchie): read chapters 1–6 alongside the project, not before
- *Managing Projects with GNU Make - Third Edition* (Mecklenburg): for mastering complex multi-target builds (optional, not so much a need this early on but worth a skim)

### Project - `cjson`: A minimal JSON parser
Parse a subset of JSON (strings, numbers, booleans, null, nested objects, arrays) from a file into a C struct tree, then pretty-print it back.

**Why:** Forces handling of strings, structs, recursive data structures, and manual memory (all in Phase 1 territory). A real problem with a clear correctness signal.

**Stretch:** Add a query interface supporting dot-separated path traversal across nested objects and arrays (e.g. `json_get_path(root, "users.0.name")` returns the matching node).

**Completed Example:** [`cjson`](https://github.com/WillEdgington/cjson)

---

## Phase 2 - Data Structures and the C Idiom
**Goal:** Build common data structures that can be taken for granted in high-level languages. Own memory at a deeper level.

### Concepts
- Linked lists, doubly linked lists
- Hash maps: open addressing and chaining
- Generic programming via `void *`: Handling data without knowing its type at compile-time
- Function pointers: syntax and use cases
- Arena (Region-based) Allocation: Slab-based memory management to reduce fragmentation and malloc overhead
- Cache Locality: How physical memory layout and slab size impact CPU throughput
- Performance Profiling: Measuring throughput (ns/op) and memory footprint (RSS)
- `#define` macros vs `static inline` functions
- Error handling patterns in C (return codes, `errno`)

### Resources
- *The C Programming Language* (Kernighan & Ritchie): read chapters 6–8
- *Hacking\: The Art of Exploitation* (Jon Erickson): chapters 1–2 (optional, great for memory intuition)
- *C Interfaces and Implementations* (David R. Hanson): for modular library design (optional, recommended if you are looking for something to read to cement your understanding)
- *Test-Driven Development for Embedded C* (James W. Grenning): for establishing a test-first workflow in C (optional, again, but great for solidifying understanding)
- [MinUnit](https://jera.com/techinfo/jtns/jtn002): A minimal testing framework for verification (worth a look through to inspire your own test framework)

### Project - `clib`: A Generic Data Structure Library
Build a reusable C library providing fundamental data structures (Vector, HashMap, Arena Allocator) designed to handle generic data using `void *`. Adopt a **TDD** workflow: define the interface in a header, write failing unit tests, implement the logic, and verify that all tests pass. This requires a `Makefile` capable of **multi-target builds** to separate production code from the test suite (e.g. `make` for the library and `make test` for the unit tests).

**Why:** In C, you don't get a standard collections library; you have to build your own. This project forces you to master **generic programming** (managing item_size and memory offsets), **API design**, and **memory architecture**. It transitions you from writing "programs" to writing "tools" that you will import and use in every subsequent phase of this curriculum.

**Stretch:** Implement a **Binary Heap** (Min/Max) and a **Performance Benchmarking** suite. The suite should measure execution time and physical memory impact (RSS) to analyse implementation trade-offs (e.g. comparing chaining vs. open-addressing under high collision rates).

**Completed Example:** [`clib`](https://github.com/WillEdgington/clib)

---

## Phase 2.5 - Bridging Data Structures and Systems
**Goal:** Apply [Phase 2](#phase-2---data-structures-and-the-c-idiom) data structure skills in a networked context before moving to full OS interaction in [Phase 3](#phase-3---systems-thinking).

### Concepts
- TCP sockets: `socket`, `bind`, `listen`, `accept`, `send`, `recv`
- The HTTP/1.1 request/response format and Keep-Alive connection lifecycles
- Parsing raw byte streams and leveraging generic data structures (e.g., `HashMaps` for headers)
- Memory Isolation: Using Arena allocators for request-scoped memory without heap fragmentation
- Concurrency: POSIX threads (`pthreads`), mutexes, condition variables, and thread pools
- Network Guardrails: Socket timeouts (`SO_RCVTIMEO`) and preventing resource starvation (Slowloris)
- Basic file serving: securely resolving paths, reading files, and writing them to a socket

### Resources
- *Computer Systems: A Programmer's Perspective* (CS:APP): chapter 11 (network programming) and chapter 12 (concurrent programming)
- RFC 7230 (HTTP/1.1 message syntax): skim the relevant sections, not cover to cover
- [LLNL POSIX Threads Programming](https://hpc-tutorials.llnl.gov/posix/) (an excellent primer on `pthreads`, mutexes, and condition variables)
- Linux `man` pages for socket and pthread APIs

### Project - `chttp`: A minimal HTTP/1.1 server
Accept TCP connections, parse raw HTTP GET requests, serve static files from a directory, return correct status codes (`200`, `404`, `405`). 

**Why:** Requires sockets, robust string parsing, and dynamic buffers. This is a natural extension of [Phase 2](#phase-2---data-structures-and-the-c-idiom) skills into a networked context, forcing you to use your custom data structures to solve real-world parsing and routing problems. Highly satisfying to test: you can curl it or open it in a browser.

**Stretch Ideas:**
- **Concurrency Engine:** Implement a fixed-size worker thread pool to handle concurrent clients without blocking the main listener thread.
- **Keep-Alive & Security:** Implement connection reuse (`Connection: keep-alive`) and apply socket timeouts to prevent thread hijacking and resource starvation.
- **System Configuration:** Build a parser for a localised configuration file (e.g., `server.toml`) to customise ports, thread counts, and memory footprints at boot.
- **Thread-Safe Auditing:** Create a synchronised logging module protected by a global mutex to record HTTP traffic and system diagnostics without race conditions.
- **Dynamic Templating:** Replace hardcoded error strings with a templating engine that reads HTML files and injects dynamic variables at the correct namespaces (like `{status_code}`).

**Completed Example:** [`chttp`](https://github.com/WillEdgington/chttp)

---

## Phase 3 - Systems Thinking
**Goal:** Understand how C sits on top of the operating system kernel. Transition from writing standard standalone programs to orchestrating isolated execution environments and interacting directly with low-level system states.

### Concepts
- Process execution model: process duplication (`fork`), image replacement (`execvp`), synchronous harvesting (`waitpid`), and status isolation
- Inter-process communication (IPC): N-stage rolling file descriptor allocation loops and data stream piping (`pipe`)
- Resource redirection: low-level input/output file descriptor manipulation and tracking via `dup2`
- Terminal I/O stewardship: shifting between standard line-buffered (canonical) mode and raw character-by-character input mode via `termios` adjustments (`tcsetattr`, `ICANON`, `ECHO`)
- Advanced signal topology: configuring signal barriers, child tracking notifications (`SIGCHLD`), and parental masking of foreground-control signals (`SIGTSTP`, `SIGTTIN`, `SIGTTOU`)
- Job control mechanics: establishing isolated process group boundaries (`setpgid`), manipulating terminal control group ownership (`tcsetpgrp`), and harvesting state changes (`WNOHANG | WUNTRACED | WCONTINUED`)
- Stream directory manipulation: reading and traversing environment paths and file system pointers (`opendir`, `readdir`)
- Structural execution tracking: single-linked pipeline list compilation and short-circuit routing logic

### Resources
- *Computer Systems: A Programmer's Perspective* (CS:APP): chapters 8 (exceptional control flow) and 10 (system-level I/O)
- Linux `man` pages for `fork`, `execvp`, `waitpid`, `pipe`, `dup2`, `termios`, and `tcsetpgrp`
- The Redis source code (`src/sds.c` or general loop handling) for structural intuition on low-level buffer management

### Project - `cshell`: A Unix-compliant Command Line Shell
Build an interactive command-line interface that evaluates multi-stage execution pipelines, safely manages stream resources across disk boundaries, and routes parent environmental modifications natively without subshell degradation.

**Why:** A shell forces you to take complete ownership over process boundaries, asynchronous events, and the system file descriptor layout. It acts as the ultimate bridge between software logic and kernel state, requiring meticulous descriptor hygiene and bulletproof memory determinism to prevent deadlocks or leaks.

**Core Ideas:**
- **Workspace-Aware Prompt:** Implement a custom REPL loop that prints a live prompt string displaying the current working directory. Optimise terminal screen real estate by dynamically truncating the user's `$HOME` path prefix to a clean `~` shorthand notation.
- **Environment & Process Mutators:** Intercept and route state-altering instructions natively within the parent process context. Implement `exit` to break the control loop, `cd` leveraging `chdir()`, and `export KEY=VALUE` using `setenv()` to alter the process block.
- **Arbitrary N-Stage Pipelines:** Orchestrate execution chains containing an arbitrary number of external commands separated by `|` tokens. Utilise a rolling descriptor rotation scheme to connect standard output to downstream standard input without leaking parent resources.
- **Low-Level Stream Redirection:** Intercept file markers (`<` and `>`) to safely divert byte streams across disk boundaries using `dup2()`. Ensure standard built-ins preserve subshell isolation rules: running natively when called standalone, but cascading smoothly into isolated child scopes when piped.

**Stretch Ideas:**
- **Job Control & Process Suspension:** Establish distinct process groups for execution chains. Intercept `CTRL+Z` signal boundaries to suspend foreground tasks, log lifecycle state transitions within a persistent tracking table, and implement built-in commands (`jobs`, `fg`, `bg`) to shift group boundaries dynamically.
- **Raw-Mode Input & Line Editing:** Replace standard block-buffered file streams with a raw terminal interaction engine. Build an ANSI escape sequence state machine capable of tracking cursors logically, handling mid-line gap text modifications, and performing instant viewport erasures (`CTRL+L`) without breaking current input states.
- **Interactive Tab Completion:** Intercept the horizontal tab character (`\t`) within your input reader to run prefix matches. Scan active system binary paths or local directories via system pointers to finish tokens inline, expanding the engine to render a multi-column, aligned selection grid on ambiguous double-tab interactions.
- **Short-Circuit Pipeline Chaining:** Upgrade the lexical analyzer and parser to compile sequential lines into a linked list of independent pipelines connected by logical operators (`&&` and `||`). Route evaluations through a central dispatcher that enforces short-circuit rules based on the live exit status parameter (`$?`).
- **Synchronous Startup Configurations:** Extract your file-scanning routines into a reusable stream utility module. Use it during the initialisation sequence to locate, parse, and evaluate configuration commands within an optional `~/.cshellrc` boot profile before rendering the primary prompt environment.

**Completed Example:** [`cshell`](https://github.com/WillEdgington/cshell)

---

## Phase 4 - Low-Level Numerics and Performance
**Goal:** Learn how hardware handles numeric primitives, floating-point limitations, and memory cache dimensions. Build a high-performance vector and matrix math engine from scratch to serve as the structural backbone for the subsequent neural network phase.

### Concepts
- IEEE 754 floating-point architecture: bitwise layout, precision limits, rounding drops, and handling NaN/Inf states
- Data density trade-offs: `float` vs `double` footprint sizes and spatial efficiency metrics
- Cache-aware programming: Row-Major vs. Column-Major matrix layout traversal and its physical CPU throughput impact
- Compiler optimisation dynamics: Decoding loop vectorisation, pointer aliasing hints (`restrict`), and optimisation boundaries (`-O2`/`-O3`)
- Benchmarking mechanics: Tracking raw clock cycle metrics, arithmetic density ($GFLOPS$), and operational scales
- Software profiling tools: Isolating performance hotspots via hardware counters or execution graph tracking (`perf`, `gprof`)

### Resources
- *What Every Computer Scientist Should Know About Floating-Point Arithmetic* (David Goldberg): read the foundational sections on precision loss
- *Computer Systems: A Programmer's Perspective* (CS:APP): chapter 2 (data representation) and chapter 6 (memory hierarchy)
- Intel/AMD Optimisation Reference Manuals: browse sections highlighting matrix-multiply loop transformations (optional rabbit hole)

### Project - `matlib`: A High-Performance Linear Algebra Primitives Library
Implement a highly optimised matrix mathematics library backed by flat 1D floating-point allocations. Build a companion benchmarking suite to analyze how algorithm structure impacts hardware performance.

**Why:** A neural network requires millions of mathematical mutations per iteration. If your underlying linear algebra code is poorly structured, training models will be completely intractable. Writing this library from scratch forces you to bridge abstract mathematical formulas with physical hardware constraints like CPU L1/L2/L3 caches.

**Core Baseline Requirements:**
Your computational engine must fully implement these primary linear algebra operations before exploring aggressive tuning passes:
- **Flat Memory Primitives:** Matrix allocation, zero-initialisation, and random uniform seeding structures backed exclusively by 1D continuous arrays to enforce contiguous memory layouts.
- **Fundamental Transformations:** Implement clean, hand-rolled algorithms for matrix addition, subtraction, scalar operations, and transposition.
- **The Core Primitives:** Matrix-matrix multiplication ($C = A \times B$) and matrix-vector dot products. Ensure strict dimension verification guards are active.
- **Activation Functions:** Implement element-wise activations directly on the matrix blocks, providing native operations for Sigmoid, Softmax, and ReLU functions.

**Stretch Ideas:**
- **Cache-Conscious Tiling:** Refactor the baseline matrix multiplication loops into a blocked/tiled architecture. Match tile dimensions to local CPU L1/L2 cache boundaries to maximize line reuse and minimize execution misses.
- **Memory Aliasing Minimization:** Audit function signatures to introduce the `restrict` keyword and `const` guarantees, removing compiler pointer-aliasing barriers to unlock auto-vectorisation ($SIMD$).
- **The Execution Benchmark Suite:** Build a precision profiling harness using `clock_gettime` or high-resolution cycle counters. Calculate $GFLOPS$ and memory throughput metrics to compare the naive loop implementations directly against the cache-tiled variations.
- **Automated Verification Harness:** Integrate a custom testing module that performs relative error evaluations (e.g., $|A - B| < \epsilon$) rather than direct floating-point identity checks (`==`) to safely verify correctness across varied compiler optimisation tiers.

---

## Phase 5 - Neural Network from Scratch
**Goal:** Build a fully training neural network in C, using nothing but matlib and the standard library.

### Concepts
- Computational graph mental model (don't need to implement one - but understand it)
- Forward pass: layer types (dense/linear), activation functions (ReLU, sigmoid, softmax)
- Loss functions: MSE, cross-entropy
- Backpropagation: chain rule, gradient accumulation
- Parameter update rules: SGD, then optionally Adam
- Mini-batch training loop
- Saving/loading weights to binary files
- Numerical gradient checking (finite differences) - for verifying backprop

### Resources
- Andrej Karpathy's *micrograd* (Python) - read the source before building in C, it's the clearest backprop reference available
- *Neural Networks and Deep Learning* (Nielsen, free online) - for the maths

### Project - `cnet`: A neural network library + trained model
Build a library supporting arbitrarily deep dense networks. Train it on MNIST (handwritten digits - simple binary format, easy to load in C). Target: >97% test accuracy.

**Why:** MNIST is the right target - hard enough to need real backprop, simple enough that debugging is tractable. The binary file format means writing a data loader from scratch, reinforcing file I/O skills.

**Stretch:** Add a conv layer. Save weights and write a small inference-only CLI: `./cnet predict image.bin`.

---

## Standalone - A Linear Programming Engine
**Goal:** Build a language and solver engine for linear programming mathematics in C.

### Concepts
- Lexing and parsing a domain-specific language for expressing linear programs
- The simplex method: tableau representation, pivoting, basis selection, standard form
- Slack variables, dual variables, shadow prices, reduced costs
- Detecting infeasible and unbounded problems
- Numerical stability considerations in iterative matrix algorithms

### Resources
- *Introduction to Linear Programming* (Bertsimas & Tsitsiklis): chapters 1–3 for the theory
- Understand the simplex algorithm by hand before implementing it
- `matlib` from [Phase 4](#phase-4---low-level-numerics-and-performance) as the computational backend

### Project - `clp`: A Linear Programming Engine
Build a tool that lets you express a linear program in a natural language format and solves it, reporting a full solution: primal variables, objective value, slacks, duals, and reduced costs.

**Why:** Combines language front-end work with numerical computing. Linear programming appears throughout operations research, economics, logistics, and machine learning. Open-ended enough to keep iterating on and potentially useful to others.

**Stretch:** Two-phase simplex for problems without an obvious initial feasible point. Minimisation and maximisation. Sensitivity analysis. A library interface so the solver can be embedded in other projects.

**Placement:** After [Phase 5](#phase-5---neural-network-from-scratch) (`matlib` primitives available), before [Phase 6](#phase-6---interpreter--compiler) (warms up lexer and parser thinking without the full complexity of a general language).

---

## Phase 6 - Interpreter / Compiler
**Goal:** Build a working interpreted language in C.

### Concepts
- Lexing: token types, scanning, handling whitespace/comments
- Parsing: recursive descent, operator precedence, AST node types
- Tree-walking interpreter: environments, scopes, closures
- Memory management for AST nodes and runtime objects
- Error reporting with line numbers
- Optionally: bytecode compilation + a stack-based VM

### Resources
- *Crafting Interpreters* (Nystrom, free online) - the definitive resource. Follow the C half (Part III), not the Java half.
- Do not skip the challenges at the end of each chapter.

### Project - `clox` (or own variant)
A complete interpreted language with: variables, control flow, functions, closures, classes, and a mark-sweep garbage collector.

**Why:** Crafting Interpreters guides directly to this. By Phase 6 the C fluency is there to go off-piste - modify the language, add features, make it your own.

**Stretch:** Compile to bytecode with an optimising pass; add a foreign function interface so C code can be called from the language.

---

## Phase 7 - Science Simulator (Open-ended)
**Goal:** Apply everything. Pick a simulation domain and build it properly.

### Candidate directions
- **N-body gravitational simulation** - particles, Euler/Verlet integration, visualised via SDL2
- **Cellular automaton** - Conway's Life is trivial; reaction-diffusion (Gray-Scott) is genuinely interesting
- **Fluid simulation** - Navier-Stokes on a grid (Eulerian), Jos Stam's stable fluids paper is the reference
- **Neural-physics hybrid** - train `cnet` to predict trajectories of a physical system

### Graphics - SDL2 basics
Before building the simulator, spend a short sprint on SDL2:
- Setup: linking SDL2, creating a window and renderer
- The event loop: handling quit, keyboard, mouse events
- Drawing primitives: pixels, rectangles, lines
- Updating the display each frame (`SDL_RenderPresent`)
- Mapping simulation state (e.g. particle positions, grid values) to pixel coordinates

SDL2 is well-documented and the basics are achievable in a day or two - it doesn't warrant a full phase.

### Concepts (as needed by chosen direction)
- Numerical integration methods (Euler, RK4)
- Spatial data structures (grid, quadtree) for performance
- Parallelism: `pthreads` or `OpenMP` if performance demands it

---

## Running themes throughout
- Always compile with `-Wall -Wextra -pedantic`. Fix every warning.
- During development, always build with `-fsanitize=address,undefined`. Strip for release builds.
- `valgrind --leak-check=full` on every project before calling it done.
- Write a `Makefile` for every project from Phase 1 onwards.
- Read other people's C - the [Redis](https://github.com/redis/redis) source (`src/`) and [SQLite](https://github.com/sqlite/sqlite) amalgamation are both excellent references.
