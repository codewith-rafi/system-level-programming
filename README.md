# 💻 System Level Programming

A comprehensive study guide covering fundamental computer systems for Semester exam

---

## 📝 Past Exam Questions & Answers

### Short Answer Questions (5 points each)

#### Q1: Convert floating points to IEEE 754 binary format

**Problem 1: Convert 20.5 to IEEE 754**

**Steps:**
1. Convert to binary: 20.5 = 10100.1₂
2. Normalize: 1.01001 × 2⁴
3. Sign bit: 0 (positive)
4. Exponent: 4 + 127 = 131 = 10000011₂
5. Mantissa: 01001000000000000000000 (23 bits)

**Answer:**
```
Sign | Exponent  | Mantissa
0    | 10000011  | 01001000000000000000000
```
**Hex**: 0x41A40000

---

**Problem 2: Convert -20.5 to IEEE 754**

**Steps:**
1. Same as 20.5, but sign bit = 1 (negative)
2. Exponent: 10000011₂ (same)
3. Mantissa: 01001000000000000000000 (same)

**Answer:**
```
Sign | Exponent  | Mantissa
1    | 10000011  | 01001000000000000000000
```
**Hex**: 0xC1A40000

---

#### Q2: Describe generational garbage collection

**Answer:**

Generational GC is based on the observation that most objects die young. It divides the heap into generations:

- **Young generation (G0)**: Newly created objects
- **Old generation (G1, G2...)**: Long-lived objects

**Process:**
1. Objects initially allocated in young generation
2. Young generation collected frequently (minor GC) - fast
3. Surviving objects promoted to old generation
4. Old generation collected rarely (major GC) - slower

**Advantages:**
- Most garbage is in young generation, so minor GC is fast
- Focuses collection effort where most garbage exists
- Improves overall performance

**Disadvantages:**
- More complex implementation
- Requires write barriers to track references between generations

---

#### Q3: Calculate speedup using Amdahl's Law

**Problem:** New CPU is 20× faster on search queries. Old CPU spends 90% time on search. What is the speedup?

**Solution using Amdahl's Law:**

```
Speedup = 1 / ((1 - P) + P/S)

Where:
P = 0.9 (90% of time spent on search)
S = 20 (20× faster)

Speedup = 1 / ((1 - 0.9) + 0.9/20)
        = 1 / (0.1 + 0.045)
        = 1 / 0.145
        = 6.9

Answer: Speedup ≈ 6.9×
```

**Explanation:** Even though the CPU is 20× faster on searches, the overall speedup is limited to 6.9× because 10% of the time is still spent on other tasks that aren't improved.

---

#### Q4: What is CSAPP and what did you learn?

**Answer:**

**CSAPP** stands for **"Computer Systems: A Programmer's Perspective"**

**Key topics learned this semester:**

1. **Program Execution**: How C programs are compiled, assembled, linked, and executed
2. **Data Representation**: Binary, two's complement, IEEE 754 floating-point
3. **Assembly Language**: Understanding low-level instructions and register usage
4. **Memory Management**: Stack, heap, pointers, dynamic allocation
5. **Buffer Overflow**: Security vulnerabilities and prevention techniques
6. **Performance Optimization**: Cache awareness, loop optimization, profiling
7. **Linking**: Static and dynamic linking, symbol resolution, relocation
8. **Exceptions**: Hardware/software exception handling mechanisms

**Main lesson:** Understanding how computer systems work at low levels makes you a better programmer who can write more efficient, secure, and reliable code.

---

### Comprehension Questions (10 points each)

#### Q1: Datalab-style Puzzles

**Problem 1: minusOne - return -1 using only bitwise operations**

```c
int minusOne(void) {
    return ~0;
}
```

**Explanation:** 
- `~0` inverts all bits of 0 (which is 0x00000000)
- Result is 0xFFFFFFFF, which is -1 in two's complement

---

**Problem 2: isPower2 - check if x is a power of 2**

```c
int isPower2(int x) {
    return (!!x) & (!((x) & (x + (~0))));
}
```

**Alternative clearer solution:**
```c
int isPower2(int x) {
    return (x > 0) & (!(x & (x + (~1 + 1))));
}
```

**Explanation:**
- Power of 2 has exactly one bit set (e.g., 8 = 1000₂)
- `x - 1` flips all bits after that single 1 bit
- `x & (x-1)` is 0 only if x is power of 2
- `!!x` ensures x is not 0
- Check x is positive (no negative powers of 2)

