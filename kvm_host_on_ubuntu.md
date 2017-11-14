
## How to run virtual machines on Ubuntu using KVM

### Install packages with virtualization tools 

```console
# apt-get install qemu-kvm qemu-utils libvirt-bin uuid virtinst --no-install-recommends
```

### Configure bridged networking

For VMs you can provide access to external networks through a bridge interface created and configured on a KVM host.
Bridged networking is used for interconnecting the virtual interfaces of VMs with the physical interface of the host.  
  
Edit the file with the host network configuration.

```console
# vim /etc/network/interfaces
```

If you have a primary network interface `eth0`, replace it configuration by the lines with the bridge interface configuration.

```text
auto br0
iface br0 inet static
    bridge_ports    eth0
    bridge_stp      off
    bridge_maxwait  0
    bridge_fd       0
    address 192.168.0.3
    netmask 255.255.255.0
    gateway 192.168.0.1
    dns-nameservers 192.168.0.1
```

Stop your primary network interface `eth0`.

```console
# ifdown eth0
```

Start the bridge network interface `br0`.

```console
# ifup br0
```

Warning! Run `ifdown` and `ifup` commands from the host console or you will lost a connection to the host.  

Enable IP forwarding if you haven't already did it.  

To the file `/etc/sysctl.conf` add the line
```text
net.ipv4.ip_forward = 1
```

It's recommended to avoid sending packets traversing the bridge to iptables for processing.

Add the lines to the file `/etc/sysctl.conf`
```text
net.bridge.bridge-nf-call-arptables = 0
net.bridge.bridge-nf-call-ip6tables = 0
net.bridge.bridge-nf-call-iptables = 0
```

Apply the changes in `/etc/sysctl.conf`
```console
# sysctl -p
```

If for somehow reasons you don't want to avoid processing the bridge packets, allow forwarding for them

```console
# iptables -A FORWARD -i br0 -j ACCEPT
```

IF you have a connection to the Internet directly from the host system through eth1 interface, turn on IP masquerading for your guest VMs
```console
# iptables -t nat -A POSTROUTING --out-interface eth1 -j MASQUERADE  
```

Store the changes in the iptables configuration file or you will lose them after reboot!

### Create a VM using CLI tools

You may want to set a MAC address on a VM's virtual network interface manually.  
It's recommended to use `54:52:00`' for the first three octets in KVM/Qemu VMs.  
Run the follow command to get a random MAC address for a new VM.

```console
# printf '54:52:00:%02X:%02X:%02X\n' $((RANDOM%256)) $((RANDOM%256)) $((RANDOM%256))
```

Create and run a new VM with Ubuntu as a guest system

```console
# virt-install \
--name domainname \
--hvm \
--ram 4096 \
--vcpus 2 \
--disk path=/var/lib/libvirt/images/domainname.qcow2,size=10,spare=false,bus=virtio \
--os-type linux \
--os-variant ubuntu16.04 \
--cdrom /tmp/ubuntu-16.04.3-server-amd64.iso \
--network bridge=br0 \
--mac 54:52:00:B3:26:AD \
--graphics vnc,listen=0.0.0.0,port=5900 \
--noautoconsole \
--console pty,target_type=serial 
```

The options meaning

```text
--name          - a name for a new VM (you will use it for manage your VM)
--ram           - the amount of RAM
--vcpus         - the number of CPUs
--disk          - a path to the guest OS image file, with the size and virtio bus (fastest) and without spare (for speed)
--cdrom         - an iso image with the guest OS installation
--network       - a host OS bridge interface
--mac           - a MAC address for your guest OS
--graphics      - bind the guest OS console to the 5900 port of the host system (you can connect to it using a VNC client)
--noautoconsole - don't bind the guest system output to any tool
```

If you want to start you VM after reboot

```console
# virsh autostart domainname
```

### Migrate a VM from VirtualBox

Convert a VirtualBox image to a Qemu image.

```console
# qemu-img convert -f vdi -O qcow2 /tmp/domainname.vdi /var/lib/libvirt/images/domainname.qcow2
```

Copy an xml file from another VM as a sample.  
In the cloned VM's xml file change parameters: a name, an amount of RAM, a number of CPUs, an uuid value, a path to the iamge, a MAC address and a VNC port.  
Some of them you can take from the VirtualBox configuration file.  
Or you can get a random values for uuid and MAC

```console
# uuid
# printf '54:52:00:%02X:%02X:%02X\n' $((RANDOM%256)) $((RANDOM%256)) $((RANDOM%256))
```

Define your migrated VM.  
xmlfile is the VM's xml file placed to `/etc/libvirt/qemu`.

```console
# virsh define xmlfile
```

Create and run the VM.  
domainname is the VM's name from the xmlfile.

```console
# virsh create domainname
```

Turn autostart on if you want to start you VM after reboot.

```console
# virsh autostart domainname
```

### Managing a VM from CLI

If you want to make and apply changes in VM's configuration file

```console
# virsh shutdown domainname
# virsh define xmlfile
# virsh start domainname
```

Make a backup of xml files from `/etc/libvirt/qemu`
If you run `virsh undefine domainname` it deletes domainname's xml file!

