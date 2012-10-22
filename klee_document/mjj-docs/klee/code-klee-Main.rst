=============================
Code Analysis - Main Function
=============================

:Author: Mao Junjie <eternal.n08@gmail.com>

.. contents::

Entry: tools/klee/main.cpp:1084

Parse arguments
===============

Create watchdog
===============
Only if "--watchdog" is given.

Set handlers
============
Including an 'exit' handler and a 'Ctrl-C' signal handler.

Load bytecode
=============
Saved in *mainModule*.

Initial POSIX runtime environment
=================================
(Ignored)

Link libc
=========
(Ignored)

Get main function and prepare for the arguments
===============================================
Main function is got by getFunction_.

Input arguments follows the input bytecode. E.g., if klee is invoked by the command:

    klee hello.bc a b c

the arguments for hello.bc is "a b c" and argc is 3. The list of arguments is saved in *InputArgv*.

Environment variables is parsed from the given file via --environ. If the options is not given, the environment variables given to klee will be used (i.e. envp).

Running
=======

Replay Mode
-----------

If --replay-out-dir or --replay-out is given, klee will run the bitcode in replay mode. Ignored.

Normal Mode
-----------

1. Prepare for seeds if --seed-out or --send-out-dir is not empty. (TODO: what's this?)
2. Run the main functions with the arguments prepared by calling *runFunctionAsMain*.

Cleanup and print statistics
============================

.. _getFunction: http://llvm.org/doxygen/classllvm_1_1Module.html#a3f156c68d0efa530f05698ca15d66593
