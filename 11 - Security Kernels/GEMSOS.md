#cis/security_kernels 
## Overview
- **gemini operating system (gemsos)** - similar system goals to [[SCOMP|scomp]]
	- built for the "new" x86 processor
		- e.g. now have support for ethernet
	- consists of a [[Security Kernels|security kernel]], gate library, and a layer of trusted software that is dependent on the deployed system
	- uses a software-based ring mech to simulate 8 [[Protection Rings|protection rings]]
		- still have processor rings
	- still around unlike scomp
	- *fine-grained* kernel design
	- eventual unix/posix emulation
		- scomp could not emulate
	- file system inside security kernel
		- kernel and trusted software depends on data layout in files
## Security Goals
- implement [[Reference Monitor Concept|reference monitor]] functionality
- incorporate security policy model
- uses [[Lattice Access Control Models|blp]] for secrecy
- supports biba integrity
- hardware-enforced rings of protection
- mediates on segments
- i/o devices viewed as system processes