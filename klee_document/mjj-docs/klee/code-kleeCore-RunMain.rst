===================================
Code Analysis - *runFunctionAsMain*
===================================

:Author: Mao Junjie <eternal.n08@gmail.com>

.. contents::

*Executor*
==========

*runFunctionAsMain*
-------------------

Entry point of executing a bitcode program. It will do the following things.

Prepare for The Arguments
~~~~~~~~~~~~~~~~~~~~~~~~~
The first (argc) is a constant expression. The second (argv array) is a block of memory allocated by *MemoryManager*, which will always be *MemoryObject* 0. The third (envp array) is an offset in the memory block allocated for argv as klee lay out the environment variables at the end of the argv array. Thus the memory block should look like this:

+--------------------+------+--------------------+------+------+
|     argv array     | null |     envp array     | null | null |
+--------------------+------+--------------------+------+------+

where the nulls (which is a pointer) after each array is a sign of termination and the last null is to make uclibc happy according to the comment in the source. The char arrays pointed to by the elements in argv or envp are also allocated by *MemoryManager*.

For each C-sytle string whose pointers are saved in the argv/envp arrays, klee will then allocate a *MemoryObject* for it. These strings will sequentially corresponding to *MO* 1, *MO* 2, etc.

Create the Initial *ExecutionState*
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
The first PC is the entry point of the function. A corresponding stack frame (caller = 0) is pushed on the stack. The arguments are binded in the *locals*.

TODO: What's *ObjectState* binded to *MemoryObject* for? The state (content or expression) of the memory block?

Initialize External Objects And Globals
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
There're 7 external objects added to memory mapping including
- 4-byte errno
- 3 buffers (one 768-byte and two 1536-byte) and their sizes required in libc's character handling
If the id of the first MO allocated during the execution is *n*, the ids for the MOs for external objects should be from *n-7* (the errno) to *n-1*.

Also allocate memory blocks for the globals (if any) and initialize them with some contents.

Create a Process Tree And Start Running
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
The main loop is implemented in *Executor::run()*.

*ExecutorState*
===============

Stack
-----
A stack is represented by a vector of *StackFrame* which includes the caller instruction, the function called, a vector of *MemoryObject* for tracking the units allocated by the instruction **alloca**, an array of *Cell* objects standing for locals (such as the variables and allocated memory cells) and something else. It is a static member in an *ExecutorState* object and thus doesn't require explicit initialization process. The vector is operated mostly by *pushback()* and *popback()* and the top frame of the stack can be fetched by calling *back()*.

Memory Management
=================

*MemoryObject* or MO
--------------------
A *MemoryObject* maintains information about a memory block including its address, its size, whether it's global or local and something else.

*ObjectState* or OS
-------------------
An *ObjectState* maintains the content of a *MemoryObject*. The content of it can be either symbolic or concrete. How OSes are binded with MOs is maintained by the *addressSpace* in *ExecutionState*.

*MemoryManager*
---------------

'Bind' Mechanism
================

Globals
-------
A memory block (represented by MO) is allocated for each global and a corresponding OS is binded with it in the *addressSpace* of the initial *ExecutionState*. The global MOs can be enumerated by iteracting *globalObjects* in the *Executor*.

Locals
------
Each local variable is represented by a *Cell* object which consists of a reference to an expression only. *bindArgument()* fills the corresponding cell with the given value.
