# webapp-docker-k8s

## To-do app (Flask + MongoDB)

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
$ docker build -t todo-flask-mongodb .
$ docker run -p 5000:5000 todo-flask-mongodb
```

Run the application and the database together using docker-compose
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
on Docker Hub and push the image
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
