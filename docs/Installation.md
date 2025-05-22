
#  Installation

This guide walks you through the installation process of setting up a Red Hat OpenShift cluster in IBM Z. Each step is crucial to ensure a smooth and successful deployment

## Steps to Install OpenShift

1. **[Step 1: Install KVM Hypervisor](./install-kvm-hypervisor.md)**
    - Install the KVM hypervisor, which is necessary for creating and managing virtual machines that will run the OpenShift cluster components.

2. **[Step 2: Prepare Network Configuration](./prepare-network-configuration.md)**
    - Configure the network settings to ensure proper communication between the cluster nodes and other network resources.

3. **[Step 3: Prepare Load Balancer VM](./prepare-VM.md)**
    - Set up a virtual machine to act as a load balancer, distributing traffic among the OpenShift cluster nodes for improved reliability and performance.

4. **[Step 4: Prepare the Red Hat OpenShift Cluster](./create-openshift.md)**
    - Initialize the OpenShift cluster setup, configuring the necessary parameters and settings.

5. **[Step 5: Create Bootstrap Node](./create-bootstrap-node.md)**
    - Create and configure the bootstrap node, which helps in bootstrapping the OpenShift cluster during installation.

6. **[Step 6: Create the Control Plane Nodes (Master Nodes)](./create-control-plane-node.md)**
    - Set up the control plane nodes (master nodes) that will manage the OpenShift cluster operations and orchestration.

7. **[Step 7: Create the Compute Plane Nodes (Worker Nodes)](./create-compute-plane-node.md)**
    - Add the compute plane nodes (worker nodes) to the cluster, where application workloads will be deployed and run.

8. **[Step 8: Check for CSR Requests](./CSR-requests.md)**
    - Review and approve the Certificate Signing Requests (CSRs) from the cluster nodes to ensure secure communication within the cluster.

9. **[Step 9: Access Web UI](./Access-web-ui.md)**
    - Access the OpenShift web console to manage and monitor the cluster through a graphical user interface.

10. **Certificate Installation**
    - Install and configure the necessary certificates to secure the OpenShift cluster and its communications.


