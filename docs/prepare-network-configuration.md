# Managing Virtual Bridges on RHEL KVM Host

#### Step 1: Check Current Virtual Bridges

To check the current virtual bridges defined on the RHEL KVM host, run:

```bash
$ virsh net-list
```
You should see output similar to:

#### Step 2: Create a New Bridge

Create a file macvtap-net.xml file:

```bash
<network>
	<name>macvtap-net</name>
	<forward mode="bridge">
		<interface dev="<network_interface_name>"/>
	</forward>
</network>
```

Create the new bridge using the macvtap-net.xml file

```bash
$ virsh net-define macvtap-net.xml
Network macvtap-net defined from macvtap-net.xml
```

#### Step 3: Activate the new bridge and set it to auto-start.
```bash
$ virsh net-autostart macvtap-net
Network macvtap-net marked as autostarted

$ virsh net-start  macvtap-net
Network macvtap-net started
```

#### Step 4: Check whether the new bridge is created, active, and set to auto-start.
```bash
$ virsh net-list
```
#### Step 5: Set the /var/lib/libvirt/images permissions
```bash
$ sudo chown root:libvirt /var/lib/libvirt/images
$ sudo chmod 775 /var/lib/libvirt/images
```