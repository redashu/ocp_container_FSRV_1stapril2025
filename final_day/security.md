## Understanding users , Role and projects 

### login with admin user in openshift cli 

```
 oc login   https://api.tcs-cluster.ashutoshh.xyz:6443  -u kubeadmin -p "yourpasswrod"  --insecure-skip-tls-verify
WARNING: Using insecure TLS client config. Setting this option is not supported!

Login successful.

You have access to 76 projects, the list has been suppressed. You can list all projects with 'oc projects'

Using project "default".

```

### current user check 

```
oc whoami
```

## Creating new user in OCP 

- **- this can be done using multiple ways like htpasswd , LDAP  etc 

## using htpsasswd to create users in oc 

```
yum install httpd-tools 
Last metadata expiration check: 1 day, 0:29:29 ago on Mon Aug 26 05:26:15 2024.
Dependencies resolved.
==============================================================================================================================================================
 Package                                 Architecture                  Version                                       Repository                          Size
==============================================================================================================================================================
Installing:
 httpd-tools                             x86_64                        2.4.59-2.amzn2023                             amazonlinux                         81 k
Installing dependencies:
 apr                                     x86_64                   

 ```

 ### creating user 
 
 ```
htpasswd -cbB  creds ashu redhat@1234 
Adding password for user ashu
[root@ip-172-31-16-156 users]# ls
creds
[root@ip-172-31-16-156 users]# cat creds 
ashu:$2y$05$/bPl22IW0E.4itCq216jw.otHq/i8xQMSSUFrLEdbbERym2JRHmm6

 ```

 ### creating secret in openshift-config project

 ```
oc create  secret generic tcs    --from-file htpasswd=/root/ocp/newsetup/users/creds -n openshift-config
secret/tcs created

 ```

 ### edit oauth 

 ```
oc edit oauth 

===> under spec section add 
spec:
  identityProviders:
  - name: htpasswd_provider
    mappingMethod: claim
    type: HTPasswd
    htpasswd:
      fileData:
        name: tcs # name of secret

 ```

 ### now you can keep creating users 

 ```
htpasswd -bB /root/ocp/newsetup/users/creds  user2 password2
 ```

 ### lets try to login 

 ```
 oc login   https://api.tcs-cluster.ashutoshh.xyz:6443  -u ashu -p redhat@1234  --insecure-skip-tls-verify
WARNING: Using insecure TLS client config. Setting this option is not supported!

Login successful.

You don't have any projects. You can try to create a new project, by running

    oc new-project <projectname>

[root@ip-172-31-16-156 users]# oc whoami
ashu

 ```

 ### new user can create project and perform all the things over there only 

 ```
oc  new-project tt1
 ```

## as admin user give role to user in project 

```
oc adm policy add-role-to-user admin user1 -n my-project

===
oc get rolebindings -n my-project
 
 ==>
 oc adm policy remove-role-from-user admin user1 -n my-project
 
 