# VM Systems
- **protection domain** is extended to *operating systems* one one physical platform
	- invented for *resource utilization*
- provide potential security benefit due to **default isolation**
- vm isolation differs from os isolation

## VM Security
- enforce **isolation**
	- does not use system software to facilitate sharing between vms
- **virtualization** - technique for hiding physical characteristics of computing resources from the way in which other systems, apps, and end users interact with those resources
- **virtual machine** - single physical resource can appear to be multiple logical resources

# Architectures
- **full system simulation** - simulate operation of cpu
	- qemu
- **paravirtualization** - vm has special api, requires os changes
	- xen
- **full/native virtualization** - support from hardware not requiring os changes
	- vmware
- **application virtualization** - application api
	- jvm

# Types
- **type 1** - lowest level, bare metal vmm
- **type 2** - runs on host os
- more trust issues with type 2 than type 1
- **containers** - different from virtualization, single kernel shared by all containers
	- enforced by namespace isolation, sandboxes, etc
	- increasingly popular because of *higher density* of containers on machine compared to vms

# VM and Ref Monitor
- vms improve ability to achieve [[Reference Monitor Concept|ref monitor]] guarantees
- **mediation** - done between vm interactions
- **tamperproof** - has protection boundaries between itself and os
- **verify** - simple to do

## VAX VMM
- a1-assured vmm system, carefully crafted
- **mediation** - vm interaction
- **tamperproof** - minimal [[Enforcement|tcb]]
- **verify** - simple, code and policy assurances
- **design goal** - explicitly support reference monitor concept
	- vmm offered both *simplicity* and *application compatibility*
	- *practical goal* - mimic existing systems, don't build from scratch
	- make *layered system* for simplicity and modularity
		- ideas from [[Multics]]
		- layering could make *formal verification* possible
- **key design tasks**
	- *virtualize processor* - make all sensitive instructions privileged
	- *more [[Protection Rings|rings]]* - need a new ring for vmm
	- *i/o emulation*
	- *self-virtualizable*
- **constrained** subjects and objects
	- ensures simplicity
	- two subjects - users, vms
	- four objects - devices, volumes, virtualized resources, security kernel files
	- user (admin) can only access [[Security Kernels|security kernel]] through a *trusted path*
		- **trusted path** - secure communication path, prevents unauthorized programs from reading data of user
	- vmm exports volumes, resources to vms
		- reserves right to process security kernel files to itself
### Policy
- **[[Multilevel Security|mls]]** - control secrecy
- **[[Lattice Access Control Models|biba]]** - control integrity
	- did anything actually use this?
- **privileges**
	- exceptional accesses
	- audited
- in practice
	- vms are *single level*
	- if user at higher level wants to access lower level info, higher level vm attaces lower level vm as a read-only device
- **multiuser vm** - support because if every user needs their own vm, memory requirements would scale beyond what system was capable of providing
	- vm operates at [[Access Control|mac]] class, read access to lower classes
	- all users needing access at this level use this vm
	- attribution a potential downside
	- performance issue with layering vms

### Orange Book A1 Security
- required
	- reference monitor validation
	- security policy, targeted mls
	- auditing
	- design process
	- testing and documentation
	- formal methods for covert channel analysis
	- formal methods over design and test plans
	- trusted source distribution

### Evaluation
- **mediation** - ensure all security-sensitive operations are mediated?
	- virtualizing instructions
	- i/o emulation
	- vm level operations
	- privileges
- **mediation** - mediate all resources?
	- vmm level
- **mediation** - verify complete mediation?
	- a1-assured at vmm level
- **tamperproof** - protect vmm?
	- similar to [[Multics]], no gatekeepers but some kind of filters
	- authentication in vmm
	- protection system ops in vmm
- **tamperproof** - protect [[Enforcement|tcb]]?
	- all trusted code at [[Protection Rings|ring 0]]
	- trusted path from vms for admins
- **verification** - verify code?
	- a1-assured at vmm level
- **verification** - verify policy?
	- mls and biba express goals and policy
	- privileges are ad hoc
#### Verification Challenges
- code complexity
	- vax vmm is over 11k lines of assembly
- device support - code needs to be evaluated and assured when new device is supported
- covert channels
	- mainly free from storage channels but timing is a problem
	- solution of *fuzzy time*, add uncertainty to timing channels
		- slow and defeated soon after

### Challenges
- despite **a1 assurance** still several challenges
- device driver management
- no network
- amount of assembler code
- covert channel countermeasures
- implications of privilege
- but did give some interesting mechanisms
	- trusted path administration, architecture of vmm
	- virtualization for security
	- 