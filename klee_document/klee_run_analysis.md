
2012.10.22
## 启动流程分析

```
. setup-llvm-2.9-env.sh
cd klee_document/mjj-docs/klee/examples/

.gdbinit
set args -debug-print-states ex01.o


gdb --tui klee
...
warning: Source file is more recent than executable.
klee::Executor::runFunctionAsMain (this=0x14eee40, f=0x14e7440, argc=1, argv=0x14e77f0, envp=0x7fffffffdf08) at Executor.cpp:3350
(gdb) backtrace 
#0  klee::Executor::runFunctionAsMain (this=0x14eee40, f=0x14e7440, argc=1, argv=0x14e77f0, envp=0x7fffffffdf08) at Executor.cpp:3350
#1  0x000000000055dcf7 in main (argc=<optimized out>, argv=<optimized out>, envp=0x7fffffffdf08) at main.cpp:1417


```

executor.cpp 
L3376

      argvMO = memory->allocate((argc+1+envc+1+1) * NumPtrBytes, false, true,
                                f->begin()->begin());
      
分配了有 MemoryObject argvMO 来存放 argc+argv+env


L34323 
  void Executor::initializeGlobals(ExecutionState &state) 

       //构建function，
       //构建 errno_addr，lower_addr, upper_addr  from /usr/include/ctype.h 这些变量啥意思？HAVE_CTYPE_EXTERNALS  （在调试时看到没有跳过了这个地方）	
            调用了addExternalObject函数 啥是ExternalObject？
       //构建全局变量
       // 对全局变量进行初始化
            void Executor::initializeGlobalObject(ExecutionState &state, ObjectState *os,
                                      const Constant *c, 
                                      unsigned offset) {


stack
klee::Executor::run (this=0x14eee40, initialState=...) at Executor.cpp:2558
(gdb) where
#0  klee::Executor::run (this=0x14eee40, initialState=...) at Executor.cpp:2558
#1  0x0000000000585016 in klee::Executor::runFunctionAsMain (this=<optimized out>, f=0x14e5928, argc=1, argv=0x14e77f0, envp=0x7fff000001b0)
    at Executor.cpp:3436
#2  0x000000000055dcf7 in main (argc=<optimized out>, argv=<optimized out>, envp=0x7fffffffdf08) at main.cpp:1417

断点信息
(gdb) i b
Num     Type           Disp Enb Address            What
1       breakpoint     keep y   0x000000000055dcda in main(int, char**, char**) at main.cpp:1417
        breakpoint already hit 1 time
2       breakpoint     keep y   0x0000000000585000 in klee::Executor::runFunctionAsMain(llvm::Function*, int, char**, char**) 
                                                   at Executor.cpp:3436
        breakpoint already hit 1 time
3       breakpoint     keep y   0x00000000005844d3 in klee::Executor::run(klee::ExecutionState&) at Executor.cpp:2635
        breakpoint already hit 1 time


L3436
   run(*state);
           调用void Executor::run(ExecutionState &initialState) {

    对run函数的分析

          states.insert(&initialState);  
    什么是seed模式和非seed模式?

           executeInstruction(state, ki);
                    调用L1421:  void Executor::executeInstruction(ExecutionState &state, KInstruction *ki) {

           updateStates(&state);    
