#access_control #access_control/unix #access_control/windows #access_control/access_matrix 
## UNIX Access Control
- all objects are files
- classical protection system
	- limited [[cis/03 - Security Principles/Access Control|access matrix]]
	- discretionary protection state operations
- practical model for end users
	- involves some policy specification
- **mode bits** - first column in `ls -al`
	- defines read, write, and execute for each user group
	- extra flag if file is directory
- example access matrix
	- suppose private key file for subject $J$ is object $O_1$
		- only $J$ can read
	- suppose public key file for $J$ is object $O_2$
		- all can read but only $J$ can modify
	- suppose all can read and write from object $O_3$
	- resulting access matrix
	- 