# Install-kvm-hypervisor

KVM (Kernel-based Virtual Machine) is a virtualization infrastructure for the Linux kernel that turns it into a hypervisor. To manage virtual machines efficiently, a set of tools is required. Below are the steps to install KVM tools on a Linux system.

### Step 1: Check Hardware Virtualization Support

Before installing KVM, ensure that your CPU supports hardware virtualization and it is enabled in the BIOS settings. You can check this by running:

```bash
egrep -c '(vmx|svm)' /proc/cpuinfo
```
or
```bash
lscpu
```
### Step 2: Install KVM Packages

Install the KVM packages using your distribution's package manager

```bash
dnf install qemu-kvm libvirt virt-install bridge-utils
```

### Step 3: Start and Enable libvirtd Service

After installing the required packages, start and enable the libvirtd service to manage virtual machines:

```bash
systemctl start libvirtd
systemctl enable libvirtd
```

### Step 4: Verify Installation

To verify that KVM is installed correctly and the required kernel modules are loaded, run:

```bash
lsmod | grep kvm
```


