#cis/trustworthy_computing #cis/security_kernels/scomp #cis/trustworthy_computing/tpm 
## Timeline
- 1980s - [[SCOMP]]
- 1990s - visa security module, ibm 4758
- 2000s - present - [[Trustworthy Computing|tpm]]
- 2000s - present - arm trustzone
- 2010s - present - intel sgx
- 2010s - present - intel management engine, amd platform security platform

## TPM vs 4758
- **ibm 4758** - standalone device
	- tamper resistance
	- locks down *keys* primarily
	- resistant to cold boot, EM, and power analysis attacks
- **tpm** - commodity 4758
	- far less tamperproofing
	- passive device
	- hashing and signing
	- trusts the bios

## TPM Limitations
- **static root of trust** for measurement, reboot
- **coarse-grained** - measures the entire system
	- requires hundreds of [[Integrity Measurement|integrity measurements]] just to boot
	- each *host* is different
		- different versions, drivers, apps, etc
	- *tcb* includes entire system
- **integrity measurements** are done at *load-time*, not run-time
	- [[Vulnerabilities|tocttou]] problem
	- cannot detect any *dynamic attacks*
	- no guarantee of execution

## Late Launch Extensions
- amd secure virtual machine (svm) and intel trusted execution technology (txt)
- **similarities**
	- hardware support for *virtualization*
	- [[Integrity Measurement|late launch]] of a measured block of code
- **differences**
	- amd provides measured environment only
	- intel adds authenticated code capabilities
		- system's chipset contains a public key to *verify* signed code
