## Overview
- **type enforcement** - mandatory model that provides both *secrecy* and *integrity*
	- usually only have one but not the other
- considers
	- **domain** of execution for a subject
	- type of the object the subject tries to access
	- domain defines a **set of privileges**
		- similar to [[Protection Rings|protection rings]]
		- *but*, relationship between domains is not hierarchical
- **domain definition table** - subject has domain associated, object has type associated
	- matrix is *immutable*

|           | Object 1 | Object 2 |
| --------- | -------- | -------- |
| Subject 1 | R        | RW       |
| Subject 2 |          | W        |
| Subject 3 | RW       | RW         |
- **domain interaction table** - relates domains to each other and their access policies
	- matrix is *immutable*

|          | Domain 1        | Domain 2        |
| -------- | --------------- | --------------- |
| Domain 1 | create, destroy | signal          |
| Domain 2 | create          | create, destroy |
| Domain 3 |                 | signal, create, destroy                |
- **standard type enforcement** gets very complex because the tables have to express *all transitions*
	- does not really match how systems actually work
- **domain type enforcement (dte)** - designed to be higher level
	- uses *implicit* structures like directories to represent security attributes