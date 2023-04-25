- see end of [[Android]] for security overview

# Attack Surface
- attackers have many avenues to attack **mobile platforms**
	- *physical* - usb
	- *wireless* - cellular, wifi, nfc, bluetooth
	- *local* - malicious apps and websites
- android is **most vulnerable os**
	- and its not even close to the os in second place lol
- **security mechanisms** - divided between access control and other types
	- *access control*
		- linux dac
		- linux capabilities
		- selinux/seandroid (mac)
	- *other*
		- seccomp
		- android middleware
- **finding attack surfaces**
	- combine whole sys security model into *unified graph*
	- query graph to find attack paths

## BigMAC
- [paper](https://www.usenix.org/conference/usenixsecurity20/presentation/hernandez)
- *fine grained* policy analysis of **android firmware**
- collected 2k firmware images
- absolutely no standard firmware distribution format (of course, why make things easy)
- extracted **contexts** and **sepolicy** from the images
- constructed **attack graph** using this info
- **graph analysis** - determine what objects processes can access based on sys policy
	- access is based on permissions, so uid, gid, label, capabilities
	- but there is no processes in static firmware, so how do you recover them
	- *solution - emulating android boot*
		- androids boot process is well specified by platform
		- so just load the firmware and see what changes to filesystem happen during boot time
- **querying graph** - need to find attack paths in a graph of over 1 million edges
	- use prolog
		- :^))))))))
	- all you need to know about prolog is that prolog queries act like queries to a database, dont look at the code prolog is awful
- **takeaways**
	- firmware extraction and format diversity poses significant challenge to android firmware analysis
	- static analysis alone is not enough to recover deep info from firmware

# Security Takeaways
- flexible but fragmented design
	- shows how security by design plus layering in new models can increase complexity
	- security must be considered **holistically**
		- not just platform but the way info is provided to user
	- ref monitor guarantees at middleware (binder) and platform level (selinux and measured boot)