# Integrating Jenkins Master with OpenShift

Please find the step-by-step guide on how to integrate a Jenkins Master with an OpenShift x86/s390x cluster. This integration will enable Jenkins to deploy and manage workload on OpenShift


## Prerequisites

1 - Jenkins Master: Ensure you have a running Jenkins instance

2 - OpenShift Cluster: Ensure you have access to an OpenShift x86 cluster

3 - Jenkins Plugins: Install the following Jenkins plugins:
    Kubernetes Plugin


## Steps for Integration

### 1. Create a namespace in openshift cluster

```bash
oc create namespace ocp-namespace
```
### 2. Create Service account in OpenShift cluster

- Log in to your OpenShift cluster.
- Create a service account with the necessary permissions in the namsepsace

``` bash
oc create serviceaccount dwabuild -n ocp-namespace
```

- Retrieve the service account token:

```bash
oc create token dwabuild --duration=$((90*24))h -n ocp-namespace
```

### 3. Configure OpenShift Credentials in Jenkins

- Navigate to `Manage Jenkins` > `Manage Credentials`.
- Select the appropriate store (e.g., `Jenkins`).
- Click on `Add Credentials`.
- Select `OpenShift Token` as the kind.
- Enter the token retrieved from the service account.
- Give it an ID and a description.


### 4. Test connection of Jenkins & OpenShift

- Navigate to `Manage Jenkins` > `Cloud`.
- Add a new Cloud Configuration > `x86-OpenShift-Cluste`.
  - Enter the OpenShift API URL  : `https://api.apps.idz-ocp-x-1.example.com:6443`
  - Select the credentials added in the previous step.
  - Test the connection to ensure it is working.

### 5 . Create RoleBinding

To create pods we need to create a new RoleBinding for our user that we called ocp-namespace-admin that references the Role cluster-admin 

```bash
oc create rolebinding ocp-namespace-admin --clusterrole=cluster-admin --serviceaccount=ocp-namespace:dwabuild -n ocp-namespace
```

- Explanation:
     - `ocp-namespace-admin` is the name of the `RoleBinding`.
     - `--clusterrole=cluster-admin` specifies that the `RoleBinding` references the `cluster-admin` cluster role.
     - `--serviceaccount=ocp-namespace:dwabuild` specifies the service account `dwabuild` in the `ocp-namespace` namespace.
     - `-n ocp-namespace` sets the namespace for the `RoleBinding`.



