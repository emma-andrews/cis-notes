- note - non-cumulative but this stuff does build on midterm's content so
- similar format to midterm, same rules (cribsheet etc)
- foundational concepts
	- why did we talk about [[Reference Monitor Concept]]?
		- best thing we know
		- not the only model but def the best since 70s
		- most comprehensive
		- not everything is captured
	- access matrix for [[Access Control]]
- [[Trustworthy Computing]]
	- roots of trust for management
		- bottom up vs top down
		- attestation - top down, auth boot, have to wait for it to boot up entirely before you know its secure
			- both local and remote
	- trusted boot processes
		- secure and auth boot
		- load time [[Integrity Measurement]]
			- sign collection of hashes for auth boot
			- not protected vs mem attacks, runtime issues not captured
	- attestation
	- [[Secure Processors]]
		- [[Late Launch and TPM]], [[Implementing Security Processors]]
		- issues with trusted envs
- [[SGX]]
	- tpm tells you that you booted in high integrity state
	- hardware enclave program running on is trusted even if things outside enclave are compromised
	- local and remote attestation
	- [[SGX Vulnerabilities]] - side channels
		- designers thought it would be a minor issue
		- lol
	- alt archs
	- dont need to know super low level details
	- general idea of how the enclave is created
- [[Virtual Machine Basics]]
	- isolation for vm vs os [[Modern Virtual Machine Systems]]
	- type 1 and type 2 hypervisors
		- different attack surface and threat models
		- differences and pros/cons
	- containers vs vms
	- vax vmm
		- ref monitor guarantees
		- assurance levels
		- challenges to adoption
	- privilege separation in vms
		- reqs of app dev
		- separation in vms vs os
		- more complicated in vms but tradeoff worth it overall
- [[Microkernels]]
	- microkernel vs monolithic kernel
		- microkernels not used because slower, everything done in user space so you need a shit ton of syscalls to kernel
			- context switch entire user space state
- [[Android]]
	- security service for mobile devices
	- diffs between android and linux
	- where can ref monitor guarantees be enforced within android and how
	- access enforcement models
		- dac, mac selinux, capabilities, sandboxing - general linux concepts
		- permissions, binder - android specific things
			- binder is secure middleware for android
	- secure boot and tees
- peripheral security
	- hierarchy of attacks vs usb devices
	- susceptible to social engineering?
		- "just plug it in what could go wrong"
	- side channel data exfiltration
	- auth done in usb and issues with it
- microarch security
	- meltdown and spectre
		- possible because of speculative execution
		- take advantage and get info from branch, use it to do bad things
		- leverage lowest level of computing, hypervisor etc not gonna help
	- prime and probe attacks
	- cache side channels
	- effects on sgx with foreshadow
- cloud security
	- difference between cloud and other computing models
	- why didnt multics figure everything out 
	- what is security model in cloud computing
		- unique challenges
	- multitenancy and issues with it
		- covert channels