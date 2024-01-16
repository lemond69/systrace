# systrace
Bypass usermode hooking, while preserving full stack frame. Built for x64.

## Technique
We register a VEH handler, then call INT1 to trigger it. We flush registers rcx, rdx, r8, and r9, saving them elsewhere, and continue execution. For first execution, we single-step till we reach a syscall (which will take a while), for subsequent executions, we set a hardware breakpoint on the address of the syscall instruction (much faster). Once we reach syscall, we restore all registers. This means any usermode EDR hooks will see the registers as if they were all NULL, making it impossible to detect what arguments the winapi is using, hence easily bypassing usermode EDR hooks. Unlike manual syscalling, this method falls through whatever hooks the EDR has in place, making it impossible to distinguish from genuine calls via thread stack inspection from InstrumentationCallbacks.

## Compilation
Compiled with mingw gcc using `x86_64-w64-mingw32-gcc systrace.c`
