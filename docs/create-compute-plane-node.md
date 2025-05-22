# Create the compute plane nodes (worker nodes)

**DNS details**
```bash
worker0.idz-ocp-z-1.example.com		9.30.200.150 
worker1.idz-ocp-z-1.example.com		9.30.200.151
```

## Create disk image

```bash
qemu-img create -f raw /var/lib/libvirt/images/worker0.ocp4.img 100G
qemu-img create -f raw /var/lib/libvirt/images/worker1.ocp4.img 100G
```

## Create worker nodes

Run the following virt-install command to create worker nodes

### worker0

```bash
virt-install \
  --connect qemu:///system \
  --name worker0.ocp4 \
  --vcpus 4 \
  --memory 16000 \
  --disk /var/lib/libvirt/images/worker0.ocp4.img,size=100 \
  --network network=macvtap-net \
  --boot hd \
  --location /var/lib/libvirt/boot,kernel=rhcos-4.15.0-s390x-live-kernel-s390x,initrd=rhcos-4.15.0-s390x-live-initramfs.s390x.img \
  --extra-args "\
    rd.neednet=1 \
    coreos.inst=yes \
    coreos.inst.install_dev=vda \
    coreos.live.rootfs_url=http://9.30.200.145:8080/ocp/rhcos-4.15.0-s390x-live-rootfs.s390x.img \
    ip=9.30.200.150::9.30.200.1:255.255.252.0:worker0.idz-ocp-z-1.example.com:enc1:none:none \
    nameserver=9.30.31.32 \
    coreos.inst.ignition_url=http://9.30.200.145:8080/ignitions/worker.ign" \
  --noautoconsole \
  --wait
  ```
  
### worker1

```bash
virt-install \
  --connect qemu:///system \
  --name worker1.ocp4 \
  --vcpus 4 \
  --memory 16000 \
  --disk /var/lib/libvirt/images/worker1.ocp4.img,size=100 \
  --network network=macvtap-net \
  --boot hd \
  --location /var/lib/libvirt/boot,kernel=rhcos-4.15.0-s390x-live-kernel-s390x,initrd=rhcos-4.15.0-s390x-live-initramfs.s390x.img \
  --extra-args "\
    rd.neednet=1 \
    coreos.inst=yes \
    coreos.inst.install_dev=vda \
    coreos.live.rootfs_url=http://9.30.200.145:8080/ocp/rhcos-4.15.0-s390x-live-rootfs.s390x.img \
    ip=9.30.200.151::9.30.200.1:255.255.252.0:worker1.idz-ocp-z-1.example.com:enc1:none:none \
    nameserver=9.30.31.32 \
    coreos.inst.ignition_url=http://9.30.200.145:8080/ignitions/worker.ign" \
  --noautoconsole \
  --wait
```

 To start the VMs, run the following command

```bash
virsh start kvm1-worker0
virsh start kvm1-worker1
```