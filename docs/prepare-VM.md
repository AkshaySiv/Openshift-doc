
# Prepare load balacer VM 

### Step 1 : Log on to IBMZ server host 

### Step 2 : Mount rhel 9 iso image to IBMZ server. please follow  the steps from [https://ftp3.linux.ibm.com/](https://ftp3.linux.ibm.com/)


### Step 3 : Create disk image

We need to create a Disk image ,which will serve as the virtual disk for the VM, providing storage for the operating system and data within the virtual environment
To create a disk image run the below command

```bash
qemu-img create -f qcow2 /var/lib/libvirt/images/lb.ocp4.qcow2 25G
```

This command creates a new 25GB disk image file in the qcow2 format at the specified location. This disk image can then be used as a virtual disk for a QEMU/KVM virtual machine. The qcow2 format ensures efficient storage usage by dynamically allocating space and provides advanced features like snapshots and compression

### Step 4 : Create a Load balancer VM
virt-install command is used to create and configure virtual machines using libvirt

Below command creates a new virtual machine named lb-ocp4 using KVM, with 4 GiB of RAM, 4 virtual CPUs, and a 40 GB disk image. The VM will boot from a RHEL 9 installation ISO, has no graphical output, and is configured to use a specific network macvtap-net

```bash
sudo virt-install \
--name lb-ocp4 \
--virt-type kvm \
--memory 4096 \
--vcpus 4 \
--boot hd,menu=off \
--cdrom=/var/lib/libvirt/images/rhel9.iso \
--disk path=/var/lib/libvirt/images/lb.ocp4.qcow2.qcow2,device=disk,size=40 \
--graphics none \
--os-type linux \
--os-variant rhel9 \
--network network=macvtap-net \
--noautoconsole \
--noreboot
```
Note : You typically need to set up login credentials, including a username and password, during the installation process. This ensures that you can access and manage the VM once it's created

Run the following command to start your new VM
``` bash
virsh start lb-ocp4 
```
Verify that the VM is running.
``` bash
virsh list --all 
```

### Step 5 : Configuring IP Address within the Load balancer VM

Login to newly created vm lb-ocp4

``` bash
virsh console lb-ocp4 
```

The network configuration files are typically stored in the /etc/NetworkManager/system-connections/ directory. Here's how you can configure a static IP address in RHEL 9 using NetworkManager:

Create a New Connection Profile:

Run the following command to create a new connection profile for your network interface:

```bash
nmcli connection add type ethernet ifname <interface_name> con-name <connection_name>
```
Replace <interface_name> with the name of your network interface (e.g., enc1) and <connection_name> with a name for your connection profile.

Configure Static IP Address:

Set the static IP address, subnet mask, gateway, and DNS servers for the connection profile. 

Here we need to assign api ip as the loadbalancer ip , which is  9.30.200.145 (api.idz-ocp-z-1.example.com)
[Refer DNS entries ](config/dns-entries.txt)

Run the following commands

```bash
nmcli connection add type ethernet ifname enc1 con-name enc1 \
ipv4.addresses "9.30.200.145/32" \
ipv4.gateway "9.30.200.1" \
ipv4.dns "9.30.31.32" \
ipv4.dns-search "example.com" \
ipv4.method manual \
ipv6.method ignore 
```
After running this command, you may need to activate the connection using:
```bash
nmcli connection up enc1
```
Once you've completed these steps, your load balancer VM should be configured with a static IP address(9.30.200.145) and should be accessible from outside the IBMZ server host.
Ensure your are able to connect to your new load balancer VM from your local machine (that is, your laptop or workstation)

### Step 6 : Install and configure HA proxy

```bash
dnf install haproxy
```
Back up the default configuration file.

``` bash
cd /etc/haproxy
cp haproxy.cfg haproxy.bak
```

Copy the [haproxy.cfg](config/loadbalancer.txt) file from to /etc/haproxy/haproxy.cfg in the lb-ocp4 virtual machine

Note: Be sure to set the proper owner and permissions for /etc/haproxy/haproxy.cfg

Start and enable the haproxy to be persistent across reboots

```bash
systemctl --now enable haproxy
```


### Step 7 : Install and configure HTTPd

```bash
dnf install httpd
```
Create a backup of the configuration file

``` bash
cd /etc/httpd/conf
cp httpd.conf httpd.bak
```
Edit the httpd.conf file to modify Listen 80 to Listen 8080, and save the file

Create a custom config "custom.conf" in **/etc/httpd/conf/conf.d** and add the below. This configuration sets up a httpd on port 8080, serving files from **/var/www/html**
 

```bash
<VirtualHost *:8080>
    DocumentRoot /var/www/html
</VirtualHost>
```

Start and enable HTTPd to be persistent across reboots
```bash
systemctl --now enable httpd
```