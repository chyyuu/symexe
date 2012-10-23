# 
[KLEE: Unassisted and Automatic Generation of High-Coverage Tests for Complex Systems Programs](http://static.usenix.org/event/osdi08/tech/full_papers/cadar/cadar_html/)

## KLEE architecture

### KLEE基本结构
KLEE functions as a hybrid between an operating system for symbolic processes and an interpreter. Each symbolic process has a register file, stack, heap, program counter, and path condition. To avoid confusion with a Unix process, we refer to KLEE's representation of a symbolic process as a **state**. Programs are compiled to the LLVM  assembly language, a RISC-like virtual instruction set. KLEE directly interprets this instruction set, and maps instructions to constraints without approximation (i.e. bit-level accuracy).

### state表示
Unlike a normal process, storage locations for a state -- registers, stack and heap objects -- refer to expressions (trees) instead of raw data values. The leaves of an expression are symbolic variables or constants, and the interior nodes come from LLVM assembly language operations (e.g., arithmetic operations, bitwise manipulation, comparisons, and memory accesses). Storage locations which hold a constant expression are said to be concrete.

### 条件分支
Conditional branches take a boolean expression (branch condition) and alter the instruction pointer of the state based on whether the condition is true or false. KLEE queries the constraint solver to determine if the branch condition is either provably true or provably false along the current path; if so, the instruction pointer is updated to the appropriate location. Otherwise, both branches are possible: KLEE **clones the state** so that it can explore both paths, updating the instruction pointer and path condition on each path appropriately.

### 压缩state表示所占空间
Since KLEE tracks all memory objects, it can implement copy-on-write at the object level (rather than page granularity), dramatically reducing per-state memory requirements. By implementing the heap as an immutable map, portions of the heap structure itself can also be shared amongst multiple states (similar to sharing portions of page tables across fork()). Additionally, this heap structure can be cloned in constant time, which is important given the frequency of this operation.**This approach is in marked contrast to EXE, which
used one native OS process per state.**

### Query optimization查询优化

#### Expression Rewriting

#### Constraint Set Simplification.

#### Implied Value Concretization

#### Constraint Independence

#### Counter-example Cache

This mapping is stored
in a custom data structure — derived from the UBTree
structure of Hoffmann and Hoehler — which allows
efficient searching for cache entries for both subsets
and supersets of a constraint set.

### State scheduling状态调度

#### Random Path Selection


#### Coverage-Optimized Search


## Environment Modeling 环境建模

When code reads values from its environment —
command-line arguments, environment variables, file
data and metadata, network packets, etc—we conceptually
want to return all values that the read could legally
produce, rather than just a single concrete value.

KLEE has about 2,500 lines of code to
define simple models for roughly 40 system calls (e.g.,
open, read, write, stat, lseek, ftruncate,
ioctl).

### modeling the file system