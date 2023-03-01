#trustworthy_computing
## Overview
- computer is running a process
- makes a request to the computer asking for some secret data tot process
	- provides an input the computer can depend on
- **problem** - how do we know it is executing correctly?
- you could blindly trust that the computing device is working properly
	- or try to establish some sort of **trust** in the system
## Approaches
- **top-down approach** - done through *certificate authorities (CAs)*
	- already have entire trust infrastructure in place with CAs for secure communication
	- but can the CAs *themselves* help us solve the problem?
- **bottom-up approach** - root of trust
	- determine what parts of the system need to be trusted
	- determine how to establish trust
	- determine where the root of trust needs to start
- options
	- nothing
	- *proof by authority* - certificates
		- know who, but not what
	- *constrain* the system - secure boot