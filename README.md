# Todo API -- Kubernetes Deployment Overview
This project demonstrates how to deploy a full backend application onto a Kubernetes cluster using Minikube. The application is a **TypeScript Node.js REST API** that allows users to **add and retrieve todo items.**
A PostgreSQL database is used as the data layer, with a SQL initialization script that automatically creates a todo table on first boot.

The Kubernetes setup includes:
* A PostgreSQL Deployment, Service, Persistent Volume Claim, and initialization ConfigMap
* A Backend Deployment and Service that expose the API
* A Secret created from a postgres.env file to store database credentials
* Instructions to start, test, and tear down all Kubernetes resources

This guide provides all the commands needed to fully deploy, test, and clean up the application in a Kubernetes environment. All of the instructions are below:


## Get database credentials/secrets from postgres.env file:
`kubectl create secret generic postgres-secret --from-env-file=kubernetes/postgres.env --dry-run=client -o yaml | kubectl apply -f -`

### Check to see if database credentials were created 
`kubectl get secret postgres-secret`

## Config for first-boot SQL:
`kubectl apply -f="kubernetes/postgres-init-config.yaml"`

## Persistent storage (PVC):
`kubectl apply -f="kubernetes/pg-pvc.yaml"`

## Bring up Postgres service
`kubectl apply -f="kubernetes/postgres-service.yaml"`
`kubectl apply -f="kubernetes/postgres-deployment.yaml"`

## Bring up backend service
`kubectl apply -f="kubernetes/backend-service.yaml"`
`kubectl apply -f="kubernetes/backend-deployment.yaml"`

## Start service (in a new terminal)
`minikube service backend-service`

## Retrieve the url generated from `minikube service backend-service` and test backend api in postman:
### Check to see if API is running:
GET {minikube-service-url}/
### Get all todo items:
GET {minikube-service-url}/api/todos 
### Add a todo item:
POST {minikube-service-url}/api/todos
* Body of post request: `{ "title": "Go to the Gym", "text": ""}`


## Delete everything:

### Deployments
`kubectl delete -f="kubernetes/backend-deployment.yaml"`
`kubectl delete -f="kubernetes/postgres-deployment.yaml"`

### Services:
`kubectl apply -f="kubernetes/backend-service.yaml"`
`kubectl apply -f="kubernetes/postgres-service.yaml"`

### Delete ConfigMap and Secret (optional cleanup)
`kubectl delete configmap postgres-init-sql --ignore-not-found`
`kubectl delete secret postgres-secret --ignore-not-found`