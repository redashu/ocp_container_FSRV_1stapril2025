# Revision 

## app to container process 

<img src="rev1.png">

## k8s app deployment process 

<img src="rev2.png">

## every app server is having some config to enable SSL setting 

<img src="ssl.png">

## Keeping openshift router based domain to deploy custom pod app 

<img src="ssl1.png">

## build image and create container to access app using https 

```
 7 cd  .\ssl_ocp_nginx\
   8 ls
   9 docker build  -t  ashussl:v1  .
  10 docker run -itd --name ashuc1 -p 1122:443   ashussl:v1
  11 docker  ps

```