**Examples:**
- `isPower2(8)`: 8 = 1000₂, 7 = 0111₂, 8 & 7 = 0 ✓
- `isPower2(5)`: 5 = 0101₂, 4 = 0100₂, 5 & 4 = 4 ≠ 0 ✗

---

#### Q2: Stack Frame Structure

**Problem 1: Draw binary tree from memory**

Given memory dump starting at root 0x804961c:

**Answer structure:**
```
Root: 0x804961c (key=0x???)
├─ Left:  0x??????? (key=0x???)
│  ├─ Left:  ...
│  └─ Right: ...
└─ Right: 0x??????? (key=0x???)
   ├─ Left:  ...
   └─ Right: ...
```

**How to read from memory:**
- Each node has: key (4 bytes) + left pointer (4 bytes) + right pointer (4 bytes)
- At address 0x804961c: read 12 bytes total
- First 4 bytes = key value
- Next 4 bytes = left child address
- Last 4 bytes = right child address
- Follow pointers recursively

---

**Problem 2: Stack frame for search(root, 0x4e)**

**search() function:**
```c
tree_node* search(tree_node* node, int key) {
    if (node == NULL) return NULL;          // Line 1
    if (node->key == key) return node;      // Line 2
    tree_node* p = search(node->left, key); // Line 3
    if (p != NULL) return p;                // Line 4
    return search(node->right, key);        // Line 5
}
```

**Stack at line 5 (sample answer):**

```
Higher Address
+------------------+ 0xbffff880
|   old ebp        |
+------------------+ 0xbffff87c
|   rtn addr       |
+------------------+ 0xbffff878
|   0x4e (key)     |
+------------------+ 0xbffff874
|   0x804961c      | (root address)
+------------------+ 0xbffff870
|   old ebp        |
+------------------+ 0xbffff86c
|   rtn addr       |
+------------------+
Lower Address
```

**Explanation:**
- Multiple frames if recursion occurred
- Each frame contains: arguments (node, key), return address, old ebp
- Stack grows downward (higher addresses at top)

---

#### Q3: Function Optimization - Side Effects

**Problem:** Do func1(x) and func2(x) always behave the same?

```c
int func1(x) { return f(x) + f(x) + f(x) + f(x); }
int func2(x) { return 4*f(x); }
```

**Answer:**

**They have SAME behavior when:**
- f(x) has **no side effects** (pure function)
- Example:
```c
int f(int x) { return x * x; }
```
Both func1(5) and func2(5) return 100.

**They have DIFFERENT behavior when:**
- f(x) has **side effects** (modifies global state, I/O, etc.)
- Example:
```c
int counter = 0;
int f(int x) {
    counter++;
    return x;
}
```

Result:
- `func1(5)`: calls f() 4 times, counter = 4, returns 20
- `func2(5)`: calls f() 1 time, counter = 1, returns 20

**Impact on compiler optimization:**

Compilers **cannot optimize** func1 to func2 if f() might have side effects because:
1. **Procedure side effects** are optimization blockers
2. Compiler must assume f() could modify global variables, perform I/O, or have other observable effects
3. Changing number of calls to f() would change program behavior

**Conclusion:** This is why compilers are conservative and don't perform aggressive optimizations on function calls unless they can prove the function is pure.

---

#### Q4: Windows Programming - Event Handler

**Code analysis:**

```c
case WM_LBUTTONDOWN:
    xPos = GET_X_LPARAM(lParam);
    yPos = GET_Y_LPARAM(lParam);
    GetLocalTime(&systime);
    sprintf(time,"%4d-%2d-%2d %2d:%2d", systime.wYear, systime.wMonth, 
            systime.wDay, systime.wHour, systime.wMinute, systime.wSecond);
    hdc = GetDC(hwnd);
    TextOut(hdc, xPos, yPos, time, strlen(time));
    ReleaseDC(hwnd, hdc);
    return 0;
```

**Question 1: What is the function of the code?**

**Answer:**
This code displays the current system time as text at the location where the user clicks the left mouse button in the window.

---

**Question 2: Annotated code**

