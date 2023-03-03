#trustworthy_computing
## Overview
- computer is running a process
- makes a request to the computer asking for some secret data tot process
	- provides an input the computer can depend on
- **problem** - how do we know it is executing correctly?
- you could blindly trust that the computing device is working properly
	- or try to establish some sort of **trust** in the system
## Approaches
- **top-down approach** - done through *certificate authorities (CAs)*
	- already have entire trust infrastructure in place with CAs for secure communication
	- but can the CAs *themselves* help us solve the problem?
- **bottom-up approach** - root of trust
	- determine what parts of the system need to be trusted
	- determine how to establish trust
	- determine where the root of trust needs to start
- options
	- nothing
	- *proof by authority* - certificates
		- know who, but not what
	- *constrain* the system - secure boot
		- effective, limiting, but proof is implied
	- inspect the *runtime state* - authenticated boot
		- flexible, attestable, but difficult to prove semantics
## Secure Boot
- check *each stage* in the boot process
	- is the code that you are going to load acceptable?
	- if not, **terminate** the boot process
- must establish a **root of trust**
	- a component trusted to speak for the correctness of others
	- assumed to be correct because errors are undetectable
	- bios>expansion rom>bootloader>initial ram disk>os>application code
### AEGIS
- **architecture**
	- rom checks bios
	- bios checks expansion roms and boot blocks
	- bootloader checks os
- enforces **strict sequencing**
- ibm pc architecture is a star-like boot
## Authenticated Boot
- **secure boot** enforces requirements and uses special hardware to ensure a specific system is booted
	- *implicit verification* - we know its good because it is
- **authenticated boot** - measure each stage and have a *verifier* authenticate the correctness of the stage
	- verifier must know how to verify correctness
	- behavior is uncertain until verification
	- can you verify yourself?
### Remote Attestation
- **attestation** enables verifier to establish trust in an untrusted device
- tells verifier what code is executing on the device
- if intended code is executing on untrusted device, verifier can trust its operation
### Outbound Authentication (OA)
- goal to try and combine secure boot and attestation
	- relying party P wants to prove that only entity E holds key K
	- E is high integrity despite depending on several integrity-relevant events
- defines a **precise logic** for reasoning about such properties
- very limited system, only has one application
- **root of trust** in device rom
- **tamperproof** enclosure
- guardian processor maintains lock state during manufacturing
- a relying party needs to conclude that a particular key pair really belongs to a particular software entity within a particular untampered coprocessor
- secure coprocessor contains various hardware protections to isolated memory, manage keys, and perform updates
#### Configurations and Epochs
- **configuration** - a layer n configuration is the maximal period in which that layer is runnable, with an unchanging software environment in layers 1,...,n
- **epoch** - a layer n epoch is the maximal period in which the layer can run and accumulate state
- software runs for an epoch, but any change to the software results in a new configuration
	- change in software is *integrity-relevant event*
	- events constrained by hardware
#### Execution History
- an entity E wishes to prove it "owns" key K by presenting a chain (E,K,H) of certificates
	- H shows the chain of entities that verify E's K before the current run R
	- chain speaks for the correctness of K, which the **relying party** P should trust
- **implications**
	- only these entities should have access to the secrets and configuration of E
	- hardware limits the set of integrity-relevant operations that can affect E
	- general purpose systems allow more operations
#### Dependency (Integrity)
- each entity E has a dependency set D(E)
	- an entity E depends on entities that have read/write access to its secrets and write access to its code
	- in general purpose systems, it is primarily dependent on untrusted data that leads to integrity problems
- **trustset(P)** - set of entities that P trusted
	- should be a superset of the measure dependencies
- **implications**
	- dependency must be comprehensively defined
		- initialization, code load, subsequent reads
#### Validation
- P wants to verify E depends only on its trustset
	- $validate(P,chain(E,K,H)) \implies D_{R}(E) \subseteq trustset(P)$
- a run R, prefixed by H, defines an entity's $D_{R}(E)$
	- hardware protections imply $D_{R}(E)$
	- if $D_{R}(E)$ is in P's trust set, then the chain is valid
	- $D_{R}(E) \subseteq trustset(P) \implies validate(P,chain(E,K,H))$
- oa requires an entity E's dependencies satisfy the trust set of P to validate that E owns K
- **implications**
	- difficult for P to verify all entities, integrity relevant events, and dependencies
	- enforcement simplifies the protocol
		- oa makes this seem easy, but it has a lot of constraints to simplify the problem
### Is this Trusted Computing?
- **trusted platform module (tpm)** - brought authenticated boot into the main stream
	- **primitives**
		- measurement, cryptography, key generation, prng
		- controlled by physical presence of the machine
		- bios is *core root of trust for measurement (crtm)*
	- tamper-resistant secure microcontroller
	- manages cryptographic keys and functionality it uses to support security relevant operations
	- measures the code loaded by the system
	- measurements are hashes of loaded code (platform configuration registers - pcrs)
- only discussed how to measure early boot phases and general userspace measurements
- use virtualization to split system
- measure the "trusted" part to prove its integrity before responding
	- could use tpm to do so