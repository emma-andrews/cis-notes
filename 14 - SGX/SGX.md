# Attack Surface
## Today
- large code base constitutes the part that can be **exploited**
	- application codes, os codes, [[Integrity Measurement|vm manager]] codes
- millions of lines of codes need to be inspected for exploitable bugs, etc.
- [[Hardware-Enforced Security]]

## With SGX
- known **SGX Enclaves**
- application gains ability to **defend its own secrets**
- smallest attack surface for both application and processor
- malware that subverts os/vm manager, [[Trustworthy Computing|bios]], drivers, etc. cannot steal app secrets

# SGX Enclaves
## Protection vs. Memory Snooping Attacks
- **security perimeter** - cpu package boundary
- data and code is **unencrypted inside** cpu package
- data and code is **encrypted/integrity checked outside** cpu package
- external memory reads and bus snoops see only *encrypted data*
- sgx **does not protect** against *leakage* via access patterns to external memory
- **memory encryption engine** - hardware component implemented with sgx
	- provides *integrity and confidentiality* protection to off-die ram
		- aka the *seized region*
	- two-level merkle tree for integrity
		- root of tree stored on-die
	- *encryption* through tweaked aes-ctr
		- side note, why ctr instead of gcm?

## Life Cycle
- for full example with pics see slides 7 - 19
1. physical address space only
2. **bios setup** - establish system memory and *enclave page cache (epc)* in physical address space
	1. epc memory starts as all invalid entries
3. **enclave creation** - created in virtual address space with specified range
	1. epcm contains information on the enclave - valid, secs, range
4. plaintext code and data is stored in system memory in physical address space
	1. in virtual address space, this is placed within the range of the enclave
5. plaintext is added to epc, enclave sees this and initiates a copy page command
6. enclave's code and data is updated to point to the entry in epc
	1. plaintext code and data is removed from system memory now that it exists in epc
7. extend adds more code and data to the epc and enclave virtual address space
8. initialization
9. enter enclave to retrieve information about the code and data
10. retrieves plaintext from epc
11. exit enclave
12. enclave destruction with remove, makes all entries in epc invalid

# EPC Pages and Operations
## Page Swapping
- epc memory is *set by bios* and limited from size perspective
- need to be able to **remove** an epc page, place into **unprotected memory**, and **restore** it later
- page must maintain **same security** properties when restored
	- e.g. confidentiality, anti-replay, integrity
- epc paging instructions provide ability to **encrypt page** and produce *metadata* needed to meet requirements
- enclave page must be **evicted** only after all cached translations to that page have been evicted from all logical processors
- content is *swapped* on 4KB page basis
- each 4kb **epc page** produces
	- 4kb of *encrypted content*
	- 128b metadata (pcmd)

## Paging Operations
- **page eviction** from epc
	- page assigned unique version number which is recorded in a new type of epc page - **version array (va)**
	- write out to sys memory encrypted page, metadata, and epcm info
- **page reloading**
	- processor decrypts and integrity checks page, using *crypto metadata*
	- processor verifies that version is same version that was last written out

## Paging Instructions
- **EPA** - allocates 4kb page in epc for holding an array of page versions *va* for anti-replay protection
	- *va* contains versions of paged out enclave pages, size of each version slot is 64 bits
- **EBLOCK** - blocks a page from being accessed in prep for swapping it out
	- any future accesses by owner enclave to *blocked page* result in # pf (?)
	- returns indications that page was previously blocked
- **ETRACK** - sets a tracking mechanism to verify that all tlb entries for blocked page has been *flushed*
- **EWB** - *securely evicts* 4kb page from epc along with its page info
	- assign unique version value for page and store in va page
	- encrypt epc page, create mac (message auth code, not mandatory access control) over encrypted page, version counter, and metadata
		- write out to external memory
	- enclave page must first be *prepared for eviction*
		- blocked and no tlb entry refer to that page
- **ELDU/B** - *securely loads* page back from memory into epc into an *unblocked or blocked state*
	- verify mac on metadata, version counter from specific va entry, and encrypted enclave page content
	- if verification succeeds, decrypt enclave page content into epc page allocated by system memory and clear va entry

# SGX Enclave Measurement - MRENCLAVE
- sgx generates crypto log of **all build activities** when *building an enclave*
	- contains code, data, stack, and heap
	- location of each page within the enclave
	- security flags used
- **MRENCLAVE** - "enclave identity", 256 bit digest of log
	- represents enclave's *software [[Enforcement|tcb]]*

# SGX Attestation
- sgx provides both **[[Trustworthy Computing|local and remote attestation]]**
- **local attestation** - allows one enclave to attest its *thread control block (tcb)* to another enclave on the same platform
	- note the new tcb acronym def.... :)))))
- **remote attestation** - allows one enclave to attest its tcb to *another entity outside* of the platform

## Local Attestation
- application enclave **proves its identity** to another target enclave cia *EREPORT* instruction
- app enclave calls ereport instruction to **generate report** structure for a desired target enclave
	- report contains calling enclave's attributes, measurements, and user supplied data
- report structure is secured using the **report key** of the *target enclave*
- **egetkey** is used by target enclave to retrieve report key
- target enclave then **verifies report structure** using software

## Remote Attestation
- two keys burned into cpu during *manufacturing*
	- **fused seal key** and **provisioning key**
- **fused seal key** - used as processor's *secret*
- **provisioning key** - serves as a *proof* for remote platform
- **attestation key** - issued by remote platform, encrypted and stored for future use
- ![[sgx1.png]]
- **quoting enclave** - performs *local verification* of application enclave
	- upon successful local attestation, decrypts *attestation key* and signs report with it
	- remote party verifies signature
- ![[sgx2.png]]
