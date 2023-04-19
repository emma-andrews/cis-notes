# VM Systems Today
- development of **[[Virtual Machine Basics|virtual machine monitor]]** for x86 systems made it extremely popular for everyone
	- took a while though
- everything is a vm monitor now
	- e.g. vmware, xen, nettop

# VMM Principles
## Isolation and Network
- vmware and nettop assume that vmm will **isolate** guest vms
- only problem left is to control **inter-vm communication**
	- only other communication is via *network*
- vmware uses network layer, supports vmci for direct communication between vms
- nettop built on vmware where only vms of same label may communicate

## As Processes
- type 2 vm systems can treat vms as **processes**
- kvm uses [[SELinux]] to control access of vms as if they are a process
	- vms are processes to host os
	- vms can access host os resources
- uses selinux to control vm access
- many vmm resources that may be used to **communicate**
- **sHype** - adds [[Reference Monitor Concept|reference monitor]] for some objects and the privileged vm used for networking
- **xen security modules (xsm)** - adds reference validation on xen hypervisor's distribution of these resources
	- less trust in privileged vms, so *finer-grained policy* results
- minimizing tcb vs simplicity
- run hypervisor in *isolated partition*

# Xen as Reference Monitor
- **xsm** in xen
- scope includes dom0 linux and user level
- **mediation** - xsm controls vmm operations
	- [[SELinux]] in dom0
	- use network to communicate
- **tamperproof** - xen has much larger [[Enforcement|tcb]]
	- more flexible
- **verification** - lots of code, selinux style policy

# OS vs VM
- **conventional os** - broken easily and often
- **vm system** - coarser control based on *isolation*
- if we trust vm system and do not trust os, what happens?
	- several options
## Untrusted OS
- need **services** from untrusted os
- run programs on specialized, trusted system
- use conventional os like an *untrusted network*
- run programs directly on vmm
- **options**
	- *microkernels* - reduce code running in kernel mode
		- still need same services
		- just as "trusted" running in user space
	- *selinux* etc
	- *isolate* in vm systems - deploy application on custom os
		- still have to trust all services used
- **solution** - should be usable to a lot of people ideally
	- separate application from other apps/kernel
		- use *separate vm* for app with a private os separate from commodity os
	- provide interaction between apps/kernel in secure way
		- app dev decides what is and isn't sensitive
			- separate *sensitive part* into vm on private os
			- public part remains on commodity os
			- interaction between apps also passes through kernel
			- sensitive part communicates through system calls with other apps
		- use *policy* to decide if system calls are to be performed on commodity os or private os
	- result is **proxos**
		- i think idk
- **proxos assumption** - separation enforced by vmm, app dev correctly specifies routing rules
- **proxos guarantee** - confidentiality and integrity of sensitive private application data despite malicious commodity os
	- vmm has no direct interference
	- commodity os can interfere with system calls routed to it, which are not security-sensitive
	- availability not guaranteed
- **but** deploying custom os is painful (no shit)
	- building a special kernel is non-trivial
	- custom os may not even be secure itself

# Memory Shadowing
- solution to all above problems
- **shadow page table** - vmms' physical to virtual mapping of memory
- **multi-shadowing** - gives context-aware views of this memory
	- use *encryption* instead
- **memory cloaking** - encrypt pages in memory, and writes to disk

## Overshadow
- **mediate** all app interaction with os to ensure *correct cloaking* of memory
	- context identification
	- secure control transfer
	- system call adaptation
	- mapping cloaked resources
	- managing protection metadata
- **shim** - manages transitions to and from vmm via hypercall
	- *key to overshadow*
- **shim memory** - protects application, while ctc protects control registers
- **loading apps** - shim uses *loader program*
	- sets up cloaked memory with hypercall
	- loader and shim *must be trusted*
		- metadata on ctc checks for compromise
		- however do we actually check for trust?
	- propagate shims to spawned applications
- lots of os interfaces to handle
	- faults, interrupts
	- system calls - pass control to vmm, shim catches this and stores register
		- have to clear registers to prevent [[Confinement|side channels]]
		- some are easy, no side effects
		- others are complex
