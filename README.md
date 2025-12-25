# 💻 System Level Programming

Notes on System Level Programming exam based on CSAPP (Computer Systems: A Programmer's Perspective).

---

## 📝 Past Exam Questions & Model Answers

### Q1: IEEE 754 Conversion (5 pts)

**Convert 20.5 to IEEE 754:**

**Steps:**
1. Binary: 20 = 10100₂, 0.5 = 0.1₂ → 10100.1₂
2. Normalize: 1.01001 × 2⁴
3. Components:
   - Sign: 0 (positive)
   - Exponent: 4 + 127 = 131 = 10000011₂
   - Mantissa: 01001000000000000000000

**Answer:** `0 10000011 01001000000000000000000`

**Convert -20.5:** Same but sign bit = 1

---

### Q2: Generational GC (5 pts)

**Answer:**
Divides heap into generations based on age. Young generation (G0) holds new objects and is collected frequently (minor GC). Surviving objects promoted to old generation (G1+) which is collected rarely (major GC). Works because most objects die young, so focusing on young generation is efficient.

---

### Q3: Amdahl's Law (5 pts)

**Problem:** CPU 20× faster on search. Old CPU spends 90% time on search. Speedup?

**Solution:**
```
Speedup = 1 / ((1-P) + P/S)
        = 1 / ((1-0.9) + 0.9/20)
        = 1 / (0.1 + 0.045)
        = 6.9×
```

Limited by 10% unimproved time.

---

### Q4: Datalab Puzzles (10 pts)

**minusOne - return -1:**
```c
int minusOne(void) {
    return ~0;
}
```
Explanation: ~0 inverts all bits → 0xFFFFFFFF = -1

**isPower2 - check if power of 2:**
```c
int isPower2(int x) {
    return (!!x) & (!(x & (x + (~0))));
}
```
Explanation: Power of 2 has one bit set. x & (x-1) = 0 for powers of 2.

---

### Q5: Function Side Effects (10 pts)

**Question:** Do these behave the same?
```c
int func1(x) { return f(x) + f(x) + f(x) + f(x); }
int func2(x) { return 4*f(x); }
```

**Answer:**

**Same when f(x) is pure:**
```c
int f(int x) { return x * x; }
```

**Different when f(x) has side effects:**
```c
int counter = 0;
int f(int x) { counter++; return x; }
```
- func1: calls f() 4 times, counter=4
- func2: calls f() 1 time, counter=1

**Impact:** Compiler can't optimize func1→func2 because procedure side effects block optimization. Must assume f() changes global state.

---

### Q6: Windows Event Handler (10 pts)

**Code:**
```c
case WM_LBUTTONDOWN:
    xPos = GET_X_LPARAM(lParam);      // Get mouse x-coordinate
    yPos = GET_Y_LPARAM(lParam);      // Get mouse y-coordinate
    GetLocalTime(&systime);            // Get current system time
    sprintf(time,"%4d-%2d-%2d %2d:%2d", // Format time string
            systime.wYear, systime.wMonth, systime.wDay,
            systime.wHour, systime.wMinute);
    hdc = GetDC(hwnd);                 // Get device context for drawing
    TextOut(hdc, xPos, yPos, time, strlen(time)); // Draw time at click position
    ReleaseDC(hwnd, hdc);              // Release device context
    return 0;                          // Message processed
```

**Function:** Displays current time as text where user clicks left mouse button.

---

### Q7: Stack Frame Fill-in-Blanks (10 pts)

**Given:** Function call `search(root, 0x4e)` at line 5

**Stack Layout:**
```
High Address
+------------------+ 0xbffff880 ← Initial ebp
| old ebp (saved)  |
+------------------+ 0xbffff87c
| rtn addr         |
+------------------+ 0xbffff878
| 0x4e             | ← key argument
+------------------+ 0xbffff874
| 0x804961c        | ← node argument (root)
+------------------+ 0xbffff870
| old ebp          | ← Current ebp
+------------------+
Low Address
```

**Key points:**
- Stack grows down
- Arguments pushed right-to-left
- Return address pushed by `call`
- Old ebp saved by callee

---

### Q8: Cache Miss Calculation (10 pts)

**Problem:**
- 8×8 array of structs
- Each struct = 12 bytes
- Cache line = 32 bytes
- How many misses on first scan?

**Solution:**
```
Structs per line = 32/12 = 2 (with waste)
Total structs = 64
Lines needed = 64/2 = 32
First access to each line = miss

Answer: 32 misses
```

---

### Q9: Identify Bug (10 pts)

**Code:**
```c
char* getString() {
    char buffer[100];
    gets(buffer);
    return buffer;
}
```

**Bugs:**
1. `gets()` - no bounds check → buffer overflow
2. Returns pointer to local variable → dangling pointer

**Fix:**
```c
char* getString() {
    char *buffer = malloc(100);
    fgets(buffer, 100, stdin);
    return buffer;
}
```

---

### Q10: Static Linking Process (10 pts)

**Answer:**

Static linking creates executable from object files in 3 steps:

**1. Symbol Resolution:**
Match each symbol reference to its definition. Strong symbols (functions, initialized globals) can appear once. Weak symbols (uninitialized globals) can be overridden.

**2. Combination:**
Merge sections from all object files:
- All `.text` sections combined
- All `.data` sections combined  
- All `.bss` sections combined

**3. Relocation:**
Adjust symbol addresses to final memory locations. Update all references in code and data using relocation entries. Each entry contains offset, symbol, and type.

Result: Standalone executable with all code included.

---

## ⚡ Last-Minute Review Checklist

**Before Exam - Know These Cold:**

□ Stack frame layout (arguments → return → ebp → locals)  
□ Amdahl's Law formula  
□ Cache miss calculation method  
□ 4 GC algorithms (name + 1 pro + 1 con each)  
□ Buffer overflow prevention (4 methods)  
□ Static linking 3 steps  
□ 4 exception types  
□ Common memory bugs (7-8 types)  
□ Bit operations table  
□ Optimization techniques (code motion, unrolling)  

---

## 🎯 Teacher's Priority Topics (MUST KNOW)

| Priority | Topic | Week | Key Points |
|----------|-------|------|------------|
| ⭐⭐⭐ | **Stack Frame** | 05 | Function call/return, ESP/EBP/EIP, stack layout |
| ⭐⭐⭐ | **Cache Miss Rate** | 13 | Calculation, optimization techniques |
| ⭐⭐⭐ | **Static Linking** | 14 | Symbol resolution, relocation, process |
| ⭐⭐ | **Bit Operations** | 02 | Bitwise ops, masks, shifts |
| ⭐⭐ | **Buffer Overflow** | 06 | How to attack, how to prevent |
| ⭐⭐ | **GC Algorithms** | 09 | 4 algorithms, pseudocode, pros/cons |
| ⭐⭐ | **Optimization** | 11 | Code motion, loop unrolling, blockers |
| ⭐⭐ | **Amdahl's Law** | 10 | Speedup calculation formula |

---

## 📋 Quick Topic Index

### Week 01 - Program Execution Basics
- 6 phases: Edit → Preprocess → Compile → Assemble → Link → Load → Execute
- 4 hardware parts: CPU, Memory, I/O, Bus
- Virtual memory: Each process thinks it has full memory space

### Week 02 - Bits & Data ⭐⭐
**Bit Operations:**
```c
&  (AND)    - Masking:     x & 0xFF gets last byte
|  (OR)     - Setting:     x | 0x80 sets bit 7
^  (XOR)    - Toggling:    x ^ 0xFF inverts bits
~  (NOT)    - Inverting:   ~0 gives -1
<< (shift)  - Multiply:    x << 3 means x * 8
>> (shift)  - Divide:      x >> 2 means x / 4
```

**Encoding:**
- Signed: Two's complement (flip bits + 1 for negative)
- Float: IEEE 754 (sign | exponent | mantissa)
- Endianness: Little (x86) vs Big (network)

### Week 03 - Assembly Basics
```asm
mov  - move data
add/sub - arithmetic
cmp  - compare (sets flags)
jmp/je/jne - jumps
call - push return address, jump to function
ret  - pop return address, jump back
push/pop - stack operations
```

### Week 04 - Pointers & Structs
- Pointer: `*p` = value, `&x` = address
- Struct alignment: Compiler adds padding for speed
- Arrays: `arr[i] == *(arr + i)`

### Week 05 - Stack Frame ⭐⭐⭐

**Function Call Process:**
```
Caller:                 Callee:
1. Push args (right→left)  1. Push old ebp
2. call (push return addr) 2. ebp = esp
                           3. Allocate locals
                           4. Execute
                           5. esp = ebp
                           6. Pop ebp
                           7. ret (pop return addr)
```

**Stack Layout (high→low address):**
```
+------------------+
| Arguments        |
+------------------+
| Return Address   |
+------------------+
| Old EBP          | ← EBP points here
+------------------+
| Local Variables  | ← ESP points here
+------------------+
```

**Key Registers:**
- **ESP**: Stack top (decreases when push, increases when pop)
- **EBP**: Frame base (stays fixed during function)
- **EIP**: Next instruction to execute

**Stack grows DOWN** (push decreases ESP, pop increases ESP)

### Week 06 - Buffer Overflow ⭐⭐

**How to Attack:**
```
1. Input > buffer size
2. Overwrite: locals → old EBP → return address
3. Set return address = shellcode address
4. When function returns, jumps to malicious code
```

**How to Prevent:**
```
✓ Use strncpy not strcpy
✓ Use fgets(buf, size, stdin) not gets()
✓ Stack Canaries: Random value before return addr, check on exit
✓ DEP: Mark stack non-executable
✓ ASLR: Randomize stack addresses
```

### Week 07 - Memory Allocation

**Stack vs Heap:**
| Feature | Stack | Heap |
|---------|-------|------|
| Speed | Fast | Slow |
| Management | Auto | Manual (malloc/free) |
| Size | Small | Large |
| Lifetime | Function scope | Until freed |

**Heap Structures:**
- Free list (linked list of free blocks)
- Allocation tree (Red-Black tree for fast search)

**Fragmentation:**
- Internal: Wasted space inside block
- External: Free space too scattered to use

### Week 08 - Memory Bugs (From Homework)

**Common Bugs to Identify:**
```c
// 1. Uninitialized pointer
int *p;
*p = 5;  // ✗ CRASH

// 2. Wrong malloc size
int *a = malloc(100);  // ✗ 100 bytes not 100 ints
int *a = malloc(100 * sizeof(int));  // ✓

// 3. Off-by-one
int a[10];
for(i=0; i<=10; i++)  // ✗ Should be i<10

// 4. Missing null terminator
char *s = malloc(strlen(src));  // ✗ No space for '\0'

// 5. Double free
free(p);
free(p);  // ✗ ERROR

// 6. Memory leak
void f() {
    int *p = malloc(100);
    return;  // ✗ Never freed
}

// 7. Returning local address
int* f() {
    int x = 5;
    return &x;  // ✗ x destroyed after return
}

// 8. Using freed memory
free(p);
p[0] = 1;  // ✗ Dangling pointer
```

### Week 09 - Garbage Collection ⭐⭐

**Four Algorithms:**

#### 1. Mark & Sweep
```c
GC() {
    mark(roots);  // Mark reachable from roots
    sweep();      // Free unmarked
}
```
- ✓ Uses full heap
- ✗ Fragmentation, pauses

#### 2. Copying (Cheney's)
```
Two spaces: from-space, to-space
Copy live objects to to-space
Swap spaces
```
- ✓ No fragmentation
- ✗ Only uses half heap

#### 3. Reference Counting
```c
// On a = b:
inc_count(b);
dec_count(a);
if (count(a) == 0) free(a);
```
- ✓ Immediate reclaim
- ✗ Can't handle cycles, overhead

#### 4. Generational
```
Young gen (G0) - collected often
Old gen (G1)   - collected rarely
Objects promoted if they survive
```
- ✓ Fast (most garbage in young gen)
- ✗ Complex, needs write barriers

### Week 10 - Performance ⭐⭐

**Amdahl's Law:**
```
Speedup = 1 / ((1 - P) + P/S)

P = fraction improved
S = speedup of that fraction

Example: 90% improved by 20×
= 1 / ((1-0.9) + 0.9/20)
= 1 / 0.145 = 6.9×
```

**80/20 Rule:** 80% time in 20% code → optimize that 20%

**Timing:**
- Wall clock: Total time
- CPU time: Time on your code
- Tools: `rdtsc`, `gettimeofday()`, `clock()`

### Week 11 - Optimization ⭐⭐

**Optimization Blockers:**
- Memory aliasing (two pointers may overlap)
- Procedure side effects (function changes global state)

**Techniques:**

**1. Code Motion:** Move loop-invariant out
```c
// Before
for (i=0; i<n; i++)
    a[i] = strlen(s);

// After
int len = strlen(s);
for (i=0; i<n; i++)
    a[i] = len;
```

**2. Reduce Procedure Calls:**
```c
// Before
for (i=0; i<strlen(s); i++)  // strlen called n times

// After
int n = strlen(s);
for (i=0; i<n; i++)  // strlen called once
```

**3. Loop Unrolling:**
```c
// Before
for (i=0; i<n; i++)
    sum += a[i];

// After (2x unroll)
for (i=0; i<n; i+=2)
    sum += a[i] + a[i+1];
```

**4. Eliminate Memory Refs:** Use registers (local vars) not memory

**Quick Tips:**
- Use `++i` not `i++`
- Avoid malloc/printf in loops
- Use `switch` not long `if-else`

### Week 12 - Memory Hierarchy

**Hierarchy:** Registers → L1 → L2 → L3 → RAM → Disk

**Locality:**
- **Temporal**: Recently used → likely used again
- **Spatial**: Nearby data → likely used soon

**Cache Misses:**
- Cold: First access
- Capacity: Working set too large
- Conflict: Address collision

### Week 13 - Cache ⭐⭐⭐

**Cache Types:**
- Direct-mapped: 1 location per block
- N-way associative: N locations per block
- Fully associative: Any location

**Address Split:**
```
[Tag | Set Index | Block Offset]
```

**Miss Rate Calculation (EXAM COMMON):**

**Example:**
```
Struct = 16 bytes
Cache line = 32 bytes → 2 structs/line
Array[16][16] = 256 structs
Each struct accessed 4 times = 1024 accesses

Lines needed = 256/2 = 128
First access to each line = miss
Misses = 128
Miss rate = 128/1024 = 12.5%
```

**Cache-Aware Programming:**

**1. Row-major order (C arrays):**
```c
// Good ✓
for (i=0; i<N; i++)
    for (j=0; j<N; j++)
        sum += a[i][j];

// Bad ✗
for (j=0; j<N; j++)
    for (i=0; i<N; i++)
        sum += a[i][j];
```

**2. Loop Tiling (Blocking):**
```c
for (i=0; i<N; i+=B)      // Process BxB blocks
    for (j=0; j<N; j+=B)
        for (ii=i; ii<i+B; ii++)
            for (jj=j; jj<j+B; jj++)
                // Work here
```

**3. Loop Fission:** Split large loop to fit in instruction cache

**4. Loop Fusion:** Combine loops over same data

### Week 14 - Linking ⭐⭐⭐

**Static Linking (3 Steps):**
```
1. Symbol Resolution
   - Match references to definitions
   - Strong symbols (functions, initialized globals)
   - Weak symbols (uninitialized globals)

2. Combination
   - Merge .text, .data, .bss sections

3. Relocation
   - Adjust addresses to final locations
```

**Object File Sections:**
- `.text`: Code
- `.data`: Initialized globals
- `.bss`: Uninitialized globals (no space in file)
- `.symtab`: Symbol table

**Symbol Rules:**
1. Multiple strong symbols → error
2. Strong overrides weak
3. Multiple weak → linker picks one

**Static Library (.a/.lib):**
- Archive of .obj files
- Linker copies only needed modules

### Week 15 - Exceptions

**Four Types:**

| Type | Sync? | Cause | Return |
|------|-------|-------|--------|
| Interrupt | Async | I/O device | Next instruction |
| Trap | Sync | System call | Next instruction |
| Fault | Sync | Recoverable error | Current instruction |
| Abort | Sync | Hardware failure | Never |

**Exception Handling:**
```
1. Event detected
2. Save state (push PC, PSW)
3. Jump to handler: ETR + (exception# × 4)
4. Handler runs in kernel mode
5. Return or abort
```

**Important Exception Numbers:**
- 0: Divide by zero (fault)
- 13: General protection (fault)
- 14: Page fault (fault)
- 128: System call (trap)

---
