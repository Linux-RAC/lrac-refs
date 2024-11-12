# A GPF Privilege instruction violation example

This is privilege instruction violation that causes GPF. The main cause of this failure is the **RDMSR** executed by the C code listed below.

## Original post from StackOverflow

The original post was accessed last time Nov 12, 2024 and still existed, but you can retrieve its content by reading below:

------
**Content authorship:**

* **Author**: [merlin2011](https://stackoverflow.com/users/391161/merlin2011) 
* **When**: Mar 10, 2014 at 5:05
* **URL**: https://stackoverflow.com/questions/22292447/why-a-segfault-instead-of-privilege-instruction-error

------

### Why a segfault instead of privilege instruction error?

I am trying to execute the privileged instruction `rdmsr` in user mode, and I expect to get some kind of privilege error, but I get a segfault instead. I have checked the `asm` and I am loading `0x186` into `ecx`, which is supposed to be `PERFEVTSEL0`, based on the [manual](https://download.intel.com/products/processor/manual/325384.pdf), page 1171.

**What is the cause of the segfault, and how can I modify the code below to fix it?**

I want to resolve this before hacking a kernel module, because I don't want this segfault to blow up my kernel.

Update: I am running on Intel(R) Xeon(R) CPU X3470.

```c
#define _GNU_SOURCE 
#include <stdio.h>
#include <stdlib.h>
#include <inttypes.h>
#include <sched.h>
#include <assert.h>
uint64_t
read_msr(int ecx)
{ 
    unsigned int a, d; 
    __asm __volatile("rdmsr" : "=a"(a), "=d"(d) : "c"(ecx)); 
    return ((uint64_t)a) | (((uint64_t)d) << 32); 
} 
int
main(int ac, char **av)
{
    uint64_t start, end;
    cpu_set_t cpuset;
    unsigned int c = 0x186;
    int i = 0;
    CPU_ZERO(&cpuset);
    CPU_SET(i, &cpuset); 
    assert(sched_setaffinity(0, sizeof(cpuset), &cpuset) == 0); 
    printf("%lu\n", read_msr(c)); 
    return 0;
} 
```

------
**⚠️ WARNING**: do not change this file path.