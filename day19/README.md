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
          fromValue: # reading value from somewhere 
            secretRef: # calling ocp secret 
              name: ashu-db-creds # name of secret 
              key: my-pass # key of secret 
        ports:
        - containerPort: 3306
        resources: {}
status: {}

```