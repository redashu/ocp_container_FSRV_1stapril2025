# ocp_container_FSRV_1stapril2025

## Deploy 2 tier webapp in OCP 

<img src="2t.png">

### POd to pod communications 

<img src="p2com.png">

## Creating mysql deployment yaml 

```
 oc create  deployment ashu-mysql --image fiservclass.azurecr.io/mysql:v1  --port 3306 
 --dry-run=client -o yaml >mysql_deploy.yaml

 ==> Some modification are required 
 . storage root creds in secret 

 ===>
  oc  create  secret  generic  ashu-db-creds --from-literal  my-pass=Ashu@12345 --dry-run=client -o yaml >db_cred.yaml 

>>>

apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: ashu-mysql
  name: ashu-mysql
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ashu-mysql
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: ashu-mysql
    spec:
      imagePullSecrets: # to be used by ocp node for image pulling purpose 
      - name: ashu-secret # calling this secret from current project 
      containers:
      - image: fiservclass.azurecr.io/mysql:v1
        name: mysql
        env: # to call any ENV present in container image 
        - name: MYSQL_ROOT_PASSWORD
          valueFrom: # reading value from somewhere 
            secretKeyRef: # calling ocp secret 
              name: ashu-db-creds # name of secret 
              key: my-pass # key of secret 
        ports:
        - containerPort: 3306
        resources: {}
status: {}


===> MYsql service creation to connect mysql pod 

-->
PS C:\Users\labuser\Desktop\ashu-project\ashu-2t-app> oc  get  deploy
NAME            READY   UP-TO-DATE   AVAILABLE   AGE  
amit-mysql      1/1     1            1           7m55s
ashu-mysql      1/1     1            1           7m31s
asif-deploy     0/1     1            0           24m  
jh-mysql        0/1     1            0           9m25s
manoj-mysql     1/1     1            1           7m30s
rayu-mysql      1/1     1            1           5m34s
rohan-mysql     1/1     1            1           7m23s
sandhya-mysql   1/1     1            1           7m9s 
sid-mysql       1/1     1            1           85s  
PS C:\Users\labuser\Desktop\ashu-project\ashu-2t-app> oc  expose deployment  ashu-mysql --type ClusterIP --port 3306 --name ash-db-lb       
  --dry-run=client -o yaml >mysql_svc.yaml
PS C:\Users\labuser\Desktop\ashu-project\ashu-2t-app> oc create -f .\mysql_svc.yaml   
service/ash-db-lb created
PS C:\Users\labuser\Desktop\ashu-project\ashu-2t-app> oc  get  svc
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
ash-db-lb    ClusterIP   172.30.72.231   <none>        3306/TCP   4s 
kubernetes   ClusterIP   172.30.0.1      <none>        443/TCP    83m
PS C:\Users\labuser\Desktop\ashu-project\ashu-2t-app> 


```

### Creating adminer webui deployemnt 

```
oc  create  deployment ashu-web --image fiservclass.azurecr.io/adminer:v1  --port 8080  --dry-run=client -o yaml >ashu_deploy_web.yaml

===> Some modification like updating image pull secrets 

apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: ashu-web
  name: ashu-web
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ashu-web
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: ashu-web
    spec:
      imagePullSecrets:
      - name: ashu-secret
      containers:
      - image: fiservclass.azurecr.io/adminer:v1
        name: adminer
        ports:
        - containerPort: 8080
        resources: {}
status: {}


==>


 oc  create -f .\ashu_deploy_web.yaml
deployment.apps/ashu-web created
PS C:\Users\labuser\Desktop\ashu-project\ashu-2t-app> 

===> creating service for web app

PS C:\Users\labuser\Desktop\ashu-project\ashu-2t-app> oc  get deploy
NAME              READY   UP-TO-DATE   AVAILABLE   AGE
amit-mysql        1/1     1            1           21m
amit-web          1/1     1            1           2m43s
ashu-mysql        1/1     1            1           21m
ashu-web          1/1     1            1           3m39s
asif-deploy       0/1     1            0           37m
asif-web          1/1     1            1           2m28s
jh-mysql          0/1     1            0           22m
manoj-mysql       1/1     1            1           21m
manoj-web         1/1     1            1           3m39s
rayu-mysql        1/1     1            1           19m
rayu-web          1/1     1            1           3m31s
riti-web          1/1     1            1           2m31s
rohan-mysql       1/1     1            1           20m
rohan-web         1/1     1            1           3m33s
sandhya-adminer   1/1     1            1           108s
sandhya-mysql     1/1     1            1           20m
sid-mysql         1/1     1            1           14m
sid-web-app       1/1     1            1           2m9s
trng-mysql        1/1     1            1           12m
PS C:\Users\labuser\Desktop\ashu-project\ashu-2t-app> oc  expose deployment  ashu-web --type ClusterIP --port 8080 --name ashu-web-lb --dry-run=client -o yaml  >ashu-web-svc.yaml
PS C:\Users\labuser\Desktop\ashu-project\ashu-2t-app> oc create -f .\ashu-web-svc.yaml
service/ashu-web-lb created

```
