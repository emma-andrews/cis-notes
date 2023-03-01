note: not in midterm overview of concepts, may not need to know
## Mitigating Code Execution
- developed for Windows
- preventing arbitrary **code generation**
	- **code integrity guard** - images must be signed and loaded from valid places
	- **arbitrary code guard** - prevent dynamic code generation, modification, and execution
- preventing **control-flow hijacking**
	- **control flow guard** - enforce control flow integrity on *indirect* function calls
	- unnamed technology - enforce control flow integrity on function returns
- **Hypervisor Enforced Code Integrity (HVCI)** - leverages virtualization page tables managed by VLT1 (secure mode) to eliminate [[Buffer Overflow|W^X]] memory in VTL0 kernel mode