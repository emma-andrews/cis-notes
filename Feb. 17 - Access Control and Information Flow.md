## access control
- decisions based on requests and policy
- access control - can I perform a specific action on a specific object
- policy allows us to enforce these ideas
	- availability, integrity, confidentiality
	- want simple mechanisms to be easy to maintain and use
- access system itself can change
	- safety problem will be on exam :)
- policy changes can lead to unsafe states
- can only read data if higher privilege read down
- star property - can write only to a security level higher than myself write up
### biba
- represents integrity dual of BLP
- like tibetan monks
	- monk can write book the can be read by commoners, but not read by high priest
	- monk may read book by high priest, but not one written by a commoner
- prevent low integrity info flows to higher integrity processes
- categories and compartments for separation between levels
- safety is implicity in the model
### denning's lattice model
- formalizes information flow models
- showsthat information flow model instances form a lattice
- biba and blp are simplest examples
- SC is finite
- SC has a lower bound
- join is a least upper bound
	- defined for any pair in SC
- explicit flow - direct transfer to $b$ from $a$ 
- implicit flow - where value of $b$ may depend on value of $a$ indirectly
- 