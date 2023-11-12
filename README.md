# webapp-docker-k8s

In this assignment, we will deploy a web application on Kubernetes using Docker
containers. We will start by containerizing the application using Docker, create
a persistence volume, and push the image to Docker Hub. Then, deploy the
container on a local Kubernetes cluster using Minikube and then deploy it on AWS
EKS. We will also explore various Kubernetes features such as a replication
controller, health monitoring, rolling updates, and alerting.

## To-do app (Flask + MongoDB)

Fixed some dependency issues in the `requirements.txt` file and updated the code
of the [todo flask](./todo/) app to work.

Create a virtual environment, install the dependencies and run the app
```bash
$ python -m venv .venv
$ source .venv/bin/activate
(.venv) $ cd todo/
(.venv) $ pip install -r requirements.txt
```
```bash
(.venv) $ cd todo/
(.venv) $ python app.py
 * Serving Flask app 'app'
 * Debug mode: on
WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
 * Running on http://127.0.0.1:5000
```

[Install
MongoDB](https://www.mongodb.com/docs/v4.2/tutorial/install-mongodb-on-os-x/)
and run it
```bash
$ brew services start mongodb-community@4.4
==> Successfully started `mongodb-community@4.4` (label: homebrew.mxcl.mongodb-community@4.4)
```

## Docker & Docker Compose

Install [docker (docker desktop) and
docker-compose](https://docs.docker.com/desktop/)

Build the image and run the container
```bash
$ docker build -t todo-flask-mongodb -f ./docker/Dockerfile .
$ docker run -p 5000:5000 todo-flask-mongodb
```

Run the application and the database together using
[docker-compose](https://docs.docker.com/compose/)
```bash
$ docker-compose up
```

Few other docker commands
```bash
# check the images
$ docker images ls

# remove an image
$ docker rmi <image-id>

# check the running containers
$ docker ps

# stop a container
$ docker stop <container-id>

# remove a container
$ docker rm <container-id>

# open a bash shell inside the container
$ docker exec -it <container-id> bash

# check the logs of the container
$ docker logs <container-id>

# authenticate to Docker Hub
$ docker login
```

Few other docker-compose commands
```bash
# check the running containers
$ docker-compose ps

# stop the containers
$ docker-compose down

# remove the volumes
$ docker-compose down -v

# build the images and run the containers
$ docker-compose up --build
```

Create a repository
[vchrombie/todo-flask-mongodb](https://hub.docker.com/repository/docker/vchrombie/todo-flask-mongodb)
on [Docker Hub](https://www.docker.com/products/docker-hub/) and push the image
```bash
$ docker tag todo-flask-mongodb:latest vchrombie/todo-flask-mongodb:latest
$ docker push vchrombie/todo-flask-mongodb:latest
```

## Minikube & Kubernetes

Install [minikube](https://minikube.sigs.k8s.io/docs/start/) and
[kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

```bash
# start minikube
$ minikube start

# check the status
$ minikube status

# connect to LoadBalancer services (has to be running)
$ minikube tunnel
```

Create a
[deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)
and a
[service](https://kubernetes.io/docs/concepts/services-networking/service/) for
the todo-flask app and the mongodb (with pvc)
```bash
# apply the deployment and service files
$ kubectl apply -f k8s/flask-deployment.yml
$ kubectl apply -f k8s/flask-service.yml
$ kubectl apply -f k8s/mongodb-deployment.yml
$ kubectl apply -f k8s/mongodb-service.yml

# apply the persistent volume claim for mongodb
$ kubectl apply -f k8s/mongodb-pvc.yml
```

Get the list of persistent volume claims and pods
```bash
$ kubectl get pvc
$ kubectl get pods
```

Few other kubectl commands
```bash
# check the logs of the pods
$ kubectl logs <pod-name>

# get the list of services and deployments
$ kubectl get services
$ kubectl get deployments

# get the list of all resources
$ kubectl get all

# delete a service or a deployment
$ kubectl delete service <service-name>
$ kubectl delete deployment <deployment-name>

# delete all resources
$ kubectl delete all --all
```

Few other minikube commands
```bash
# check the dashboard
$ minikube dashboard

# stop minikube
$ minikube stop

# delete minikube
$ minikube delete
```

## AWS EKS

Install
[eksctl](https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html)
and [aws
cli](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html)

Create two IAM roles, and assigned the required policies to them.
- `eksClusterRole`
    - `AmazonEKSClusterPolicy`
    - `AmazonEC2ContainerRegistryReadOnly`
    - `AmazonEKSWorkerNodePolicy`
    - `eksFullAccess`
- `eksClusterNodeGroupsRole`
    - `AmazonEC2ContainerRegistryReadOnly`
    - `AmazonEKSWorkerNodePolicy`
    - `AmazonEC2FullAccess`

Create an EKS cluster using `eksctl`
```bash
$ eksctl create cluster --name todo-flask-mongodb --region us-east-1 --nodegroup-name standard-workers --node-type t2.medium --nodes 1
```

Attatch the `eksClusterRole` to the cluster and `eksClusterNodeGroupsRole` to
the node group using the AWS console.

Configure `kubectl` to use the cluster
```bash
$ aws eks update-kubeconfig --name todo-flask-mongodb --region us-east-1
```

Apply the deployments for the todo-flask app and the mongodb
```bash
$ kubectl apply -f k8s/flask-deployment.yml
$ kubectl apply -f k8s/mongodb-deployment.yml
```

Apply the services for the todo-flask app (with LoadBalancer) and the mongodb
(with pvc)
```bash
$ kubectl apply -f k8s/flask-service.yml
$ kubectl apply -f k8s/mongodb-service.yml
```

Apply the persistent volume claim for mongodb
```bash
$ kubectl apply -f k8s/mongodb-pvc.yml
```

> `mongodb-pvc` is waiting for a volume to be created by the external
> provisioner `ebs.csi.aws.com`. This indicates that the AWS EBS CSI driver is
> not installed on the cluster.
```bash
# install the AWS EBS CSI driver
$ kubectl apply -k "github.com/kubernetes-sigs/aws-ebs-csi-driver/deploy/kubernetes/overlays/stable/ecr/"

# verify if the driver is installed
$ kubectl get pods -n kube-system | grep ebs-csi

# check the drivers installed on the cluster
$ kubectl get csidrivers
```

Get the list of persistent volume claims and their status
```bash
$ kubectl get pvc
```

> `todo-flask` pods are in a `CrashLoopBackOff` status, which is related to an
> architecture mismatch between the image and the host. Built the image which
> supports multi-architecture builds and pushed it to Docker Hub.
```bash
# create a new builder which supports multi-architecture builds
$ docker buildx create --name mybuilder --use
# start up the builder instance
$ docker buildx inspect --bootstrap

# build and push the image with the new builder
$ docker buildx build --platform linux/amd64,linux/arm64 -t vchrombie/todo-flask-mongodb:latest -f ./docker/Dockerfile . --push
```

Get the LoadBalancer URL
```bash
$ kubectl get svc
```

Few other required commands
```bash
# get the status of the cluster
$ eksctl get cluster --name <cluster-name> --region <region>

# get the list of nodes and their status
$ kubectl get nodes

# get the list of pods and their status
$ kubectl get pods
```

## Replication Controller

Create a [replication
controller](https://kubernetes.io/docs/concepts/workloads/controllers/replicationcontroller/)
for the todo-flask app
```bash
$ kubectl apply -f k8s/flask-rc.yml
```

Initial number of replicas can be set in the `flask-rc.yml` file
```yaml
spec:
  replicas: 1
```

Scale the number of replicas
```bash
$ kubectl scale rc todo-flask-rc --replicas 0
$ kubectl scale rc todo-flask-rc --replicas 2
```

Few other commands
```bash
# get the list of replication controllers
$ kubectl get rc

# delete a replication controller
$ kubectl delete rc <rc-name>

# get the list of pods
$ kubectl get pods

# delete a pod
$ kubectl delete pod <pod-name>
```

## Rolling Update Strategy

Update the deployment for the todo-flask app to include the [rolling update
configuration](https://kubernetes.io/docs/tutorials/kubernetes-basics/update/update-intro/)
```yaml
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 1
      maxSurge: 1
```

Apply the deployment
```bash
$ kubectl apply -f k8s/flask-deployment.yml
```

Updated the css to test the rolling update
```
diff --git a/todo/static/assets/style.css b/todo/static/assets/style.css
-    background-color: #333;
+    background-color: #0000ff;
```

```bash
# built the image, pushed it to docker hub
$ docker buildx build --platform linux/amd64,linux/arm64 -t vchrombie/todo-flask-mongodb:v2 -f ./docker/Dockerfile . --push

# update the deployment to use the new image
$ kubectl set image deployment/todo-flask todo-flask=vchrombie/todo-flask-mongodb:v2

# check the rollout status
$ kubectl rollout status deployment/todo-flask

# verify if the new image is running
$ kubectl describe pods | grep Image
```

Few other commands
```bash
# check the rollout history
$ kubectl rollout history deployment/<deployment-name>

# undo the rollout
$ kubectl rollout undo deployment/<deployment-name>

# rollback to a specific revision
$ kubectl rollout undo deployment/<deployment-name> --to-revision=<revision-number>
```

## Health Monitoring

Update the deployment for the todo-flask app to include the [liveness
probe](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)
```yaml
    spec:
      containers:
      - name: todo-flask
        . . .
        livenessProbe:
          httpGet:
            path: /healthz
            port: 5000
          initialDelaySeconds: 15
          timeoutSeconds: 2
          periodSeconds: 5
          failureThreshold: 3
        readinessProbe:
          httpGet:
            path: /readiness
            port: 5000
          initialDelaySeconds: 5
          timeoutSeconds: 2
          periodSeconds: 5
          successThreshold: 1
```

Apply the deployment
```bash
$ kubectl apply -f k8s/flask-deployment.yml
```

Update the code of the [todo-flask app](./todo/app.py) to simulate the verification of the liveness and readiness

Both the liveness and readiness endpoints return a `200 OK` response \
docker image `vchrombie/todo-flask-mongodb:live-ready`
```py
@app.route('/healthz')
def healthz():
    return Response("OK", status=200)

@app.route('/readiness')
def readiness():
    return Response("OK", status=200)
```

```bash
$ docker buildx build --platform linux/amd64,linux/arm64 -t vchrombie/todo-flask-mongodb:live-ready -f ./docker/Dockerfile . --push

$ kubectl set image deployment/todo-flask todo-flask=vchrombie/todo-flask-mongodb:live-ready
```

The liveness endpoint returns a `500 Internal Server Error` response \
docker image `vchrombie/todo-flask-mongodb:check-liveliness`
```py
@app.route('/healthz')
def healthz():
    return Response("Internal Server Error", status=500)

@app.route('/readiness')
def readiness():
	return Response("OK", status=200)
```

The readiness endpoint returns a `500 Internal Server Error` response \
docker image `vchrombie/todo-flask-mongodb:check-readiness`
```py
@app.route('/healthz')
def healthz():
    return Response("OK", status=200)

@app.route('/readiness')
def readiness():
	return Response("Internal Server Error", status=500)
```

Few other commands
```bash
# check for liveliness and readiness
$ kubectl describe pods | grep -i live
$ kubectl describe pods | grep -i ready

# check the available endpoints
$ kubectl describe svc | grep Endpoints
```
