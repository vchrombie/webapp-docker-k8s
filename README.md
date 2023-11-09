# webapp-docker-k8s

## To-do app (Flask + MongoDB)

Create a virtual environment, install the dependencies and run the app
```bash
$ python -m venv .venv
$ source .venv/bin/activate
$ pip install -r requirements.txt
```
```bash
$ python app.py
 * Serving Flask app 'app'
 * Debug mode: on
WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
 * Running on http://127.0.0.1:5000
```

[Install MongoDB](https://www.mongodb.com/docs/v4.2/tutorial/install-mongodb-on-os-x/) and run it
```bash
$ brew services start mongodb-community@4.4
==> Successfully started `mongodb-community@4.4` (label: homebrew.mxcl.mongodb-community@4.4)
```

## Docker & Docker Compose

Install [docker (docker desktop) and docker-compose](https://docs.docker.com/desktop/)

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

# open a bash shell inside the container
$ docker exec -it <container-id> bash

# check the logs of the container
$ docker logs <container-id>

# authenticate to Docker Hub
$ docker login
```

Create a repository [vchrombie/todo-flask-mongodb](https://hub.docker.com/repository/docker/vchrombie/todo-flask-mongodb) on Docker Hub and push the image
```bash
$ docker tag todo-flask-mongodb:latest vchrombie/todo-flask-mongodb:latest
$ docker push vchrombie/todo-flask-mongodb:latest
```
