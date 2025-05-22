# Check for CSR requests

### step 1. Log on to the Load balancer vm: 9.30.200.145

### step 2. Export the KUBECONFIG shell variable

```bash
export KUBECONFIG=/root/ocp-install/ocp-deploy/auth/kubeconfig
```

### step 3. Check for pending CSRs

Run the following command to check for pending CSRs

```bash
oc get csr
```
### Step 4. Approve pending CSRs

If there is any CSR pending , run the following command to approve it

```bash
oc get csr | grep -i Pending | awk '{print $1}' | xargs oc adm certificate approve
```

### Step 5. Check whether all nodes have joined your Red Hat OpenShift cluster

```bash
oc get nodes
```
