# Create the control plane nodes or master nodes

**DNS details**
```bash
master0.idz-ocp-z-1.svl.ibm.com		9.30.200.147 
master1.idz-ocp-z-1.svl.ibm.com		9.30.200.148 
master2.idz-ocp-z-1.svl.ibm.com		9.30.200.149 
```

## Create disk image

```bash
qemu-img create -f raw /var/lib/libvirt/images/master0.ocp4.img 100G
qemu-img create -f raw /var/lib/libvirt/images/master1.ocp4.img 100G
qemu-img create -f raw /var/lib/libvirt/images/master2.ocp4.img 100G
```

## Create master nodes

Run the following virt-install command to create master nodes

### master0

```bash
virt-install \
  --connect qemu:///system \
  --name master0.ocp4 \
  --vcpus 4 \
  --memory 16000 \
  --disk /var/lib/libvirt/images/master0.ocp4.img,size=100 \
  --network network=macvtap-net \
  --boot hd \
  --location /var/lib/libvirt/boot,kernel=rhcos-4.15.0-s390x-live-kernel-s390x,initrd=rhcos-4.15.0-s390x-live-initramfs.s390x.img \
  --extra-args "\
    rd.neednet=1 \
    coreos.inst=yes \
    coreos.inst.install_dev=vda \
    coreos.live.rootfs_url=http://9.30.200.145:8080/ocp/rhcos-4.15.0-s390x-live-rootfs.s390x.img \
    ip=9.30.200.147::9.30.200.1:255.255.252.0:master0.idz-ocp-z-1.svl.ibm.com:enc1:none:none \
    nameserver=9.30.31.32 \
    coreos.inst.ignition_url=http://9.30.200.145:8080/ignitions/master.ign" \
  --noautoconsole \
  --wait
```

### master1

```bash
virt-install \
  --connect qemu:///system \
  --name master1.ocp4 \
  --vcpus 4 \
  --memory 16000 \
  --disk /var/lib/libvirt/images/master1.ocp4.img,size=100 \
  --network network=macvtap-net \
  --boot hd \
  --location /var/lib/libvirt/boot,kernel=rhcos-4.15.0-s390x-live-kernel-s390x,initrd=rhcos-4.15.0-s390x-live-initramfs.s390x.img \
  --extra-args "\
    rd.neednet=1 \
    coreos.inst=yes \
    coreos.inst.install_dev=vda \
    coreos.live.rootfs_url=http://9.30.200.145:8080/ocp/rhcos-4.15.0-s390x-live-rootfs.s390x.img \
    ip=9.30.200.148::9.30.200.1:255.255.252.0:master1.idz-ocp-z-1.svl.ibm.com:enc1:none:none \
    nameserver=9.30.31.32 \
    coreos.inst.ignition_url=http://9.30.200.145:8080/ignitions/master.ign" \
  --noautoconsole \
  --wait
```
### master2

```bash
virt-install \
  --connect qemu:///system \
  --name master2.ocp4 \
  --vcpus 4 \
  --memory 16000 \
  --disk /var/lib/libvirt/images/master2.ocp4.img,size=100 \
  --network network=macvtap-net \
  --boot hd \
  --location /var/lib/libvirt/boot,kernel=rhcos-4.15.0-s390x-live-kernel-s390x,initrd=rhcos-4.15.0-s390x-live-initramfs.s390x.img \
  --extra-args "\
    rd.neednet=1 \
    coreos.inst=yes \
    coreos.inst.install_dev=vda \
    coreos.live.rootfs_url=http://9.30.200.145:8080/ocp/rhcos-4.15.0-s390x-live-rootfs.s390x.img \
    ip=9.30.200.149::9.30.200.1:255.255.252.0:master2.idz-ocp-z-1.svl.ibm.com:enc1:none:none \
    nameserver=9.30.31.32 \
    coreos.inst.ignition_url=http://9.30.200.145:8080/ignitions/master.ign" \
  --noautoconsole \
  --wait
  ```

When this script runs, it creates the VMs for the control nodes. To start the VMs, use the following command.

```bash
virsh start kvm1-master1
virsh start kvm1-master2
virsh start kvm1-master3
```

Monitor the installation process for the bootstrap 

```bash
journalctl -b -f -u bootkube.service
```

The control nodes are ready for the next stage of the installation process when the following message displays in the bootstrap’s logs

```bash
bootkube.service complete
bootkube.service: Succeeded
```

**Note: It can take 5-25 minutes to complete this step**
