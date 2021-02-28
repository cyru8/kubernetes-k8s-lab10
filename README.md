# kubernetes-k8s-lab10
In this scenario you will how to deploy existing Docker Compose files to Kubernetes using Kompose.

Kompose is a tool to help users familiar with docker-compose move to Kubernetes. It takes a Docker Compose file and translates it into Kubernetes resources. More details can be found at http://kompose.io/

######

Step 1 - Install

Kompose is deployed as a binary onto a client. To install Kompose on Katacoda, run the command curl -L https://github.com/kubernetes/kompose/releases/download/v1.9.0/kompose-linux-amd64 -o /usr/bin/kompose && chmod +x /usr/bin/kompose

Details on how to install Kompose for your OS can be found at https://github.com/kubernetes/kompose/releases

######

Step 2 - Up

Kompose takes existing Docker Compose files and enables them to be deployed onto Kubernetes. Compose is a tool for defining and running multi-container Docker applications. With Compose, you use a Compose file to configure your application’s services.
Task

Copy an example Docker Compose file to the editor.

Copy to Editorversion: "2"
services:
  redis-master:
    image: redis:latest
    ports:
      - "6379"
  redis-slave:
    image: gcr.io/google_samples/gb-redisslave:v1
    ports:
      - "6379"
    environment:
      - GET_HOSTS_FROM=dns
  frontend:
    image: gcr.io/google-samples/gb-frontend:v3
    ports:
      - "80:80"
    environment:
      - GET_HOSTS_FROM=dns

As with Docker Compose, Kompose allows the Images to be deployed using a single command of kompose up

The details of what has been deployed can be discovered with the Kubernetes CLI kubectl.

kubectl get deployment,svc,pods,pvc

controlplane $ kompose up
INFO We are going to create Kubernetes Deployments, Services and PersistentVolumeClaims for your Dockerized application. If you need different kind of resources, use the 'kompose convert' and 'kubectl create -f' commands instead. 
 
INFO Deploying application in "default" namespace 
INFO Successfully created Service: frontend       
INFO Successfully created Service: redis-master   
INFO Successfully created Service: redis-slave    
INFO Successfully created Deployment: frontend    
INFO Successfully created Deployment: redis-master 
INFO Successfully created Deployment: redis-slave 

Your application has been deployed to Kubernetes. You can run 'kubectl get deployment,svc,pods,pvc' for details.
controlplane $ kubectl get deployment,svc,pods,pvc
NAME                                 READY   UP-TO-DATE   AVAILABLE   AGE
deployment.extensions/frontend       1/1     1            1           87s
deployment.extensions/redis-master   1/1     1            1           87s
deployment.extensions/redis-slave    1/1     1            1           87s

NAME                   TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
service/frontend       ClusterIP   10.105.106.220   <none>        80/TCP     87s
service/kubernetes     ClusterIP   10.96.0.1        <none>        443/TCP    74m
service/redis-master   ClusterIP   10.106.57.64     <none>        6379/TCP   87s
service/redis-slave    ClusterIP   10.104.197.242   <none>        6379/TCP   87s

NAME                               READY   STATUS    RESTARTS   AGE
pod/frontend-64bcc8dd75-tsn5m      1/1     Running   0          87s
pod/redis-master-fc59d57fd-4npfp   1/1     Running   0          87s
pod/redis-slave-8676777656-c4q5t   1/1     Running   0          87s
controlplane $ 
######

Step 3 - Convert

Kompose also has the ability to take existing Compose files and generate the related Kubernetes Manifest files.

The command "kompose convert" will generate the files, viewable via 

kubernetes-k8s-lab10[main !?] $ kompose convert
INFO Kubernetes file "frontend-service.yaml" created 
INFO Kubernetes file "redis-master-service.yaml" created 
INFO Kubernetes file "redis-slave-service.yaml" created 
INFO Kubernetes file "frontend-deployment.yaml" created 
INFO Kubernetes file "redis-master-deployment.yaml" created 
INFO Kubernetes file "redis-slave-deployment.yaml" created 
kubernetes-k8s-lab10[main !?] $ 

ls.
######

Step 4 - Kubectl create

With the files converted, they too can be deployed using Kubectl. This will match the existing deployment applied via kompose up.

kubectl apply -f frontend-service.yaml,redis-master-service.yaml,redis-slave-service.yaml,frontend-deployment.yaml,redis-master-deployment.yaml,redis-slave-deployment.yaml

######
Step 5 - OpenShift

Kompose also supports different Kubernetes distributions, for example OpenShift.

kompose --provider openshift convert

kubernetes-k8s-lab10[main !?] $ kompose --provider openshift convert
INFO OpenShift file "frontend-service.yaml" created 
INFO OpenShift file "redis-master-service.yaml" created 
INFO OpenShift file "redis-slave-service.yaml" created 
INFO OpenShift file "frontend-deploymentconfig.yaml" created 
INFO OpenShift file "frontend-imagestream.yaml" created 
INFO OpenShift file "redis-master-deploymentconfig.yaml" created 
INFO OpenShift file "redis-master-imagestream.yaml" created 
INFO OpenShift file "redis-slave-deploymentconfig.yaml" created 
INFO OpenShift file "redis-slave-imagestream.yaml" created 


######

Step 6 - Convert To Json

By default, Kompose generates YAML files. It's possible to generate JSON based files by specifying the -j parameter.

kompose convert -j

kubernetes-k8s-lab10[main !?] $ kompose convert -j
INFO Kubernetes file "frontend-service.json" created 
INFO Kubernetes file "redis-master-service.json" created 
INFO Kubernetes file "redis-slave-service.json" created 
INFO Kubernetes file "frontend-deployment.json" created 
INFO Kubernetes file "redis-master-deployment.json" created 
INFO Kubernetes file "redis-slave-deployment.json" created 

######