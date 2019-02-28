## MoonShine-Optimizing OS Fuzzer Seed Selection with Trace Distillation

This article is about fuzzing in finding OS vulnerability, which has little to do with me, but some thoughts may inspire me in the future (or not), so I read it through briefly.

 OS fuzzers primarily test the system-call interface between the OS kernel and user-level applications for security vulnerabilities. However, generating good seeds for OS fuzzing is a hard problem as the behavior of each system call depends heavily on the OS kernel state created by the previously executed system calls, so a commonly used method is using hand-coded rules for generating valid seed sequences of system calls. Obviously, these hand-coded rules are both complex and limiting the code coverage.

MoonShine is a novel strategy which derives from a simple idea that we can generate seeds from real-world programs. This is easy to understand, for real-world programs always conform to those hand-coded rules (if not, the programs will crash immediatly), and they are always beyond our **stupid** mutation strategy : D.

But there is still a vital problem to solve:

    A real-world program may consists of a large amount of syscalls, most of which are useless for expanding code coverage rate. So we have to find an appropriate strategy to "distill" it, until it only contains what we need.

So they raise a concept named "dependencies" of syscall, which I think is inspired by some theory in compilation. It's easy to understand anyway, so I just put it here:

1. Explicit Dependencies. We define a system call ci to be explicitly dependent on another system call cjif cjproduces a result that ciuses as an input argument. For example, in Figure 1, the open call in line 2 is an explicit dependency of the mmap call in line 3 because open returns a file descriptor (3) that is used by mmap as its fourth argument. If open did not execute, then mmap would not return successfully, which means it would take a different execution path in the kernel.

2. Implicit Dependencies. A system call ciis defined to be implicitly dependent on cjif the execution of cjaf- fects the execution of cithrough some shared data structure in the kernel, even though there is no overlap between cj’s output and ci’s input arguments. In Figure 1, the mlockall call is an implicit dependency of the msync call. The mlockall call instructs the kernel to lock all memory pages that are mapped into the process’s address space to avoid swapping. When msync is called with the flag MS_INVALIDATE on an mmap’d page to in- validate all changes, msync fails with an -EBUSY error because the pages were locked in memory. In this case, the mlockall call affects the behavior of msync through the vma->vm_flags as shown in Figure 2 even though these calls do not share any arguments.

![image](https://github.com/kongjiadongyuan/image_in_a_mess/raw/master/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-02-28%20%E4%B8%8A%E5%8D%8811.39.18.png)

![image](https://github.com/kongjiadongyuan/image_in_a_mess/raw/master/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-02-28%20%E4%B8%8A%E5%8D%8811.39.27.png)



So the following steps are easy to guess, when we get a real-world program, we just distill it and get what we want as seed, then mutate it and search it, until we get Poc or exp, etc.

Here is the algorithm:

![image](https://github.com/kongjiadongyuan/image_in_a_mess/raw/master/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-02-28%20%E4%B8%8A%E5%8D%8811.40.06.png)

![image](https://github.com/kongjiadongyuan/image_in_a_mess/raw/master/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-02-28%20%E4%B8%8A%E5%8D%8811.40.01.png)

Finally, put the overview of the system here:

![image](https://github.com/kongjiadongyuan/image_in_a_mess/raw/master/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-02-28%20%E4%B8%8A%E5%8D%8811.39.40.png)



### Conclusion

Although fuzzing is not my direction, but the idea is really interesting. The core idea is that we can use real-world program to substitute those rules we have to hand-code, and use some methods to distill them, finally get what we want. Maybe it can be used in automatic exploit generation, but it still needs more thinking. GL.
