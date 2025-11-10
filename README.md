# Yolo Full-Stack Application Deployment on Kubernetes

- This repository contains the necessary manifest files and configurations to deploy the Yolo full-stack application (client, backend API, and MongoDB database) using Kubernetes.

## Project Overview

The Yolo application is designed as a classic three-tier architecture orchestrated entirely by Kubernetes:

Frontend (Client): A web application responsible for the user interface and cart interactions.

Backend (API): A Node.js/Express API that handles business logic and manages connections to the database.

Database (MongoDB): A non-relational database used for persistent data storage (e.g., cart items).

## Architecture and Kubernetes Objects

Component

Kubernetes Object

Purpose

Key Feature

MongoDB

StatefulSet & PersistentVolume

Ensures a stable network identity and state for the database.

Guarantees data persistence (e.g., cart items survive pod restarts).

Backend API

Deployment & Service

Manages containerized Node.js application replicas and exposes the internal API port.

Final stable configuration uses jjumaaa/yolo-backend:v1.0.3.

Frontend Client

Deployment & Service

Manages client application replicas and exposes the application to the network.

Handles user interaction and communicates with the Backend Service.

### Prerequisites

To deploy this application, you must have the following set up:

A functional Kubernetes cluster (e.g., Minikube, GKE, EKS).

kubectl configured to connect to your cluster.

### Deployment Guide

## Follow these steps to deploy the application and its dependencies:

1. Deploy MongoDB (Database)

Deploy the StatefulSet and associated headless service to ensure data persistence and stable networking for the database:

kubectl apply -f k8s/01-mongodb-statefulset.yaml


Wait until the MongoDB pod is fully running:

kubectl get pods
# Status should show: mongo-statefulset-0   1/1   Running


2. Deploy Backend API

Deploy the API layer. Note the critical fixes in the final configuration (Image tag v1.0.3 and probe path /):

kubectl apply -f k8s/02-backend-deployment.yaml


Verify that the backend pods stabilize and become ready:

kubectl get pods
# Status should show: yolo-backend-deployment-...   1/1   Running


3. Deploy Frontend Client

Deploy the user interface layer:

kubectl apply -f k8s/03-client-deployment.yaml


Verify that the client pods are running and ready:

kubectl get pods
# Status should show: yolo-client-deployment-...   1/1   Running


⚠️ Critical Troubleshooting Notes

The final stable deployment was achieved by addressing two major issues:

Dependency Issue (v1.0.1 image crash): The original deployment image failed due to missing dependencies. This was resolved by migrating to the fully functional image: jjumaaa/yolo-backend:v1.0.3.

Health Check Failure: Both /health and /api/health endpoints resulted in a 404 error, causing the pods to enter CrashLoopBackOff. This was finally resolved by setting the Liveness and Readiness probe paths to the application's root path: path: /.