#cis/trustworthy_computing #cis/trustworthy_computing/tpm #cis/trustworthy_computing/late_launch #cis/trustworthy_computing/flicker #cis/enforcement/tcb 

## Minimizing TCB
- done with **[[Integrity Measurement|late launch]]**
- **goal** - move execution of sensitive code to a small protected area

## Flicker Overview
- **goal** - reset the system configuration using drtm
- allows for **fine-grained attestation**
	- e.g. server code handling password executes in isolation, attestation about the operation
	- pcr extended after every run to support *remote-party verification*
- claimed that loc in tcb was 50k for skinit and xen, and 250 for skinit and flicker

## Flicker Components
### PAL Applications
- run in effectively initialized [[Trustworthy Computing|attested]] environments
- simple c code
- links against object file containing flicker's core functionality
- starts and ends within the slb core
- cleans up and resumes os when done
- runs in [[Protection Rings|ring 3]] to restrict accessible memory

### SLB
- total size 64 bytes
	- if pal needs more it needs to point to the next [[Late Launch and TPM|slb]]
- **global descriptor table (gdt)** stores untrusted os
- no heaps, memory management, or virtual memory support

### Multiple Sessions
- saving states between sessions can be useful
- but, you are resetting the [[Trustworthy Computing|tpm]] with drtm whenever you execute a pal
- **solution** - *tpm sealed storage*
	- tpm generates attestation identity key (aik) pair from its endorsement key (ek)
	- aik private key encrypted by a storage root key (srk)
	- srk then locked to the tpm
		- can be used to record current state of the tpm
			- state of pcrs
		- encrypts a blob that can be unsealed later

### Remote Verification
- flicker attestation based on drtm
	- convince a remote party that a particular pal was executed
	- after running a pal and terminating flicker, invoke a tpm quote
	- extend dynamic pcr 17 with measurements of pal input and output parameters
- create secure channel to remote entity by combining flicker sessions
	- use public key in a pal to create the secure channel

## Performance Overhead
- **highest overhead** - comes from tpm quote
- overhead decreases across multiple sessions