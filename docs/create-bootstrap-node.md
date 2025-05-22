# Create the bootstrap node

### step 1 . Log on to the svlxioc1 host 


### Step 2 . Create disk image

```bash
qemu-img create -f raw /var/lib/libvirt/images/bootstrap.ocp4.img 100G
```

### Step 3 . Create bootstrap VM

```bash
virt-install \
  --connect qemu:///system \
  --name bootstrap.ocp4 \
  --vcpus 4 \
  --memory 16000 \
  --disk /var/lib/libvirt/images/bootstrap.ocp4.img,size=100 \
  --network network=macvtap-net \
  --boot hd \
  --location /var/lib/libvirt/boot,kernel=rhcos-4.15.0-s390x-live-kernel-s390x,initrd=rhcos-4.15.0-s390x-live-initramfs.s390x.img \
  --extra-args "\
    rd.neednet=1 \
    coreos.inst=yes \
    coreos.inst.install_dev=vda \
    coreos.live.rootfs_url=http://9.30.200.145:8080/ocp/rhcos-4.15.0-s390x-live-rootfs.s390x.img \
    ip=9.30.200.146::9.30.200.1:255.255.252.0:bootstrap.idz-ocp-z-1.svl.ibm.com:enc1:none:none \
    nameserver=9.30.31.32 \
    coreos.inst.ignition_url=http://9.30.200.145:8080/ignitions/bootstrap.ign" \
  --noautoconsole \
  --wait
```

To start the VM so that the bootstrap can load its configuration and proceed, use the following command.

```bash
virsh start bootstrap
```

### Step 4 . Monitor installation process for the bootstrap

To monitor the installation process for the bootstrap, log in to load balancer vm from your system as a rhel user and switch to root user to issue the following command

```bash
ssh core@9.30.200.149
sudo su -
```

```bash
journalctl -b -f -u bootkube.service
```

The bootstrap program should be ready for the next stage of the installation process when the following message displays in bootstrap’s logs

***Created "99_openshift-cluster-api_master-user-data-secret.yaml" secrets.v1./master-user-data -n openshift-machine-api***

When you see this message, go back to svlxioc1 host and run the next script to install the control nodes (master nodes)