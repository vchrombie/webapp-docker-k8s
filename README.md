# webapp-docker-k8s

In this assignment, we will deploy a web application on Kubernetes using Docker
containers. We will start by containerizing the application using Docker, create
a persistence volume, and push the image to Docker Hub. Then, deploy the
container on a local Kubernetes cluster using Minikube and then deploy it on AWS
EKS. We will also explore various Kubernetes features such as a replication
controller, health monitoring, rolling updates, and alerting.

## Steps

- [x] [To-do app (Flask + MongoDB)](steps.md#to-do-app-flask--mongodb)
- [x] [Docker & Docker Compose](steps.md#docker--docker-compose)
- [x] [Minikube & Kubernetes](steps.md#minikube--kubernetes)
- [x] [AWS EKS](steps.md#aws-eks)
- [x] [Replication Controller](steps.md#replication-controller)
- [x] [Rolling Update Strategy](steps.md#rolling-update-strategy)
- [x] [Health Monitoring](steps.md#health-monitoring)
- [ ] Alerting

You can check the [steps.md](./steps.md) for a detailed walkthrough of the
steps.

## Screenshots

Checkout the [screenshots.md](./screenshots.md) for a detailed walkthrough of
the project.

## Tools

- [Flask](https://flask.palletsprojects.com/en/1.1.x/) & [MongoDB](https://www.mongodb.com/)
- [Docker](https://www.docker.com/)
- [Docker Compose](https://docs.docker.com/compose/)
- [Docker Hub](https://hub.docker.com/)
- [Minikube](https://minikube.sigs.k8s.io/docs/)
- [Kubernetes](https://kubernetes.io/)
- [`kubectl`](https://kubernetes.io/docs/reference/kubectl/kubectl/)
- [AWS EKS](https://aws.amazon.com/eks/)
- [AWS CLI](https://aws.amazon.com/cli/)
- [`eksctl`](https://eksctl.io/)
- [AWS IAM](https://aws.amazon.com/iam/)
- [AWS CloudFormation](https://aws.amazon.com/cloudformation/)
