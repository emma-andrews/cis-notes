#cis/confinement
## Why do we need confinement?
- **protection** - ensure another process cannot access or modify any of my data except those that I have *explicitly* granted access to
- how can an external service be harmful?
	- does not perform as advertised
		- e.g. Trojan horse
	- transmits data to its owner about the data it has access to
- **confinement problem** - assure that any program, if *confined*, will be unable to *leak data*
- **data leak sources**
	- external service reads data from memory and returns that data to the malicious owner
	- external service writes into a file and owner collects data from there
	- service creates temp file and grants owner access to it
		- read out the data before the file is removed
	- service uses IPC to send data to the file owner
	- information is encoded into a legitimate file that the owner can access
		- *covert encoding*
	- covert channel through manipulation of mutex locks
		- order of file execution can be a signal
	- change the ratio of computing to i/o or rate of paging
		- performance of the system then becomes a signal to the adversary
			- *covert timing channel*
## Making Confinement
- **total isolation** - confined program cannot make calls to another program
	- *impractical* - even benign system calls would be disallowed
- **transitivity** - confine a program, if it calls another program that is not trusted then that program must also be confined
	- hard to do - supervisor must be trustworthy and writing one is hard
	- many information leaks can be done in a very subtle way
	- enumerate and eliminate potential sources of data leaks
### Types of Leaky Channels
- **storage channel** - write to a service such that an unconfirmed program can read the data, either shortly after writing or at a later time
- **covert channel** - a side channel that is not meant to exist in the first place as a way of *transferring information*
	- need to ensure that these are mitigated