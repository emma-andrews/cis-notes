#cis/access_control/access_matrix #cis/protection #cis/reference_monitor #cis/enforcement/tcb 
## OS Protection
- os expects user-space vulnerabilities
	- marks those processes as *untrusted*
- os **policies** will protect the system from harm
	- only the os and a few processes need to be trusted
	- ideal thinking
	- specifying the [[Access Control|access control]] policy is not enough
### Satisfying Reference Monitor
- even with fixing the access control policy, these systems do *not satisfy* the [[Reference Monitor Concept|reference monitor concept]]
#### Complete Mediation
- **mediation - does interface mediate correctly?**
	- no, several operations impact security that are ignored
- **mediation - on all resources?**
	- for unix, no because there is no network
	- for windows, it is possible
- **mediation - verifiably?**
	- lol no
#### Tamperproof
- **tamperproof - is reference monitor protected?**
	- the os is *not protected*
		- rootkits can attack the os
	- kernel modules, trusted processes, and extensible functions need to be protected
	- policy can be modified by *untrusted processes*
		- discretionary policy
- **tamperproof - is system [[Enforcement|TCB]] protected?**
	- not able to define it
	- have to protect all root and setuid processes and the ones that they depend on
	- also have to protect anything an *admin runs* as root
#### Verification
- **verifiable - is TCB code base correct?**
	- no
- **verifiable - does the [[Access Control|protection system]] enforce the system's [[System Security Goals|security goals]]?**
	- lol what are goals?