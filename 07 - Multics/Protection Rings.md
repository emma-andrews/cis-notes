 #cis/multics #cis/multics/protection_ring
## Overview
- **integrity** for [[Multics|multics]]
- successively less-privileged domains
- multics had 64 rings in theory, 8 in practice
- in modern processors, 4 rings but only 2 are in general use
	- **ring 0** - kernel mode
	- **ring 3** - user mode
### Ring Brackets
- kernel resides in **ring 0**
- process resides in **ring $r$**
	- access is based on *current ring*
- process accesses data (segment)
	- each data segment has an **access bracket** $(a_1,a_2)$
	- **access bracket relationship** - $a_1 \leq a_2$
	- describes read and write access to the *current ring*
- given the **current ring $r$**
	- *access permitted* - $r \leq a_1$
	- *read and execute permitted, write denied* - $a_{1}\leq r\leq a_{2}$
	- *all access denied* - $a_{2}\leq r$
### Process Invocation
- program cannot call code of **higher privilege** *directly*
- **gate** - mediates access
	- *special memory address* where lower-privilege code can call higher-privilege functions or code
	- enables the os to control where applications call *privileged functionality*
	- in modern oses, equivalent to system calls
#### Brackets
- different procedure segments with
	- **call brackets** $(c_{1},c_{2})$ where $c_{1}\leq c_{2}$
	- **access brackets** $(a_{1},a_{2})$
- **rights** to execute in a procedure segment
	- $r\leq a_{1}$ - access permitted with a *ring-crossing fault*
		- ring-crossing fault changes the procedure's ring, increases from $r$ to $a_{1}$
	- $a_{1}\leq r\leq a_{2}=c_{1}$ - access permitted and *no fault*
		- $r$ keeps the same ring number
	- $a_{2}<r\leq c_{2}$ - access permitted through a *valid gate*
		- arguments are checked by the gate, ring number is decreased
	- $c_{2}<r$ - access *denied*
- **examples**
	- process in ring 3 accesses data segment
		- access bracket - (2, 4)
		- operations - @todo
	- process in ring 5 accesses same data segment
		- operations - @todo
	- process in ring 5 accesses procedure segment
		- access bracket (2, 4) and call bracket (4, 6)
		- @todo
		- can call be made?
		- determining new ring
		- new proc segment access above data segment
#### Brackets as MPS
- **protection state**
	- rings are fixed in hierarchy
	- protection state can be modified by the owner
- **labeling state**
	- ring determined statically
	- default can be changed as above
- **transition state** - through call brackets, guarded by gates