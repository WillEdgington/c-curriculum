# c-curriculum

A self-directed curriculum for learning C, structured in phases. Each phase covers the concepts needed to tackle a project slightly beyond my current ability, forcing learning as I go. Concepts are introduced before the project that needs them, but kept brief - the project is where understanding solidifies.

> Planned collaboratively with AI assistance and updated as I progress through each phase.

---

## Phase 1 - Foundations
**Goal:** Get comfortable enough with C syntax, memory, and tooling to build something small but real.

### Concepts
- Compilation model: preprocessor → compiler → linker, `gcc`/`clang`, `Makefile` basics
- Primitive types, operators, control flow
- Functions, scope, stack frames (mental model, not assembly)
- Arrays and pointer arithmetic - the core of Phase 1
- Strings as `char` arrays, `<string.h>`
- Structs and basic typedef patterns
- Manual memory: `malloc`, `free`, `calloc`, heap vs stack
- File I/O: `fopen`, `fread`, `fwrite`, `fclose`
- `<stdio.h>`, `<stdlib.h>`, `<string.h>` - the everyday headers
- Basic debugging: `gdb`, `valgrind` for memory leaks
- Sanitizer flags: `-fsanitize=address` (ASan), `-fsanitize=undefined` (UBSan) - run these during development on every project

### Resources
- [*C Programming Full Course for free ⚙️*](https://youtu.be/xND0t1pr3KY?si=HhqefSVryw13gdQV) on YouTube (for setup and basics)
- *The C Programming Language* (K&R) - read chapters 1–6 alongside the project, not before
- `cs50x` first 2–3 weeks for environment setup if needed
- `valgrind` and `gdb` tutorials (short, practical ones)

### Project - `cjson`: A minimal JSON parser
Parse a subset of JSON (strings, numbers, booleans, null, nested objects, arrays) from a file into a C struct tree, then pretty-print it back.

**Why:** Forces handling of strings, structs, recursive data structures, and manual memory - all in Phase 1 territory. A real problem with a clear correctness signal.

**Stretch:** Add a simple query interface - e.g. `get(root, "user.name")` returns the value.

---

## Phase 2 - Memory, Data Structures, and the C Idiom
**Goal:** Own memory explicitly. Build the data structures I've taken for granted in Python/JS.

### Concepts
- Pointers to pointers, pointer-to-struct patterns
- Dynamic arrays (realloc growth strategy)
- Linked lists, doubly linked lists
- Hash maps - open addressing or chaining
- Function pointers - syntax and use cases
- Header files, multi-file projects, `#include` guards
- `#define` macros vs `static inline` functions
- Error handling patterns in C (return codes, errno)
- Bit manipulation basics

### Resources
- K&R chapters 6–8
- *Hacking: The Art of Exploitation* ch. 1–2 (optional, great for memory intuition)

### Project - `chttp`: A minimal HTTP/1.1 server
Accept TCP connections, parse raw HTTP GET requests, serve static files from a directory, return correct status codes (200, 404, 405).

**Why:** Requires sockets (`<sys/socket.h>`), string parsing, dynamic buffers, and multi-file project structure. Satisfying - you can curl it or open it in a browser.

**Stretch:** Handle multiple concurrent connections with `fork()` or `select()`.

---

## Phase 3 - Systems Thinking
**Goal:** Understand how C sits on top of the OS. Build something that interacts directly with the system.

### Concepts
- Process model: `fork`, `exec`, `wait`, `pipe`
- File descriptors, `dup2`, redirection
- Signals: `SIGINT`, `SIGCHLD`, `signal()`
- Environment variables
- `mmap` and memory-mapped files (intro level)
- Makefiles - proper multi-target builds, dependency tracking
- Address space layout: text, data, BSS, heap, stack

### Resources
- *Computer Systems: A Programmer's Perspective* (CS:APP) - chapters 8 (exceptional control flow) and 10 (system-level I/O)
- Linux `man` pages

### Project - `csh`: A Unix shell
Support command execution, `|` pipes, `<`/`>` redirection, background jobs (`&`), built-ins (`cd`, `exit`, `export`).

**Why:** Directly exercises the process model, file descriptors, and signal handling. Classic systems project for good reason.

**Stretch:** Job control (`fg`, `bg`, `jobs`), history with arrow keys via `termios`.

---

## Phase 4 - Low-Level Numerics and Performance
**Goal:** Learn how C handles numbers at the hardware level - essential groundwork for the neural network.

### Concepts
- IEEE 754 floating point - representation, precision, NaN, inf
- `float` vs `double` - when each matters
- SIMD intuition (won't write intrinsics yet, but understand what the compiler can do)
- Cache locality - row-major vs column-major access patterns, why it matters for matrix ops
- Profiling: `gprof`, `perf`, or just `clock()` - profile before optimising, always
- Compiler optimisation flags: `-O0` vs `-O2` vs `-O3`, and what the compiler is actually doing
- `static`, `inline`, and `const` as optimisation hints
- `restrict` keyword, `const` correctness
- Fixed-size integer types: `int32_t`, `uint8_t` etc. (`<stdint.h>`)
- Basic linear algebra in C: matrix multiply, dot product, transpose - written by hand

### Resources
- *What Every Computer Scientist Should Know About Floating-Point Arithmetic* (Goldberg) - skim, don't memorise
- CS:APP chapter 2 (data representation)

### Project - `matlib`: A matrix operations library
Implement a small matrix library: creation, addition, elementwise multiply, matrix multiply, transpose, scalar ops. Backed by flat `float` arrays. Include a basic benchmark comparing naive vs cache-friendly implementations.

**Why:** The direct precursor to the neural network. By the end I'll have the exact primitives needed, and understand *why* they're written the way they are.

**Stretch:** Add basic BLAS-style naming conventions; implement a simple softmax and sigmoid over a matrix.

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

### Project - `clox` (or my own variant)
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
- Read other people's C - the Redis source (`src/`) and SQLite amalgamation are both excellent references.
