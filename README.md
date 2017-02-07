# netauto-lab: Network Automation Lab

Created for the ipspace.net Network Automation Solutions course.

The lab is based on the following software:
* Arista vEOS
* Ubuntu
* Vagrant
* VirtualBox
* Ansible

The network design is a basic leaf/spine topology that will be built entirely using eBGP peerings.

The entire lab is running on an Intel NUC with internal 120GB SSD and 16GB of RAM.

It's quite likely that I'll use a 2nd NUC running ESXi to run up a VIRL lab at some point as my day job is still Cisco everywhere...

Currently working:

* Automatic deployment of Spine/Leaf switches, configuration of hostname, management VRF and configuration of management (Eth1) interface.
* Automatic deployment of NMS host, configuration of hostname and management IP.
* Automatic installation on NMS host: Ansible (from ansible repo), pip (Ubuntu repo), ntc-ansible (via pip)
* Tested communication from nms host to each switch using Ansible raw module.

~~~~
vagrant@nms:~$ ansible all -i hosts -m raw -a "show version"
spine-2 | SUCCESS | rc=0 >>
Arista vEOS
Hardware version:
Serial number:
System MAC address:  0800.272e.8530

Software image version: 4.17.2F
Architecture:           i386
Internal build version: 4.17.2F-3696283.4172F
Internal build ID:      c6362f13-ae6d-4c88-b5fd-4678d66018ab

Uptime:                 45 minutes
Total memory:           1893460 kB
Free memory:            932508 kB

Shared connection to 10.0.5.11 closed.


leaf-1 | SUCCESS | rc=0 >>
Arista vEOS
Hardware version:
Serial number:
System MAC address:  0800.27d9.ce40

Software image version: 4.17.2F
Architecture:           i386
Internal build version: 4.17.2F-3696283.4172F
Internal build ID:      c6362f13-ae6d-4c88-b5fd-4678d66018ab

Uptime:                 43 minutes
Total memory:           1893460 kB
Free memory:            932968 kB

Shared connection to 10.0.5.12 closed.


spine-1 | SUCCESS | rc=0 >>
Arista vEOS
Hardware version:
Serial number:
System MAC address:  0800.27de.389c

Software image version: 4.17.2F
Architecture:           i386
Internal build version: 4.17.2F-3696283.4172F
Internal build ID:      c6362f13-ae6d-4c88-b5fd-4678d66018ab

Uptime:                 48 minutes
Total memory:           1893460 kB
Free memory:            932652 kB

Shared connection to 10.0.5.10 closed.


leaf-2 | SUCCESS | rc=0 >>
Arista vEOS
Hardware version:
Serial number:
System MAC address:  0800.27b2.2c4e

Software image version: 4.17.2F
Architecture:           i386
Internal build version: 4.17.2F-3696283.4172F
Internal build ID:      c6362f13-ae6d-4c88-b5fd-4678d66018ab

Uptime:                 42 minutes
Total memory:           1893460 kB
Free memory:            930404 kB

Shared connection to 10.0.5.13 closed.
~~~~