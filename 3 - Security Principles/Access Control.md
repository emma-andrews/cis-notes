#access_control/access_matrix #access_control/protection_system 
## Authorization and Authentication
- authentication - verifying someone or something's identity
- authorization - deciding whether a subject can perform a requested operation on an object
- authentication is performed for authorization
### Protection System
- protection system - manages the access control policy for a system
	- a [[System Security Goals|security goal]]
	- represents protection state and its operations
	- describes what operations each subject can perform on each object
- access matrix - a way to represent policy
	- frequently used mechanism for describing policy
	- columns represent set of objects $O$
	- rows represent set of subjects $S$ within the access matrix $A$
	- find the appropriate entry to determine if a specific subject has the right to access a specific object
	- succinct descriptor for $\theta(|S||O|)$ entries
example
|           | File 1           | File 2    | Process 1                 | Process 2                 |
| --------- | ---------------- | --------- | ------------------------- | ------------------------- |
| Process 1 | read, write, own | read      | read, write, execute, own | write                     |
| Process 2 | append           | read, own | read                      | read, write, execute, own |
- $S=$ $\{$Process1, Process2$\}$
- $O=$ $\{$File1, File2, Process1, Process2$\}$
- $R=$ $\{$read, write, execute, own, append$\}$
- 