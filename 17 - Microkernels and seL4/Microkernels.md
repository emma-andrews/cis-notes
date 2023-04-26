this slide deck is 176 pages, not going to finish it

# Monolithic Kernels vs. Microkernels
- **microkernel** - flexible, minimal platform
	- mechanisms instead of policies
	- actual os functionality provided by *user-mode servers*
	- servers invoked by kernel-provided *message-passing mechanisms (ipc)*
		- ipc performance is critical
	- features add usermode code
	- policies replace usermode code
	- kernel complexity is stable
	- *pros* - adaptable, dependable, highly optimized
	- *principle of minimality*
		- **pros**
			- easy to implement, porting is limited by arch-specific micro-optimizations though
			- easier to optimize
			- hopefully enables a minimal [[Enforcement|tcb]]
				- smaller attack surface, fewer failure modes
			- easier to debug, maybe even prove correct
- **monolithic kernel** - traditional os kernel
	- new features add code kernel
	- new policies add code kernel
	- kernel complexity grows

# L4 Microkernel Family
- **l4** - family of *high-performance* microkernels
	- second generation
	- solved performance issue
	- left a number of security issues unresolved
	- *problem* - ad hoc approach to protection and resource management
		- global thread name space resulted in [[Confinement|covert channels]]
		- threads as ipc targets lead to insufficient encapsulation
		- single kernel memory pool lead to dos attacks
		- insufficient delegation of authority resulted in limited flexibility and performance
		- unprinciple management of time
- **sel4** - microkernel designed to address problems of second gen l4 microkernels
	- designed to support safety and security critical systems
	- principled time management

# seL4
## Principles
- **capabilities** - single protection mechanism
	- proper time management finished
- all **resource-management** policy at user level
	- painful to use
	- need to provide standard memory management library, results in l4 like programming model
	- suitable for *formal verification*, achieved in 2009
- **assurance** - systematic evaluation and testing
- **formal verification** - mathematical proof
- **assurance + formal verif** - establish correctness of mechanisms design and their implementation