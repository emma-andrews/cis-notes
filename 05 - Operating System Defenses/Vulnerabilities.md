#vulnerabilities/morris #vulnerabilities/confused_deputy #vulnerabilities/overflow #vulnerabilities/tocttou 
## Vulnerabilities in Commodity OSes
- **privilege escalation** - function run by one subject that enables an attacker to gain *unauthorized privileges*
- two views on vulnerabilities
	- subjects can do something for attacker
	- attacker can take over a process
- attack can use the subject's privileges
	- can use these to compromise an even more privileged subject
### Morris Worm
- 99 line program that disabled the internet
- buffer overflow on fingerd program, which has root access
- cracks user passwords on the compromised host to try on other hosts
- used liberal system configurations to identify hosts that accept logins without a password from the compromised host
- scanned local interfaces for network information
- hid its activities
	- renamed its process to sh
	- prevented accurate cores
	- forked itself again
- *significance* - it would re-attack an already infected system after 7 scans
### Confused Deputy
- server process has the privileges necessary to service requests from all its clients
- client can [[UNIX Access Control|trick]] the server into using the server's permissions for the client
- server access an object whose name is supplied by a client
### Unknown Origin
- server may use a file under the control of a client
- client may provide file by name or predict the name of a file that the server may create or use
- this file provides guidance that the server *trusts*
- this enables the client to **control** how the server runs
### /tmp
- create a file in shared space /tmp
- give it a filename used by a higher authority service
- service has to have permission to the file
- need to create a file that does not already exist
- call `creat(pathname, mode)` with `O_EXCL` flag
	- flag causes an error if the file already exists
	- without the flag, then the file can be shared with the higher authority process
### setuid
- setuid is a special mode bit
- **execute file** - resulting process is effective and has filesystem user id or group id of the file owner
- enables user to **escalate privilege** to execute a trusted service
- **however**, user defines the *execution environment*
	- e.g. environment variables, input arguments, open descriptors
- service must *protect itself* or user can gain root access
### chroot
- create a **domain** in which a process is **confined**
	- process can only read and write within file system subtree
	- applies to all descendent processes
	- can carry file descriptors in *chroot jail*
- chroot can trick its own system
	- define a new passwd file
	- run as superuser, giving root access
	- use `mknod` to create device file to access *physical memory*
- need great care in setting up
	- never run chroot process as root
	- must not be able to get root privileges
	- no control by chrooted process of contents in jail
	- careful about descriptors, open sockets, IPC that may be available
- chroot as **MPS**
	- protection state - confine to namespace, but can change within that namespace
	- labeling state - could create root objects
	- transition state - no rules governing transitions
	- **N O**
### Other Attacks
- **buffer or heap overflow** - can inject code into the server
- **path or input libraries** - can inject code into the server
- **TOCTTOU** - time of check to time of use - cause an authorization to pass due to race conditions
- **integer overflow** - causes a different *control path*