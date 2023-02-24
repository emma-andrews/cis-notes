## lomac
- without lomac trojan horse can use buffer overflow attack to take control over a subject
	- has its permissions and can alter other objects that the subject has permissions for
## chinese wall model
- example consulting business
- consultant is authorized 
- company dataset - set of objects that may belong to a company
- conflict of interest class - datasets of companies in conflict
	- each object has only one
- cw-simple security property - read iff
	- if subject $s$ reads object $o$ belonging to dataset $CD$, subject can never read another object where that object's dataset is a member of a conflict of interest class
	- objects canbe sanitized
- writer can only access objects in one dataset
- 