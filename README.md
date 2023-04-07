## Dockerizing the App

Use multi-stage builds for efficient docker images. Building efficient Docker images are very important for faster downloads and lesser surface attacks. In this multi-stage build, building a React app and put those static assets in the build folder is the first step. The second step involves taking those static build files and serve those with node server.

## Stage 1

Start from the base image node:10

There are two package.json files: one is for nodejs server and another is for React UI. We need to copy these into the Docker file system and install all the dependencies.

We need this step first to build images faster in case there is a change in the source later. We don’t want to repeat installing dependencies every time we change any source files.

Copy all the source files.

Install all the dependencies.

Run npm run build to build the React App and all the assets will be created under build a folder within a my-app folder.

## Stage 2

Start from the base image node:10

Take the build from stage 1 and copy all the files into ./my-app/build folder.

Copy the nodejs package.json into ./api folder

Install all the dependencies

Finally, copy the server.js into the same folder

Have this command node ./api/server.js with the CMD. This automatically runs when we run the image.


## Nodejs-React Application Architecture Design
![image](https://user-images.githubusercontent.com/59709429/230519489-32d977b4-889d-40d2-b848-a64f465ec85e.png)


Technology stack:
- Nodejs
- React
- MySQL
- Docker
- Terraform
- Bash Scripting 
- AWS ( EKS, EC2 NODE GROUPS, ECR, IAM, SECURITY GROUP, ALB VPC, EC2(Jenkins server)and CLOUDWATCH(logs and metrics))

## Continuous deployment to Kubernetes cluster using ArgoCD and Helm Chart 

## Best practice for Git Repository 
1. Application source code in different repository - https://github.com/lanru2001/nodejs-react-user-mgt-app
2. Application configuration ( k8s manifest file) in another repository.

## Install argocd on EKS Cluster

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```
##  kubectl get all -n argocd

```bash

Azeez.Olanrewaju@DCITSYS01-L argocd-istio % kubectl get all -n argocd
NAME                                                   READY   STATUS    RESTARTS   AGE
pod/argocd-application-controller-0                    1/1     Running   0          53s
pod/argocd-applicationset-controller-f5f44f5d9-vfd9j   1/1     Running   0          54s
pod/argocd-dex-server-7cd84cf844-s6xvn                 1/1     Running   0          54s
pod/argocd-notifications-controller-b7fcb9bdb-9f6jb    1/1     Running   0          54s
pod/argocd-redis-7dd84c47d6-w6lqd                      1/1     Running   0          53s
pod/argocd-repo-server-7c5579d46d-8ff2f                1/1     Running   0          53s
pod/argocd-server-6d65f8d8f5-tqjvx                     1/1     Running   0          53s

NAME                                              TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                      AGE
service/argocd-applicationset-controller          ClusterIP   172.20.168.45    <none>        7000/TCP,8080/TCP            55s
service/argocd-dex-server                         ClusterIP   172.20.152.8     <none>        5556/TCP,5557/TCP,5558/TCP   55s
service/argocd-metrics                            ClusterIP   172.20.27.228    <none>        8082/TCP                     55s
service/argocd-notifications-controller-metrics   ClusterIP   172.20.203.12    <none>        9001/TCP                     54s
service/argocd-redis                              ClusterIP   172.20.34.34     <none>        6379/TCP                     54s
service/argocd-repo-server                        ClusterIP   172.20.136.164   <none>        8081/TCP,8084/TCP            54s
service/argocd-server                             ClusterIP   172.20.224.183   <none>        80/TCP,443/TCP               54s
service/argocd-server-metrics                     ClusterIP   172.20.188.174   <none>        8083/TCP                     54s

NAME                                               READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/argocd-applicationset-controller   1/1     1            1           54s
deployment.apps/argocd-dex-server                  1/1     1            1           54s
deployment.apps/argocd-notifications-controller    1/1     1            1           54s
deployment.apps/argocd-redis                       1/1     1            1           53s
deployment.apps/argocd-repo-server                 1/1     1            1           53s
deployment.apps/argocd-server                      1/1     1            1           53s

NAME                                                         DESIRED   CURRENT   READY   AGE
replicaset.apps/argocd-applicationset-controller-f5f44f5d9   1         1         1       54s
replicaset.apps/argocd-dex-server-7cd84cf844                 1         1         1       54s
replicaset.apps/argocd-notifications-controller-b7fcb9bdb    1         1         1       54s
replicaset.apps/argocd-redis-7dd84c47d6                      1         1         1       53s
replicaset.apps/argocd-repo-server-7c5579d46d                1         1         1       53s
replicaset.apps/argocd-server-6d65f8d8f5                     1         1         1       53s

NAME                                             READY   AGE
statefulset.apps/argocd-application-controller   1/1     53s
Azeez.Olanrewaju@DCITSYS01-L argocd-istio % 

```
# Add a namespace label to instruct Istio to automatically inject Envoy sidecar proxies when you deploy your application later
```bash
kubectl label namespace argocd  istio-injection=enabled
kubectl get namespace -L istio-injection

```

# Tells argocd-server to start in “insecure mode” and patched argocd-server deployment

```bash

# kubectl edit cm argocd-cmd-params-cm
# and add server.insecure option

data:
  server.insecure: "true"
  
Then restart the argocd-server deployment
```

# Download istio
```bash 
curl -L https://istio.io/downloadIstio | sh -
# Move to the Istio package directory. For example, if the package is istio-1.16.2:
cd istio-1.16.2

# Add the istioctl client to your path (Linux or macOS)
export PATH=$PWD/bin:$PATH

# Istio installation
istioctl install 

```
# Deploy istio Gateway and Virtualservice

```bash
kubectl apply -f istio-gateway.yml   
kubectl apply -f istio-virtualservice.yml 

kubectl get gateway -n argocd 
kubectl get virtualservice -n argocd 

```

Using Istio Gateway to Route Traffic to Microservices on Amazon EKS - https://www.youtube.com/watch?v=_ImVPrUZ6yY
