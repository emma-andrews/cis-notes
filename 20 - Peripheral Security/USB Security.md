- not gonna write up internal workings and hardware of usb, see the slides if you wanna look at it

# USB Attacks
- people just trustingly plug random usbs into sensitive devices
	- usb can steal/corrupt data etc on that device
- **layers of attacks**
	- *human*
	- *application*
	- *transport*
	- *physical*

## Human Layer
- **outsider threats**
	- *social engineering*
	- *usb baiting* - labeling a usb in some way that will entice a person to plug it into their device
- **insider threats**
	- snowden - thumb drive for nsa files
	- manning - cd-rom for military files

## Application Layer
- client <--> function
- **code injection**
	- stuxnet
	- conflicker
	- flame
- **data exfiltration**
	- webcam - can be turned on and used without the user knowing
	- turnipschool - hides wifi in a usb
	- usbee - usb transmits information to nearby receiver

## Transport Layer
- os <--> firmware
- **protocol masquerading**
	- rubber ducky
	- badusb - reprograms usb to act like a human interface device, can exec cmds and malicious programs
- **protocol corruption**
	- facedancer - simple hardware devices that act as "remote-controlled" usb devices, can carry out mitm usb connections
		- can alter packets live, perform protocol analysis
	- syzkaller

## Physical Layer
- bus interface <--> bus interface
- **signal eavesdropping**
	- usbsnooping - finding leakage signal within usb traffic signal
	- cottonmouth - provides wireless bridge into target network and is able to load exploit software onto target pcs
- **signal injection**
	- usbkiller - sends high voltage power signals to device repeatedly, will damage unprotected hardware components
	- usbee - malware in root hub

# USB-C Authentication
- first official response to security
- bring ca model into usb ecosystem
- **authentication protocol**
	- *digest query* - host asks device or digest, device returns it
	- *certificate read* - host asks device for certificate, device returns it
	- *authentication challenge* - host issues a challenge to device, device has to respond correctly to be authenticated
- **formal verification**
	- *untrusted channels* - networking protocol vs hardware protocol
	- *protected* - private keys vs certificates
		- private keys are secure, but still vulnerable to spoofing and badusb attacks
	- *security goals* - identity vs functionality
- **pros (???) and cons** - some could be interpreted as a pro i guess but overall these are drawbacks (e.g. no support for legacy = update and be secure please)
	- no binding for identification with functionality
	- unidirectional authentication
	- vague policy component
	- impractical key protection requirement
	- no revocation or self-signed certificates
	- no support for legacy products
- implementing and integrating solutions for security into usb will have ramifications on other peripherals
	- e.g. bluetooth, bluetooth low energy, near field communication, ultra wideband
- reference monitor guarantees are possible on host side but interactions are complex