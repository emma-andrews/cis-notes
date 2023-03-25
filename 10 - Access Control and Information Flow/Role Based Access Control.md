#cis/access_control/rbac #cis/access_control/mac #cis/access_control 
## Linux in 2000
- **authorization** - discretionary access control
- **discretionary access control (dac)** - allows users the ability to make policy decisions or assign security attributes
	- governs ability of subjects to access objects
- **mandatory access control (mac)** - constrains the ability of a subject or initiator to access or generally perform some sort of operation on an object or target
	- do not confuse with message authentication code! different things
	- strongly associated with [[Multilevel Security|multilevel security]]
- **security**
	- patches to the linux kernel
		- enforce different access control policy, mainly restrict root processes
		- some hardening
	- argus pitbull - limited permissions for root services
	- RSBAC - MAC enforcement and virus scanning
	- grsecurity - RBAC MAC system
		- e.g. auditing, buffer overflow prevention
	- LIDS - MAC system for root confinement
## RBAC
- assign users to roles which have a set of permissions to objects
- **role** - collection of privileges and permissions associated with some function or affiliation
- permissions are static but the user-role mapping is transient, so this is not standard discretionary access control
- **rbac** is class of access control that is neither dac or mac but may be one or either of these
	- U - *users* - subjects in the system
	- R - *roles* - different roles users may assume
	- P - *permissions* - rights that can be assumed
- **many-to-many relationship** between
	- users and roles
	- roles and permissions
- **relations** define the rbac policy
### Sessions
- **session** - when active, the user assumes a *subset* of available roles
	- **activating** a set of roles
		- user rights are the *union* of rights of the activated oles
	- **terminates** at the user's discretion
	- do not activate all roles at once because it is possible there is a subset of roles that will conflict in permissions and actions
### Constraints
- want to **constrain** the evolution of *protection states*
	- **constraints** - explicit way to do so
- **rbac constraints**
	- role assumption
	- permission-role assignment
	- user-role assignment
### Examples
- **required inclusion** - you must be acting as an employee of UF to be a professor
- **mutual exclusion** - you cannot both be the CFO of a company and its auditor
- **cardinality constraint** - you can only assume one particular role (or $n$ specified roles)