# Containerization of the application

Use multi-stage builds for efficient docker images. Building efficient Docker images are very important for faster downloads and lesser surface attacks. In this multi-stage build, building a React app and put those static assets in the build folder is the first step. The second step involves taking those static build files and serve those with node server.

# Stage 1

Start from the base image node:10

There are two package.json files: one is for nodejs server and another is for React UI. We need to copy these into the Docker file system and install all the dependencies.

We need this step first to build images faster in case there is a change in the source later. We don’t want to repeat installing dependencies every time we change any source files.

Copy all the source files.

Install all the dependencies.

Run npm run build to build the React App and all the assets will be created under build a folder within a my-app folder.

# Stage 2

Start from the base image node:10

Take the build from stage 1 and copy all the files into ./my-app/build folder.

Copy the nodejs package.json into ./api folder

Install all the dependencies

Finally, copy the server.js into the same folder

Have this command node ./api/server.js with the CMD. This automatically runs when we run the image.

Technology stack:
- Nodejs
- React
- MySQL
- Docker
- Terraform
- Istio
- Bash Scripting 
- AWS ( EKS, EC2 NODE GROUPS, ECR, IAM, SECURITY GROUP, ALB VPC, EC2(Jenkins server)and CLOUDWATCH(logs and metrics))

## Continuous deployment to Kubernetes cluster using ArgoCD and Helm Chart 

## Best practice for Git Repository 
1. Application source code in different repository - https://github.com/lanru2001/nodejs-react-user-mgt-app
2. Application configuration ( k8s manifest file) in another repository.

Using Istio Gateway to Route Traffic to Microservices on Amazon EKS - https://www.youtube.com/watch?v=_ImVPrUZ6yY