```c
case WM_LBUTTONDOWN:  // Handle left mouse button click event
    
    // Get x-coordinate of mouse click from lParam
    xPos = GET_X_LPARAM(lParam);
    
    // Get y-coordinate of mouse click from lParam
    yPos = GET_Y_LPARAM(lParam);
    
    // Get current system time and store in systime structure
    GetLocalTime(&systime);
    
    // Format time as string: "YYYY-MM-DD HH:MM:SS"
    sprintf(time,"%4d-%2d-%2d %2d:%2d", systime.wYear, systime.wMonth, 
            systime.wDay, systime.wHour, systime.wMinute, systime.wSecond);
    
    // Get device context (DC) for drawing in the window
    hdc = GetDC(hwnd);
    
    // Draw the time string at the clicked position (xPos, yPos)
    TextOut(hdc, xPos, yPos, time, strlen(time));
    
    // Release the device context (free resources)
    ReleaseDC(hwnd, hdc);
    
    // Return 0 to indicate message was processed
    return 0;
```

**Key Windows API functions explained:**
- `WM_LBUTTONDOWN`: Windows message sent when left mouse button pressed
- `GET_X_LPARAM/GET_Y_LPARAM`: Macros to extract coordinates from lParam
- `GetLocalTime()`: Retrieves current local date and time
- `GetDC()`: Gets device context for drawing
- `TextOut()`: Outputs text string to window
- `ReleaseDC()`: Releases device context handle

---

## 📑 Table of Contents

