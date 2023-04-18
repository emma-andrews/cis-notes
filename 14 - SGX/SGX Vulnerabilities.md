# SGX Security Model
- on chip and microcode reprogramming *attacks* are possible
- **[[Confinement|cache side channels]]** are there
	- up to user to deal with them, assuming they care (lol no)
- well known attacks in this space
- enclave runs in **user space** with resources controlled by *untrusted os*
- other side channels?
	- most definitely, almost impossible to eliminate all side channels

# Alt Architecture - Sanctum
- security logic in **software**, not hardware/firmware
- allows inspection by computer owner
- can be formally verified and replaced
- uses [[SGX]] idea of enclaves, also in [[Protection Rings|ring 3]]
- enclaves rely on host to proxy system calls to os through runtime library
- **software stack** - has *shims* to enclave
- sanctum software able to run at a *higher privilege level*
	- runs on risc-v architecture that supports a "machine level"
	- not exactly commodity hardware, though
- **partitions cache** to deal with *page table timing attacks*

# Memory-Related Vulnerabilities
## Background
### TLBs
- **translation lookaside buffer** - stores recent translations of virtual memory to physical memory
- multilevel set-associative hardware cache of page table entries (pte)
- l1 tlb split into itlb (instructions) and dtlb (data)
- l2 tlb unified
- processors usually also have another paging structure cache

### Page Tables
- multilevel data structures stored in main memory
- entry at each level points to the next level (phys addr)
- controlled by the *untrusted* os
	- e.g. page fault handling
- pte structure
	- present bit - indicates whether a phys page is mapped
		- page fault if not set
	- access bit - set by processor when pte is referenced by a page table walk
	- dirty bit - set when associated page is updated
	- reserved bits - trigger page fault if set

### SGX Memory Isolation
- virtual and physical memory management
	- enclave programs and metadata use processor reserved memory (prm)
	- mmu prevents access to prm from outside programs
	- epc - subset of prm from which enclave pages are allocated
	- tlb - works in traditional ways for epc
- cpu transition between enclave and non-enclave
	- eenter, eexit instructions
	- async enclave exits (aex) - faults, exceptions, interrupts
	- tlb entries associated with pcid are flushed out
- enclave code (trusted) can access non-enclave memory (untrusted)
- side channels
	- **spatial granularity** - smallest unit of info *directly* observable
	- **temporal observability** - timing signals to help distinguish different scenarios
	- **side effects** - observable anomalies caused by a side channel attack

## Adversary Model
- full control of os, modify kernel
- knowledge of victim *binary* code, but not *source* code
	- base virtual address of enclave
	- virtual to physical mapping
	- can derive mapping of binary code in pages, cache, and dram
- has machine with same configuration prior to attack

# Sneaky Page Monitoring
## Basic (B-SPM)
- continuously check **ptes accessed bits**
- system level process, outside enclave
	- inspect each pte's accessed flag
	- record it when set
	- reset flag
- page access trace - sequence of page sets
	- cannot differentiate visit order of page within same set
	- accessed flag not set again once page number added to tlb
- does not cause aex when page first visited
	- requires invalidating tlb entries proactively
	- use inter-processor interrupt (ipi) to cause tlb shootdown
	- still more lightweight than page fault attack
## Timing Enhancement (T-SPM)
- repeated visit to same pages
	- high resolution leads to more tlb shootdowns, which then leads to **anomalous interrupt rate**
- leverage **timing info**
- secret-dependent info in same page
- if code fragment has *unique* entry and exit pages
	- measure execution time between them
	- once accessed flag of exit page set, flush tlb and reset flags for both
- avoid interrupts between entry and exit pages
	- still learn *execution path* between them

## HyperThreading (HT-SPM)
- observation - when hyperthreading is turned on for a processor, can clear tlb *without* issuing tlb shootdowns
	- makes all **interrupt-based protection** ineffective
- ht transparent to os
	- physical resource shared among two virtual cores
	- *no interrupts generated*
- processes running on two virtual cores **share** some of tlb
	- attacker can remove tlb entries out enclave *without* causing interrupts
	- shared dtlb with 64 entries means only need to access 64 pages to evict

# Cache Attacks
- address translation attacks have **page granularity**
- recommend to align specific code and data blocks to exist entirely within a single page
	- however, only makes sense if page granularity is *finest spatial granularity* achievable
- sgx not designed to deal with cache attacks

## Flush + Reload
1. **flush** - evict memory line from cache
2. **wait** - victim enclaves use cache
3. **reload** - measure time to reload line
- fine grained
- low false positives
- **cannot work on sgx** - no *shared pages* between enclaves

## Prime + Probe
1. **prime stage** - attacker fills cache with own cache lines
2. **idle stage** - victim enclave runs, utilizes cache
3. **probe stage** - measure time to load primed cache lines
- **works on sgx** - granularity is *cache-set*
- attacker runs in *non-enclave mode*

## DRAMA
- **dram attack**
- **dram access latency** - varies depending on which row is stored in row buffer within dram banks
- **attack goal** - victim accessed virtual address $d$
- **setup**
	- create *spying enclave*
	- allocate two memory blocks, $p, p'$
	- $p$ on *physical row* corresponding to $d$
	- $p'$ on different row on same bank
- **attack**
	1. access memory block $p'$
	2. wait for victim enclave operations
	3. measure access time to $p$
- if victim enclave has accessed $d$, then $p$ is in row buffer
	- *faster access time*
### Challenges
- victim memory accesses will be cached
	- epc cacheable by default
	- attacker can manually disable cache, but massive slowdown
- drama may **false detect** unrelated row hits
	- 8kb dram row can be shared by multiple data structure and code regions
- drama cannot acheive finer granularity than 1kb
	- single memory page 4kb distributed over 4 dram rows
	- *best case* - attacker occupies entire row except 1kb
- attack process must **run as enclave** to share prm

# Cache-Dram Attacks
- **goal** - improve *spatial granularity* with cache and drama
- **attack**
	- thread1 - non-enclave mode to prime llc, evict one cache set
	- thread2 - enclave mode to do drama
	- 6 unknown bits = 64b spatial granularity
- **challenges**
	- must *share row* with victim
		- find $p,p'$ given $d$
		- reverse-engineering tools
	- need to reliably measure access time
		- sgxv1 - no *trusted time*
			- cannot read timestamp counter
		- system calls are heavyweight and slow
		- *solution* - smuggle cpu cycle counts inside enclave

# Defenses
- **deterministic multiplexing** - place secret-dependent data and control flows in *same pages*
	- *effectiveness* - orthogonal to cache and dram side channels
- **transactional memory** - page faults cause transaction aborts that are handled *inside* enclave
	- *effectiveness* - does not prevent accessed and dirty flags from being set
- **hardware design changes** - enclave has its own page table so page access patterns are invisible to os
	- *effectiveness* - orthogonal to drama
- **timed execution** - measure execution time of basic blocks
	- larger time indicates enclave code *interrupted by aex*
	- *effectiveness* - some attacks do not cause high rate of aex
- **enclave aslr** - fine-grained address space layout randomization (aslr) for enclave programs
	- *effectiveness* - malicious os might learn memory layout after observing access patterns