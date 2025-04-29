# Docker SSL webapp Revision 

## Docker image with SSL certs 

<img src="ssl1.png">

### Creating a seperate directory to keep all ocp manifest files in single place 

```
PS C:\Users\labuser\Desktop\ashu-project> cd  .\ssl_ocp_nginx\
PS C:\Users\labuser\Desktop\ashu-project\ssl_ocp_nginx> ls


    Directory: C:\Users\labuser\Desktop\ashu-project\ssl_ocp_nginx


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----         4/28/2025   1:27 PM                ashu-webapp
d-----         4/28/2025   1:17 PM                certs
-a----         4/28/2025   1:29 PM            503 Dockerfile
-a----         4/28/2025  12:45 PM            289 nginx-ssl.conf


PS C:\Users\labuser\Desktop\ashu-project\ssl_ocp_nginx> mkdir   ocp_deploy


    Directory: C:\Users\labuser\Desktop\ashu-project\ssl_ocp_nginx


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----         4/29/2025  11:45 AM                ocp_deploy


PS C:\Users\labuser\Desktop\ashu-project\ssl_ocp_nginx> cd  .\ocp_deploy\
PS C:\Users\labuser\Desktop\ashu-project\ssl_ocp_nginx\ocp_deploy> 

```
### we have secret so lets create deployment 

```
 oc  create  deployment  ashu-ssl-app --image=fiservclass.azurecr.io/ashussl:fiservssl  --port 443 --dry-run=client -o yaml >deploy.yaml 

 ===> we have to call secret in deploy.yaml so that ocp nodes can use to pull image

 == Modifed deploy.yaml file 
 apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: ashu-ssl-app
  name: ashu-ssl-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ashu-ssl-app
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: ashu-ssl-app
    spec:
      imagePullSecrets: # calling secret to pull image 
      - name: ashu-secret # name of secret in current project 
      containers:
      - image: fiservclass.azurecr.io/ashussl:fiservssl
        name: ashussl
        ports:
        - containerPort: 443
        resources: {}
status: {}

```

### a visual changes 

<img src="ssl2.png">

### doing it with oc cli 

```
PS C:\Users\labuser\Desktop\ashu-project\ssl_ocp_nginx\ocp_deploy> oc create -f .\deploy.yaml
deployment.apps/ashu-ssl-app created
PS C:\Users\labuser\Desktop\ashu-project\ssl_ocp_nginx\ocp_deploy> oc  get  deploy
NAME             READY   UP-TO-DATE   AVAILABLE   AGE  
ajit-ssl-app     0/1     1            0           2m19s
amit-ssl-app     1/1     1            1           18s  
ashu-ssl-app     1/1     1            1           5s   
rayudu-ssl-app   1/1     1            1           2s   
PS C:\Users\labuser\Desktop\ashu-project\ssl_ocp_nginx\ocp

```
