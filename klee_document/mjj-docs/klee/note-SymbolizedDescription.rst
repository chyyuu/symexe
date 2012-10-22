=====================================================
A Symbolized Description of Execution Process in Klee
=====================================================

:Author: Mao Junjie <eternal.n08@gmail.com>

.. contents::

Symbols
=======

Static Program
--------------
The program is represented as a map from address to instructions, i.e.

.. math::
    P ::= addr \mapsto inst

Runtime Execution State
-----------------------

The memory state is defined as

.. math::
    M ::= m \mapsto o

where :math:`m` is a *MemoryObject* and :math:`o` is an *ObjectState*. They are defined as follows.

.. math::
    m ::= \langle base, size \rangle

.. math::
    o ::= [] | byte,o

where :math:`base` and :math:`size` determine the range of the memory and :math:`byte` is a container of one-byte memory either being concrete or symbolic.

The globals

.. math::
    G ::= id \mapsto m

is a map from identifiers to *MemoryObject*.

A stack

.. math::
    \Sigma ::= [] | \sigma, \Sigma

where :math:`\sigma` is a stack frame and is defined as

.. math::
    \sigma ::= \langle pc, \Delta, \alpha \rangle

:math:`pc` is the program counter. :math:`\Delta` stands for the locals of the current function and is a map from the local identifiers to its value, i.e.

.. math::
    \Delta ::= id \mapsto V

:math:`\alpha` is a list of allocated local memory blocks using *alloca*:

.. math::
    \alpha ::= [] | m, \alpha

An execution state :math:`S` is defined as

.. math::
    S ::= \langle M, G, \Sigma \rangle

Annotations
===========

Reference to Instruction
------------------------
Given a program :math:`P` and a location whose address is :math:`a`, the instruction at the location is referred to as :math:`P[a]`.

Increment of a Program Counter
------------------------------
Given a program counter :math:`pc` which is an address to an instruction, :math:`pc_{next}` means the address of the next instruction in the program.

Mapping
-------
Suppose a map :math:`m ::= A \mapsto B` and :math:`a,a' \in A`, :math:`b,b' \in B`, then we define

- :math:`a \in Dom(m)` is true if the mapping of :math:`a` is defined in :math:`m`.

- :math:`b = m(a)` is true if :math:`a \in Dom(m)` and the mapping of :math:`a` in :math:`m` is :math:`b`.

- :math:`m[a \mapsto b]` is a new map defined as

.. math::
    m[a' \mapsto b'](a) =
        \left\{ \begin{matrix}
	m[a] & a \not = a' \\
	b' & a = a'
	\end{matrix}\right.


Reference to Tuple Elements
---------------------------
Suppose a tuple :math:`T ::= \langle a, b, c\rangle`, we write :math:`T_a` to refer to the element :math:`a` of the tuple :math:`T`.

List Operations
---------------
Suppose a list :math:`L = [l_1, l_2, ..., l_n]`

- Append: :math:`L' = L + l = [l_1, l_2, ..., l_n, l]`

- Remove: :math:`L' = L - l_i = [l_1, ..., l_{i-1}, l_{i+1}, ..., l_n]`

Stack Operations
----------------
Suppose :math:`\Sigma = [\sigma_1, \sigma_2, ..., \sigma_n]`,

- Push: :math:`\Sigma' = push(\Sigma, \sigma) = \Sigma + \sigma`

- Pop: :math:`\Sigma' = pop(\Sigma) = \Sigma - \sigma_n`

- Top: :math:`\sigma = top(\Sigma) = \sigma_n`

- Substitution: :math:`\Sigma' = \Sigma \oplus \sigma = \Sigma - \sigma_n + \sigma`

Memory Operations
-----------------

- Allocate: :math:`\langle m, o \rangle = alloc(n)` where n is the size of the memory block.

- Free: :math:`free(m)`

- Initial values: :math:`o = init(m)`. :math:`o` is of size :math:`m_size` and is initialized with arbitrary values.


Initial State
=============

Semantics of LLVM instructions
==============================

The instructions are arranged according to the LLVM Assembly Language Reference Manual [1]_. The result of each instruction is given in the form of one or more execution states assuming that the program is :math:`P` and the execution state before the instruction is invoked is :math:`S = \langle M, G, \Sigma \rangle` and :math:`\sigma = top(\Sigma) = \langle pc, \Delta, \alpha \rangle`.

Terminator Instructions
-----------------------

Binary Operations
-----------------

Bitwise Binary Operations
-------------------------

Vector Operations
-----------------

Aggregate Operations
--------------------

Memory Access and Addressing Operations
---------------------------------------

*alloca*
~~~~~~~~

+--------------------------------------------------------------------------------+
|      <result> = alloca <type>[, <ty> <NumElements>][, align <alignment>]       |
+--------------------------------------------------------------------------------+

It is assured that the size of <type> is fixed. When <NumElements> is not symbolic, the updated state :math:`S'` is defined as

.. math::
    Alloca
    \frac{
    S = \langle M, G, \Sigma \rangle, \sigma = top(\Sigma) = \langle pc, \Delta, \alpha \rangle
    }{
    S' = \langle M[m \mapsto init(m)], G, \Sigma \oplus \sigma' \rangle,
    \sigma' = \langle pc_{next}, \Delta[\hat{r} \mapsto m_{addr}], \alpha + m \rangle,
    m = alloc(\hat{t} \times \hat{N})
    }

TODO: How if <NumElements> is symbolic?

Conversion Operations
---------------------

Other Operations
----------------

.. [1] `LLVM Assembly Language Reference Manual`_

.. _LLVM Assembly Language Reference Manual: http://llvm.org/releases/2.9/docs/LangRef.html

