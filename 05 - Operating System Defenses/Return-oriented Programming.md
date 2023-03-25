#cis/vulnerabilities/rop #cis/vulnerabilities/stackguard #cis/vulnerabilities/overflow 
## Basics
- general approach to **control flow attacks**
- demonstrates the two general steps of a control flow attack
	- change the program control flow in any way
	- run any code of the attacker's choosing from starting address to return
### Return to libc
- control hijacking without code injection
- *perception* of the attacker cannot execute arbitrary code
	- attacker relies on contents of libc, can you remove `exec()`?
![[libc.png]]
- is this useful?
	- hypothesis
		- attacker can call one libc function after another
		- *but*, only straight line code can be executed
			- no branching or arbitrary functionality like in code injection attacks
		- attacker can only invoke functions in text segment and loaded libraries
			- restrict capabilities if these functions are removed
	- [[Buffer Overflow|W^X]] would be even more valuable
	- what if you do not need to call functions?
	- **thesis**
		- any sufficiently large program codebase results in arbitrary attacker computation and behavior, *without code injection*
			- control-flow integrity is absent
- treat libc as a *corpus* of instruction sequences each ending in return instruction
- fill stack with pointers or data to these sequences
- execution flows through sequences, induces desired behavior
- falsifies perception of return to libc as limited and easy to defeat
- **relation** - like rearranging magazine headlines to make a ransom note
- **Turing-complete**
## ROP Defenses
### Address Space Layout Randomization (ASLR)
- randomly locate **key segments** of memory
- **stack** - makes code injection difficult
	- location of stack is randomized instead of fixed
- **heap** - does this even apply?
	- most likely no
- **libraries** - what does this prevent?
- **example** - booting vista twice loads libraries into different locations
- **system call randomization** - randomize system call ids
- **instruction set randomization (ISR)**
#### Limitations
- all memory containing code address needs to be protected
	- [[Buffer Overflow|StackGuard]] only protects *return address*
- can execute existing code, so there is no need to inject any code
	- done with return-oriented programming
- not full ASLR
	- not all code segments are randomized
	- code must be *position-independent*
	- can be broken with insufficient system entropy
- can be **bypassed**
	- use or load a module that does not support ASLR
	- use a *secondary vulnerability* for memory disclosure
	- find a memory corruption vulnerability for both memory disclosure and code execution
### ROPDefender
- creates **shadow stack** using dynamic binary instrumentation
- on *call*, return address is placed on the shadow and real stack
- on *return*, check if address matches the one on the shadow stack
- **downsides**
	- only defends a subset of ROP
	- dynamic instrumentation is slow, affects overall performance
### G-Free
- compiler removes unintended gadgets at compile time
- encrypts return address in **function prototype**
- use **stack cookies**
- **limitations**
	- need source code
	- every module has to support it
### Static Binary Rewriting
- changes order of instructions
- replaces them with *semantically similar* ones
- **limitation** - needs *automatic disassembly*
### ROPGuard
- runtime checks when critical functions are called
- **checks**
	- return address is executable
	- call stack is valid
	- return address is preceded by a call
## Control Flow Integrity
- **control flow integrity** - force the program to execute according to an *expected control flow graph (CFG)*
- instrument indirect call sites to ensure only legal targets are allowed
	- done by using static or dynamic information
- **coarse-grained** - target all function entry points
	- many functions per target
	- can also construct a set of legal targets by mapping types
## Newton
- previous approaches use **static analysis** to scan binaries for gadgets
- new approach is to use **dynamic analysis**
	- find function call gadgets even if code-reuse defenses are in place
- **Newton** is a dynamic analysis approach
- retrieve gadgets as a set of attacker-controllable CFG edges
- edge in the graph has number of functions, tagged with dependents
- attacker inspects edge and call functions through read and write primitives
- chain edges together to allow *arbitrary functionality*
- run program mimicking real attack stages
	- constrain gadget analysis to ensure only gadgets allowed by code-reuse are used
### Workflow
- start target binary and bring it to a **stable state**
- Newton tracks memory dependencies with *dynamic taint analysis*
- user interacts with victim application using inputs desired during exploit
	- Newton tracks dependencies
- network returns report of gadgets (callsites and targets) under user control given the defense model they have chosen