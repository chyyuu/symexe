==============================================
LLVM Source Tree Layout For LLVM Projects [1]_
==============================================

:Author: Mao Junjie <eternal.n08@gmail.com>

.. contents::

Source Tree Layout
==================

lib
  Library source code. Can be linked to tools later.

include
  Header files that are global to the project. This directory is added to the search path of header files when compling the project.

tools
  Source code of executables.

.. [1] `Creating an LLVM Project`_

.. _Creating an LLVM Project: http://llvm.org/docs/Projects.html
