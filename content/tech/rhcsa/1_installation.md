# Chapter 1 RHCSA Notes - Installation

## About RHEL9

- Kernel 5.14
- Released May 2019
- Built along side of Fedora 34
- Installer program = Anaconda
- Default Bootloader = GRUB2
- Default automatic partitioning = /boot, /, swap
- Default desktop environment = GNOME

## Installation Logs

**/root/anaconda-ks.cfg** Configuration entered

**/var/log/anaconda/anaconda.log** Contains informational, debug, and other general messages

**/var/log/anaconda/journal.log** Stores messages generated by many services and components during system installation

**/var/log/anaconda/packaging.log** Records messages generated by the dnf and rpm commands during software installation

**/var/log/anaconda/program.log** Captures messages generated by external programs

**/var/log/anaconda/storage.log** Records messages generated by storage modules

**/var/log/anaconda/syslog** Records messages related to the kernel

**/var/log/anaconda/X.log** Stores X Window System information

```
Note: Logs are created in /tmp then transferred over to /var/log/anaconda once the install is finished.
```


## 6 Virtual Consoles

- Monitor the installation process.
- View diagnostic messages.
- Discover and fix any issues encountered.
- Information displayed on the console screens is captured in installation log files.

Console 1 (Ctrl+Alt+F1)

- Main screen
- Select language
- Then switches default console to 6

Console 2 (Ctrl_Alt+F2)

- Shell interface for root user

Console 3 (Ctrl_Alt+F3)

- Displays install messages
- Stores them in /tmp/anaconda.log
- Info on detected hardware, etc.

Console 4 (Ctrl_Alt+F4)

- Shows storage messages
- Stores them in /tmp/storage.log

Console 5 (Ctrl_Alt+F5)

- Program messages
- Stores them in /tmp/program.log

Console 6 (Ctrl_Alt+F6)

- Default Graphical configuration and installation console screen

Lab: What happens if I press the console hotkeys while I am logged in to my desktop?

Console 1 Brings you to the log in screen. Console 2 does nothing. Console 3-6 all bring you to this log in screen

![[Pasted image 20230624051043.png]]

Lab Setup

VM1
```
server1.example.om 
192.168.0.110 
Memory: 2GB 
Storage: 1x20GB 
2 vCPUs
```

VM2
```
server2.exmple.om 
192.168.0.120 
Memory: 2048 
Storage: 1x20GB 
	4x250 MB data disk 
	1x5GB data disk 
2 vCPUs
```

## Setting up VM1

Download the disc iso on Redhat's website: [https://access.redhat.com/downloads/content/rhel](https://access.redhat.com/downloads/content/rhel)

Name RHEL9-VM1 Accept defaults

Set drive to 20 gigs

press "spe" to hlt utooot

Selet instll

selet lnguge

onfigure timezone under time & dte

go into instlltion destintion nd li "done"

Networ nd hostnme settings

1. hnge the hostnme to [server1.exmple.om](http://server1.exmple.om/)
2. go to IPv4 settings in networ nd host nd set to mnul ddress: 192.168.0.110 netms 24 gtewy 192.168.0.1 then sve
3. slide the on/off swith in the min menu to on

Set root pssword

Chnge the oot order

1. power off the vm
2. Set oot sequene to hrd dis first then optil, remove floppy

Aept liense terms nd rete user

?Enle geogrphil lotion?

ssh from host os with putty

Issue these ommnds fter set up

```
whoami 
hostname 
pwd 
logout or ctrl+d
```

## Using cockpit
- Web gui for managing RHEL system
- Comes pre-installed
	- if not then install with:
	```
	sudo dnf install cockpit
	```
- must enable cockpit socket
	```
	sudo systemctl enable --now cockpit.socket
	```
- https://yourip:9090
## Labs

### Lab: 
Enable cockpit.socket:
```
sudo systemctl enable --now cockpit.socket
```

In a web browser, go to https://\<your-ip\>:9090