- [Week 01 - C Program Execution](#week-01---c-program-execution)
- [Week 02 - Bits & Data Representation](#week-02---bits--data-representation)
- [Week 03 - Assembly Instructions](#week-03---assembly-instructions)
- [Week 04 - Pointers, Structs, Arrays](#week-04---pointers-structs-arrays)
- [Week 05 - Function Call & Stack Frame](#week-05---function-call--stack-frame)
- [Week 06 - Buffer Overflow Attack](#week-06---buffer-overflow-attack)
- [Week 07 - Memory Allocation](#week-07---memory-allocation)
- [Week 08 - Memory Bugs & Heap Management](#week-08---memory-bugs--heap-management)
- [Week 09 - Garbage Collection](#week-09---garbage-collection)
- [Week 10 - Performance Measurement](#week-10---performance-measurement)
- [Week 11 - Optimizing Program Performance](#week-11---optimizing-program-performance)
- [Week 12 - Memory Operation and Performance](#week-12---memory-operation-and-performance)
- [Week 13 - Cache and Cache-Aware Programming](#week-13---cache-and-cache-aware-programming)
- [Week 14 - Linking](#week-14---linking)
- [Week 15 - Exception and Windows Programming](#week-15---exception-and-windows-programming)

---

## Week 01 - C Program Execution

### Six Phases of C Program Execution
```
Edit → Preprocess → Compile → Assemble → Link → Load → Execute
```

### Four Hardware Components
- **CPU** - Central Processing Unit
- **Main Memory** - RAM
- **I/O Devices** - Input/Output devices
- **System Bus** - Communication pathway

### Virtual Memory Space
An abstraction giving each process the illusion of having its own large, contiguous memory space, even though physical memory is limited and shared among processes.

---

## Week 02 - Bits & Data Representation

### Bit Operations
| Operator | Operation | Use Case |
|----------|-----------|----------|
| `&` | AND | Masking bits |
| `\|` | OR | Setting bits |
| `^` | XOR | Toggling bits |
| `~` | NOT | Inverting bits |
| `<<` | Left shift | Fast multiplication by 2^n |
| `>>` | Right shift | Fast division by 2^n |

### Integer Encoding
- **Signed integers**: Two's complement representation
- **Floating-point**: IEEE 754 format (sign, exponent, mantissa)

### Byte Order (Endianness)
- **Little-endian**: LSB at lowest address (x86, Windows, Linux)
- **Big-endian**: MSB at lowest address (network order, some older systems)

---

## Week 03 - Assembly Instructions

### Common x86 Instructions

| Instruction | Description |
|-------------|-------------|
| `mov` | Move data between registers/memory |
| `add/sub` | Arithmetic operations |
| `cmp` | Compare two values |
| `jmp/je/jne` | Unconditional/conditional jumps |
| `call` | Call function |
| `ret` | Return from function |
| `push/pop` | Stack operations |

---

## Week 04 - Pointers, Structs, Arrays

### Pointers
- Variable that stores a memory address
- `*` dereferences (gets value at address)
- `&` gets address of variable

### Struct Alignment
- Compiler adds padding to align data to word boundaries for performance
- `#pragma pack(1)` removes padding

### Arrays
- Stored in contiguous memory
- Accessed via pointer arithmetic: `arr[i] == *(arr + i)`

---

## Week 05 - Function Call & Stack Frame ⭐⭐

### Function Call Process

**Caller responsibilities:**
1. Push parameters (right-to-left)
2. `call` instruction pushes return address

**Callee responsibilities:**
1. Save old `ebp`
2. Set `ebp = esp` (establish new frame)
3. Allocate space for local variables
4. Execute function body
5. Restore `esp` and `ebp`
6. `ret` pops return address

### Stack Frame Layout (High → Low Address)

```
+------------------+
| Parameters       |
+------------------+
| Return Address   |
+------------------+
| Old EBP          | ← EBP (Base Pointer)
+------------------+
| Local Variables  | ← ESP (Stack Pointer)
+------------------+
```

### Key Registers
- **ESP**: Stack pointer (top of stack)
- **EBP**: Base pointer (start of current frame)
- **EIP**: Instruction pointer (next instruction to execute)

---

## Week 06 - Buffer Overflow Attack

### Attack Mechanism
1. Input data exceeds buffer size
2. Overwrites: Local variables → Old EBP → Return Address
3. Replace return address with address of malicious code (shellcode)

### Defense Mechanisms

| Defense | Description |
|---------|-------------|
| Safe functions | Use `strncpy` instead of `strcpy`, `fgets` with size limit |
| **Stack canaries** | Special value before return address; crash if modified |
| **DEP** | Data Execution Protection - mark stack as non-executable |
| **ASLR** | Address Space Layout Randomization - randomize stack location |

---

## Week 07 - Memory Allocation

### Static vs Dynamic Allocation
- **Static**: Compile-time (globals, `static` locals)
- **Dynamic**: Runtime (stack, heap)

### Stack vs Heap

| Feature | Stack | Heap |
|---------|-------|------|
| Speed | Fast | Slower |
| Management | Automatic (LIFO) | Manual (`malloc`/`free`) |
| Size | Limited | Larger |
| Access Pattern | LIFO | Random |

### Heap Data Structures
- **Free list**: Linked list of free blocks
- **Allocation tree**: Balanced tree (e.g., Red-Black) for quick search

### Fragmentation
- **Internal**: Wasted space inside allocated block
- **External**: Free memory split into small, unusable pieces

---

## Week 08 - Memory Bugs & Heap Management

### Review of Pointers in C

```c
int *p = malloc(sizeof(int));  // Allocate
*p = 10;                        // Use
free(p);                        // Free
p = NULL;                       // Nullify (good practice)
```

### Common Memory Bugs

#### 1. Bad References

**Forgetting `&` in scanf:**
```c
scanf("%d", &i);  // ✓ Correct
scanf("%d", i);   // ✗ Wrong - passes value, not address
```

**Uninitialized pointer:**
```c
int *p;      // Uninitialized
*p = 5;      // ✗ Crash - p points to random memory
```

**Returning pointer to local stack memory:**
```c
int* func() {
    int x = 5;
    return &x;  // ✗ Wrong - x destroyed after return
}
```

**Double pointer solution for allocation:**
```c
void alloc(int **p) {
    *p = malloc(100);
}
```

#### 2. Overwriting Memory

**Off-by-one error:**
```c
int a[10];
for(i=0; i<=10; i++)  // ✗ Should be i<10
    a[i] = 0;
```

**Wrong malloc size:**
```c
int *a = malloc(100);              // ✗ 100 bytes, not 100 ints
int *a = malloc(100 * sizeof(int)); // ✓ Correct
```

**Buffer overflow:**
```c
char s[8];
gets(s);  // ✗ Dangerous - no bounds checking
```

**Missing null terminator:**
```c
char *s = malloc(strlen(src));      // ✗ Missing +1 for '\0'
char *s = malloc(strlen(src) + 1);  // ✓ Correct
```

**Operator precedence:**
```c
(*a)--;  // Decrements value
*a--;    // Decrements pointer
```

#### 3. Double Free

```c
free(p);
free(p);  // ✗ Error - freeing same memory twice
```

**Using freed memory:**
```c
free(p);
p[0] = 1;  // ✗ Error - accessing freed memory
```

#### 4. Memory Leaks

**Simple leak:**
```c
void func() {
    int *p = malloc(100);
    return;  // ✗ Leak - p not freed
}
```

**Partial free in struct:**
```c
struct Person {
    char *name, *addr;
};

Person *p = malloc(sizeof(Person));
p->name = malloc(100);
p->addr = malloc(100);
free(p);  // ✗ Leaks name and addr

// ✓ Correct way:
free(p->name);
free(p->addr);
free(p);
```

### Debugging Memory Bugs

#### Visual Studio Debug Heap Values
- **0xCD** - Clean memory (allocated but not written)
- **0xDD** - Dead memory (freed)
- **0xFD** - Fence/no-man's land (bounds checking)

#### `_CrtMemBlockHeader` Structure
Stores:
- Pointers to next/prev blocks
- Filename/line of allocation
- Block size, type, request number
- Guard bytes (0xFD)

#### `_crtDbgFlag` Control
- `_CRTDBG_LEAK_CHECK_DF` - report leaks at exit
- `_CRTDBG_DELAY_FREE_MEM_DF` - keep freed blocks for dangling pointer detection

#### Macro-based Debugging Wrapper
```c
#define malloc(s) my_malloc(s, __FILE__, __LINE__)
#define free(p) my_free(p, __FILE__, __LINE__)
```
Adds header/footer with checksums, guard bytes, file/line info.

---

## Week 09 - Garbage Collection

### Memory Management Types
- **EMM** (Explicit Memory Management): Programmer calls `malloc`/`free`
- **AMM** (Automatic Memory Management): GC automatically reclaims unused memory

### Heap Tracking Structures
- Bitmap
- Linked list (implicit/explicit)
- Segregated free lists
- Balanced tree (e.g., Red-Black tree)

### Classical GC Algorithms

#### 1. Mark and Sweep

**Process:**
1. **Mark**: Start from roots (registers, stack, globals), mark all reachable blocks
2. **Sweep**: Scan heap, free unmarked blocks

**Pseudo-code:**
```c
GC() {
    roots = getRoots();
    for each r in roots: mark(r);
    sweep();
}

mark(p) {
    if !isPtr(p) return;
    if isMarked(p) return;
    setMark(p);
    for each child in p: mark(child);
}

sweep() {
    for each block in heap:
        if isMarked(block): clearMark();
        else: free(block);
}
```

**Advantages**: Can run only when heap is full  
**Disadvantages**: Fragmentation, stop-the-world pauses

#### 2. Copying Collection (Cheney's Algorithm)

**Process:**
- Two spaces: from-space (active), to-space (inactive)
- Copy live objects from from-space to to-space
- Switch roles after GC

**Advantages**: No fragmentation, fast sweep  
**Disadvantages**: Uses only half of heap

#### 3. Reference Counting

**Process:**
- Each object has counter of references
- When counter → 0, object freed immediately

**Pseudo-code:**
```c
// a = b
increment_ref_count(b);
decrement_ref_count(a);
if ref_count(a) == 0: free(a);
a = b;
```

**Advantages**: Immediate reclamation  
**Disadvantages**: Cannot reclaim cycles, overhead on every assignment

#### 4. Generational GC

**Concept:**
- Based on observation: Most objects die young
- Generations: Young (G0), Old (G1, ...)
- Young gen collected frequently (minor GC)
- Old gen collected rarely (major GC)

**Advantages**: Faster, focuses effort where most garbage is  
**Disadvantages**: Complexity, requires write barriers

### GC Implementation Notes
- **Conservative GC for C**: Treats any word resembling a pointer as a pointer
- **Splay trees**: Used for fast allocation tracking
- **Root enumeration**: Collect pointers from stack, registers, globals

---

## Week 10 - Performance Measurement

### Amdahl's Law

**Formula:**
```
Speedup = 1 / ((1 - P) + P/S)
```
- **P** = Fraction of program improved
- **S** = Speedup of that fraction

**Key insight**: Overall speedup limited by parts not improved. Even with huge S, small P limits total speedup.

### 80/20 Rule
80% of CPU time spent in 20% of code. **Optimize that 20%** for biggest gains.

### Timing Mechanisms

| Method | Type | Description |
|--------|------|-------------|
| Wall clock time | Real time | Total time from start to finish |
| CPU time | Process time | User + System time for your program |

**Tools:**
- **Hardware**: TSC (Time Stamp Counter), `rdtsc` instruction
- **OS**: `gettimeofday()` (Linux), `QueryPerformanceCounter` (Windows)
- **C**: `clock()` from `<time.h>`

### Profiling
Used to find **hotspots** (bottlenecks) in code.

**Tools**: VC Profiler, `gprof`

Tells you which functions are called most and take most time.

---

## Week 11 - Optimizing Program Performance

### Golden Rules of Optimization
1. **Good algorithms matter most** - O(n log n) vs O(n²) makes huge difference
2. Profile before optimizing
3. Optimize the bottlenecks

### Optimization Blockers

**Memory aliasing:**
- Two pointers may point to same location
- Compiler can't optimize safely

**Procedure side-effects:**
- Function may modify global state
- Compiler can't remove/reorder calls

### General Optimization Techniques

#### Code Motion
Move loop-invariant code outside loop.

```c
// Before
for (i = 0; i < n; i++)
    a[i] = strlen(s) * 2;

// After
int len = strlen(s) * 2;
for (i = 0; i < n; i++)
    a[i] = len;
```

#### Reduce Procedure Calls
Replace function calls with direct access.

#### Eliminate Unnecessary Memory References
Use local variables (registers) instead of memory.

#### Loop Unrolling
Do more work per iteration to reduce loop overhead.

```c
// Before
for (i = 0; i < n; i++)
    sum += a[i];

// After (unrolled 2x)
for (i = 0; i < n; i += 2)
    sum += a[i] + a[i+1];
```

### Quick Optimization Tips
- Use `register` keyword for frequently used variables
- Prefer `++i` over `i++` (no temporary copy)
- Pass by reference-to-const for large objects
- Avoid `malloc`/`printf` in tight loops (slow)
- Use `switch` instead of long `if-else` chains
- Early loop break when condition met

### CPE (Cycles Per Element)
Metric for loop efficiency. **Lower CPE = faster**.

---

## Week 12 - Memory Operation and Performance

### Memory Hierarchy

**Why?** Fast memory is small/expensive, slow memory is large/cheap.

```
Registers → L1 Cache → L2 Cache → L3 Cache → RAM → Disk
(fastest/smallest)                          (slowest/largest)
```

Works due to **locality** principle.

### Locality Principles

**Temporal Locality:**
- Recently accessed data likely to be accessed again
- Example: Loop variables

**Spatial Locality:**
- Data near recently accessed data likely accessed soon
- Example: Array elements

**Good locality → fewer cache misses → faster execution**

### Cache Concepts

**Cache hit**: Data found in cache (fast)  
**Cache miss**: Data not in cache, fetch from lower level (slow)

#### Types of Cache Misses

| Type | Description | Prevention |
|------|-------------|------------|
| **Cold/Compulsory** | First access to block | Unavoidable |
| **Capacity** | Cache too small for working set | Use smaller working set |
| **Conflict** | Multiple blocks map to same line | Better layout/padding |

### Cloud Storage
Data stored on remote servers accessed via internet.

**Advantages:**
- Scalable
- Accessible anywhere
- Disaster recovery

**Disadvantages:**
- Security risks
- Requires internet
- Potential latency

---

## Week 13 - Cache and Cache-Aware Programming

### Cache Organization

**Three types:**
- **Direct-mapped**: Each block has exactly one place
- **Set-associative**: Block can be in small set of lines (2-way, 4-way, etc.)
- **Fully associative**: Block can go anywhere

### Address Breakdown
```
[ Tag | Set Index | Block Offset ]
```

### Cache Miss Calculation Example

**Given:**
- Struct size = 4 ints = 16 bytes
- Cache line = 32 bytes → 2 structs per line
- Array `square[16][16]` → 256 elements
- Each element has 4 writes → total writes = 256 × 4 = 1024

**Calculation:**
- Number of lines = 256 / 2 = 128 lines
- **Misses**: First access to each line = **128 misses**
- **Miss rate** = 128 / 1024 = **0.125 (12.5%)**

### Cache-Aware Programming Techniques

#### Loop Fission
Split large loops into smaller ones to fit in instruction cache.

#### Loop Fusion
Combine separate loops over same data for better temporal locality.

#### Row-Major Order
In C, arrays stored row-wise. **Traverse rows first** for spatial locality.

```c
// Good - row-major
for (i = 0; i < N; i++)
    for (j = 0; j < N; j++)
        sum += a[i][j];

// Bad - column-major
for (j = 0; j < N; j++)
    for (i = 0; i < N; i++)
        sum += a[i][j];
```

#### Loop Tiling (Blocking)
Process data in small blocks that fit in cache.

```c
// Tiled matrix multiplication
for (i = 0; i < N; i += B)
    for (j = 0; j < N; j += B)
        for (k = 0; k < N; k += B)
            // Process B×B block
            for (ii = i; ii < i+B; ii++)
                for (jj = j; jj < j+B; jj++)
                    for (kk = k; kk < k+B; kk++)
                        C[ii][jj] += A[ii][kk] * B[kk][jj];
```

#### Avoid Cache Collisions
Pad arrays to prevent multiple arrays mapping to same cache set.

### Thrashing
Occurs when multiple data blocks map to same cache set causing constant eviction.

**Solution**: Adjust memory layout or add padding.

---

## Week 14 - Linking

### Static Linking Process

**Input**: Relocatable object files (.obj/.o)  
**Output**: Executable file (.exe/a.out)

**Three Steps:**
1. **Symbol Resolution**: Match each symbol reference to definition
2. **Combination**: Merge sections (.text, .data, .bss) from all files
3. **Relocation**: Adjust addresses of symbols to final memory locations

### Symbol Resolution Rules

**Strong symbol**: Functions and initialized globals  
**Weak symbol**: Uninitialized globals

**Rules:**
1. Strong symbol can appear only once
2. Weak symbol overridden by strong symbol
3. Multiple weak symbols → linker picks arbitrarily

### Object File Sections

| Section | Contents |
|---------|----------|
| **.text** | Code (read-only) |
| **.data** | Initialized global/static variables |
| **.bss** | Uninitialized global/static (space only, no file data) |
| **.symtab** | Symbol table |
| **.rel.text/.rel.data** | Relocation info for code/data |

### Relocation

After merging, addresses change → must fix references in code/data.

**Relocation entry contains:**
- Offset in section
- Symbol reference
- Relocation type (e.g., `REL32` for PC-relative jump)

**Example**: Call to `swap()` has relocation entry pointing to `swap` symbol.

### Static Libraries

Collection of `.obj` files archived into:
- `.a` (Linux)
- `.lib` (Windows)

Linker copies **only needed modules** from library into executable.

**Example**: `libc.a` contains `printf`, `scanf`, etc.

### Loader

Copies executable into memory, sets up stack/heap, jumps to `_start` (not `main`).

**OS calls:**
- Windows: `CreateProcess()`
- Linux: `execve()`

---

## Week 15 - Exception and Windows Programming

### Exception Definition
An abrupt change in control flow in response to processor state change (hardware interrupt, system call, error).

### Four Types of Exceptions

| Type | Sync/Async | Cause | Return Behavior |
|------|------------|-------|-----------------|
| **Interrupt** | Async | External hardware (I/O) | Next instruction |
| **Trap** | Sync | Intentional (system call) | Next instruction |
| **Fault** | Sync | Recoverable error (page fault) | Current instruction (retry) |
| **Abort** | Sync | Non-recoverable error | Never returns (terminate) |

### Exception Handling Process

**Steps when exception occurs:**
1. Detect event (hardware/software)
2. Save current state (PC, PSW pushed onto stack)
3. Jump to exception handler using Exception Table
   ```
   Handler Address = ETR + (Exception Number × 4)
   ```
4. Handler runs in **kernel mode**
5. Handler returns to:
   - Current instruction (fault)
   - Next instruction (interrupt/trap)
   - Aborts program (abort)

### Exception Table and ETR
- **Exception Table**: Holds addresses of exception handlers
- **ETR** (Exception Table Base Register): Points to start of table
- **Exception Number**: Index into table

### Exception vs Function Call

| Feature | Exception | Function Call |
|---------|-----------|---------------|
| Return address | May not be next instruction | Always next instruction |
| Stack used | Kernel stack | User stack |
| Mode | Kernel mode | User mode |
| Trigger | Event (hardware/software) | Call instruction |

### Important IA32 Exception Numbers

| Number | Description | Class |
|--------|-------------|-------|
| 0 | Divide error | Fault |
| 13 | General protection fault | Fault |
| 14 | Page fault | Fault |
| 128 | System call (INT 0x80) | Trap |

### Windows Programming Basics

**Entry point**: `WinMain()` (not `main()`)

**Message loop:**
```c
while (GetMessage(&msg, NULL, 0, 0)) {
    TranslateMessage(&msg);
    DispatchMessage(&msg);
}
```

**Window creation flow:**
```
Register Window Class → CreateWindow → ShowWindow → UpdateWindow
```

**Window procedure (WndProc)** processes messages:
- `WM_PAINT` - Window needs repainting
- `WM_DESTROY` - Window being destroyed
- `WM_COMMAND` - User command

### Thread Creation in Windows

**Two methods:**
1. `CreateThread()` - System call
2. `_beginthread()` - C runtime library (calls CreateThread internally, does extra initialization)

**Thread termination:**
- Return from thread function (recommended)
- `ExitThread()` - Self-termination
- `TerminateThread()` - Forced by another thread (dangerous)

### Key Terms

- **Control flow**: Sequence of instruction addresses
- **Exceptional control flow**: Abrupt changes due to exceptions
- **Context switch**: Kernel switching between processes
- **Kernel mode vs User mode**: Privilege levels
- **ISR** (Interrupt Service Routine): Handler for interrupts
- **Hungarian notation**: Naming convention (e.g., `hWnd`, `lpCmdLine`)

---

## 📝 Exam Answer Templates

### Q: Explain Mark & Sweep GC

> Mark & Sweep has two phases: First, starting from roots (stack, globals, registers), it marks all reachable heap objects by traversing object references. Second, it sweeps through the entire heap and frees any unmarked objects. This approach is simple to implement but can cause fragmentation and requires stop-the-world pauses during collection.

### Q: What is a memory leak?

> A memory leak occurs when a program allocates heap memory using malloc but forgets to free it before losing the pointer reference. Over time, this wastes available memory and can eventually crash the program. For example, calling malloc in a function and returning without calling free causes a leak.

### Q: How to debug memory errors?

> Use debug heaps that fill memory with special marker values: 0xCD for clean allocated memory, 0xDD for freed memory, and 0xFD for fence areas. Debug headers track allocation file/line numbers and include guard bytes. Check these guards on free to detect overwrites. Tools like Valgrind or Visual Studio's debug heap also help detect leaks and invalid accesses.

### Q: Compare Copying vs Mark & Sweep GC

> Copying GC uses two memory spaces and copies all live objects from one to the other, which eliminates fragmentation but wastes half the heap. Mark & Sweep works in-place by marking reachable objects and sweeping away unmarked ones, using full heap capacity but potentially fragmenting memory and requiring longer pause times.

### Q: What is a dangling pointer?

> A dangling pointer points to memory that has been freed. Accessing it causes undefined behavior including crashes or data corruption because the memory may be reallocated for other purposes. Best practice is to set pointers to NULL immediately after calling free.

### Q: Explain static linking

> Static linking happens at compile time. The linker takes multiple relocatable object files and merges them into one executable. It performs three steps: (1) Symbol resolution matches function and variable references with their definitions, (2) Combination merges code and data sections from all files, (3) Relocation adjusts all memory addresses so everything fits together. The result is a standalone executable containing all needed code.

### Q: How to improve cache performance in loops?

> Use row-major order in C by looping over rows before columns. Apply loop tiling to process small blocks that fit in cache. Use loop fission to split large loop bodies that exceed instruction cache. Access consecutive memory addresses to utilize spatial locality. Add padding to arrays when needed to prevent cache conflicts where multiple arrays map to the same cache lines.

### Q: Calculate cache miss rate

> Given: 16×16 array of structs (256 total), each struct 16 bytes, cache line 32 bytes. Each line holds 2 structs, so 256 structs need 128 cache lines. First access to each line is a cold miss, giving 128 misses. With 4 writes per struct, total accesses = 256 × 4 = 1024. Miss rate = 128/1024 = 12.5%.

---

## 🎯 Quick Reference

### Memory Bug Prevention Checklist
- [ ] Always initialize pointers
- [ ] Check malloc return value
- [ ] Free all allocated memory
- [ ] Set pointers to NULL after free
- [ ] Use safe string functions (strncpy, fgets)
- [ ] Allocate correct size (count × sizeof(type))
- [ ] Don't return pointers to local variables
- [ ] Free struct members before freeing struct

### Optimization Priority
1. Choose right algorithm (O notation)
2. Profile to find bottlenecks
3. Optimize hot code paths
4. Improve cache locality
5. Reduce procedure calls
6. Use compiler optimizations

### Cache-Friendly Code
- Access arrays in row-major order
- Use loop tiling for large datasets
- Keep working set small
- Avoid pointer chasing
- Pack data structures
- Align data properly
