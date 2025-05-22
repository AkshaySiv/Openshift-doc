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
oc create namespace idaa-ns
```
### 2. Create Service account in OpenShift cluster

- Log in to your OpenShift cluster.
- Create a service account with the necessary permissions in the namsepsace

``` bash
oc create serviceaccount dwabuild -n idaa-ns
```

- Retrieve the service account token:

```bash
oc create token dwabuild --duration=$((90*24))h -n idaa-ns
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
  - Enter the OpenShift API URL  : `https://api.apps.idz-ocp-x-1.svl.ibm.com:6443`
  - Select the credentials added in the previous step.
  - Test the connection to ensure it is working.

### 5 . Create RoleBinding

To create pods we need to create a new RoleBinding for our user that we called idaa-ns-admin that references the Role cluster-admin 

```bash
oc create rolebinding idaa-ns-admin --clusterrole=cluster-admin --serviceaccount=idaa-ns:dwabuild -n idaa-ns
```

- Explanation:
     - `idaa-ns-admin` is the name of the `RoleBinding`.
     - `--clusterrole=cluster-admin` specifies that the `RoleBinding` references the `cluster-admin` cluster role.
     - `--serviceaccount=idaa-ns:dwabuild` specifies the service account `dwabuild` in the `idaa-ns` namespace.
     - `-n idaa-ns` sets the namespace for the `RoleBinding`.

### 6 . Create Secrets for Artifactorty image pull in OpenShift

```bash

oc create secret docker-registry artifactory-proxy-svl-access-token --docker-server="https://na-proxy-svl.artifactory.swg-devops.com" --docker-username="idaa.fyreicp@de.ibm.com" --docker-password="**********qUVJDUGV5OEJOdExU" --docker-email="idaa.fyreicp@de.ibm.com" -n idaa-ns
```
```bash
oc create secret docker-registry artifactory-acces-token --docker-server="https://na-public.artifactory.swg-devops.com" --docker-username="idaa.fyreicp@de.ibm.com" --docker-password="**********qUjhqUVJDUGV5OEJOdExU" --docker-email="idaa.fyreicp@de.ibm.com" -n idaa-ns
```

### 7 . Prepare pod spec


[https://github.ibm.com/Everest/jenkins-shared-library/blob/Openshift_z_Cluster_test/resources/podspecs/centoscppbuild-test-small-x86.yml](https://github.ibm.com/Everest/jenkins-shared-library/blob/Openshift_z_Cluster_test/resources/podspecs/centoscppbuild-test-small-x86.yml)
