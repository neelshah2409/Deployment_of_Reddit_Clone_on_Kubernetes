# Deployment_of_Reddit_Clone_on_Kubernetes

## Step 1: Clone the source code
The first step is to clone the source code for the app. You can do this by using this command 
- git clone https://github.com/shahneel2409/Deployment_of_Reddit_Clone_on_Kubernetes.git

## Step 2: Containerize the Application using Docker
Write a Dockerfile with the following code: 
``` 
FROM node:19-alpine3.15

WORKDIR /reddit-clone

COPY . /reddit-clone

RUN npm install 

EXPOSE 3000

CMD ["npm","run","dev"]
```

## Step 3) Building Docker-Image
Now it's time to build Docker Image from this Dockerfile. docker build -t <DockerHub_Username>/<Imagename> . use this command to build a docker image.

## Step 4) Push the Image To DockerHub
Now push this Docker Image to DockerHub so our Deployment file can pull this image & run the app in Kubernetes pods.

First login to your DockerHub account using Command i.e docker login and give your username & password.

Then use docker push <DockerHub_Username>/<Imagename> for pushing to the DockerHub.


## Step 5) Write a Kubernetes Manifest File

When you are going to deploy to Kubernetes or create Kubernetes resources like a pod, replica-set, config map, secret, deployment, etc, you need to write a file called manifest that describes that object and its attributes either in yaml or json. Just like you do in the ansible playbook.

Of course, you can create those objects by using just the command line, but the recommended way is to write a file so that you can version control it and use it in a repeatable way.

### Write Deployment.yml file
Let's Create a Deployment File For our Application. Use the following code for the `Deployment.yml` file.
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: reddit-clone-deployment
  labels:
    app: reddit-clone
spec:
  replicas: 2
  selector:
    matchLabels:
      app: reddit-clone
  template:
    metadata:
      labels:
        app: reddit-clone
    spec:
      containers:
      - name: reddit-clone
        image: trainwithshubham/reddit-clone
        ports:
        - containerPort: 3000

```
### Write Service.yml file
```
apiVersion: v1
kind: Service
metadata:
  name: reddit-clone-service
  labels:
    app: reddit-clone
spec:
  type: NodePort
  ports:
  - port: 3000
    targetPort: 3000
    nodePort: 31000
  selector:
    app: reddit-clone

```
## Step 5) Deploy the app to Kubernetes & Create a Service For It
Now, we have a deployment file for our app and we have a running Kubernetes cluster, we can deploy the app to Kubernetes. To deploy the app you need to run following the command: `kubectl apply -f Deployment.yml` Just Like this create a Service using `kubectl apply -f Service.yml`

If You want to check your deployment & Service use the command `kubectl get deployment` & `kubectl get services`
