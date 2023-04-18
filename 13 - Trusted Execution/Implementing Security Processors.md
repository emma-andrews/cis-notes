#cis/trustworthy_computing [[Secure Processors]]
## External Modules
- outside the main system on chip (soc)
- **pros**
	- physical security
	- separate design/manufacture flow
	- formal certification of manufacture process
- **cons**
	- cannot work on a soc
	- compromise of area
	- power efficiency
	- performance
	- no direct access to user interfaces on device

## Embedded Module
- inside the soc
	- e.g. trustzone
- **pros**
	- much cheaper than external module
- **cons**
	- restricted perimeter
	- physically separate security processor - less powerful
	- consumes area and power
	- communication must be coherent

## Software Virtualization
- hypervisor runs privileged mode
- **pros**
	- any processor with mmu can do this
	- applications can be ported to secure environment on hypervisor
- **cons**
	- isolation restricted to processor implementing hypervisor
	- dma and gpu with own bus master can bypass
	- no protection against debug attacks

## ARM TrustZone
- chip virtually partitioned into normal and secure world by multiplexing page tables
	- separate base registers for normal and secure world
- system bus has bits set to show secure vs normal mode
- trigger secure world access with *secure monitor call (smc)* instruction
- **secure world** - unrestricted access to normal world memory
- **normal world** - conventional stack calls to secure world through smc and some hardware exceptions
- no official attestation or secure boot processes
- secure world runs its own separate os
- monitor mode used to context switch between worlds

## Trusted Execution Environment (TEE)
- lightweight os that runs in the secure world
- isolated execution separated by hardware and software
- can run secure boot operations, anti-cloning routines, smartphone subsidy lock, kill switches
	- stop secure data (android keystore supports storing to tee)