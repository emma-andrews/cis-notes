#cis/trustworthy_computing #cis/trustworthy_computing/tpm #cis/trustworthy_computing/late_launch #cis/trustworthy_computing/secure_boot 
## Secure Kernel Init
- amd skinit instruction - allows for DRTM (dynamic root of trust for measurement)
- **atomic execution**
	- sets cpu to init
	- disables interrupts
	- disables dma over 64kb secure loader block (slb) memory pages
	- disables debugging
	- resets dynamic pcrs in tpm to 0
- verifier receives [[Trustworthy Computing|attestation]] after skinit is run
	- knows skinit was used and that software [[Enforcement|tcb]] includes only the slb and what was executed
	- extending pcr 17 can only be done with skinit oepration
		- can be verified which slb was loaded

## TPM
- **secure boot**
	- enabled by txt interacting with [[Trustworthy Computing|tpm]]
		- reset through trusted software, txt gains control over the entire chip
		- guest vm can be [[Trustworthy Computing|secure booted]] with tpm used for attestation
	- *trust boundary* - processor package and some trusted software
		- secrets held on chip
		- software can be attacked

### TCB
- **tpm** - trusted hardware
- **ima** - trusted boot
- **work on virtualization** - isolation
- additional tcb is very large
	- hypervisor alone at least 50k loc
	- need a way to minimize it, if at all possible

## TPM 1.2
- now **dynamic root of trust of measurement**
- included dynamic pcrs (17-23) that can be reset to zero without rebooting the machine
	- pcr 17 only resets with [[Secure Processors|skinit]] command
	- skinit takes slb as input
		- resets dynamic pcrs to 0
		- disables dma and interrupts
		- extends [[Integrity Measurement|measurement]] to pcr 17
		- executes start of slb