#multics #reference_monitor 
## Overview
- multiprocessing system that developed many major concepts in operating systems, including security
- began in 1965, last deployment in 2000
- **achievements**
	- virtual memory and memory segmentation
	- hierarchical file system
		- including symlinks and removable devices
	- shared memory symmetric multiprocessing
	- dynamic linking
	- high availability utility computing platform
	- security in the design phase
## Security Goals
- security goals evolved as system design evolved
- first system design to even consider security goals
- **secrecy** - prevent leakage even if running untrusted code
	- implemented as *multilevel security*
- **integrity** - prevent unauthorized modification
	- *layers of trust*
	- implemented as *rings of protection*
- **comprehensive control** - enforce at the lowest level
- **[[Reference Monitor Concept|reference monitor]]** - mediated segment crossing and all ring crossing activity
	- *gates*
- **resulting system** - considered a high point in secure system design
### Multilevel Security
- **multilevel security system** - tags all objects and subjects with security tags
	- classifies them in terms of sensitivity and access level
- formulate access policy based on these levels
- can add other dimensions called **categories** that horizontally partition the rights space
	- similar to [[General Security Concepts|roles]]