## OCP installation options 

### IPI method understanding 

<img src="ipi.png">

### openshift installers for creating cluster 

<img src="ocp1.png">

## using openshift-installer 

```
mkdir hello_ocp
openshift-install create install-config --dir=./hello_ocp 
openshift-install create cluster --dir=./hello_ocp

```

### history 

```
### NEW Steps

1. Extract the OpenShift client and installer tar files:
    ```bash
    tar xvf openshift-client-linux.tar.gz
    tar xvf openshift-install-linux.tar.gz
    ```

2. Copy the binaries to `/usr/bin/`:
    ```bash
    sudo cp oc /usr/bin/
    sudo cp kubectl /usr/bin/
    sudo cp openshift-install /usr/bin/
    ```

3. Verify the versions:
    ```bash
    oc version
    kubectl version
    openshift-install version
    ```

4. Create a directory for the OpenShift setup:
    ```bash
    mkdir hello_ocp
    ```

5. Configure AWS credentials:
    ```bash
    aws configure
    ```

6. Generate the OpenShift install configuration:
    ```bash
    openshift-install create install-config --dir=./hello_ocp
    ```

7. Edit the `install-config.yaml` file as needed:
    ```bash
    vim hello_ocp/install-config.yaml
    ```

8. Generate an SSH key if not already available:
    ```bash
    ssh-keygen
    cat ~/.ssh/id_rsa.pub
    ```

9. Update the SSH key in `install-config.yaml`:
    ```bash
    vim hello_ocp/install-config.yaml
    ```

10. Create the OpenShift cluster:
     ```bash
     openshift-install create cluster --dir=./hello_ocp --log-level=debug
     ```

11. Export the kubeconfig file for cluster access:
     ```bash
     export KUBECONFIG=./hello_ocp/auth/kubeconfig
     ```

12. Verify the cluster setup:
     ```bash
     oc whoami
     oc get nodes
     oc get csr
     oc get nodes
     ```
```

# OCP offers RBAC to provide LIMITed access to particular user in particular project 

<img src="proj1.png">


### incase of self signed certs we can use skip login 

<img src="skl.png">

### Creatin user 

<img src="user1.png">

### from a new user in new project if we try to create pod we get below error

<img src="user2.png">

### adding scc

```
 oc  adm  policy  add-scc-to-user  privileged -z  default -n  common-project 
 oc adm policy add-scc-to-user anyuid -z default -n  common-project 
 ```

 ## Info about operator 

 <img src="ops.png">

 ## more info about operator 

 <img src="ops1.png">