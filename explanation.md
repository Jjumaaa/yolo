# DevOps Engineering Project: Microservice Deployment
- This document provides a detailed explanation of the infrastructure choices, file directives, and deployment strategies used to containerize and orchestrate the React Frontend, Node.js Backend, and MongoDB Database microservices.

1. ### Image Selection and Dockerfile Directives
The core objective of minimizing image size while maintaining functionality was achieved through careful base image selection and the use of Multi-Stage Builds.

#### A. Base Image Choices
Service	Base Image	Rationale	Objective Met
Frontend (Client)	node:20-alpine (Builder) & nginx:alpine (Final)	Uses the minimal Alpine variants. Nginx efficiently serves the static files produced by the build, keeping the final image extremely small.	Image Selection & Size
Backend	node:20-alpine	Provides a minimal environment necessary to run the Node.js server.	Image Selection & Size
Database	mongo:4.0	Explicitly used the older MongoDB 4.0 image version instead of the latest, which significantly reduces the final image size and helps meet the assignment's overall size constraint (sub-400MB).	Image Selection & Size

#### B. Multi-Stage Builds
Both the client/Dockerfile and backend/Dockerfile utilize multi-stage builds.

- Stage 1 (as builder): Uses the heavier node:20-alpine image to install development dependencies and run npm run build (for the frontend).

- Stage 2 (Final): For the frontend, it starts FROM nginx:alpine and uses COPY --from=builder to transfer only the static production files (/app/build). For the backend, it copies only the source code and installed production dependencies.

This technique prevents unnecessary build artifacts (like node_modules and compiler tools) from being included in the final image, ensuring a minimal footprint.

#### C. Critical Fix: OpenSSL Legacy Provider
- During the build process for the frontend, an error (ERR_OSSL_EVP_UNSUPPORTED) occurred due to compatibility issues between older React Scripts and the newer OpenSSL 3.0 used by Node.js 20. This was fixed by adding a single environment variable to the build step:

## Dockerfile

RUN NODE_OPTIONS=--openssl-legacy-provider npm run build 
This demonstrates the practice of troubleshooting and adapting the build process for stability.

2. ### Docker Compose Orchestration
The docker-compose.yaml file defines the three microservices and the infrastructure required for them to communicate and persist data.

#### A. Docker Compose Networking
- A custom bridge network named app-net was created and assigned to all three services.

Service Resolution: The Node.js backend connects to the MongoDB database using the service name defined in the YAML: app-ip-mongo.

Connectivity: The backend's environment variable was set as follows to ensure successful connection:

YAML

environment:
  MONGO_URI: mongodb://app-ip-mongo:27017/yolomy 
B. Docker Compose Volume Definition and Usage
Data persistence for the database was achieved using a named volume defined in docker-compose.yaml:

YAML

volumes:
  app-mongo-data:
    driver: local 
# ...
services:
  app-ip-mongo:
    volumes:
      - app-mongo-data:/data/db # Mount point inside the container
This ensures that the database data is stored outside the container filesystem. Running docker compose down and docker compose up -d confirmed the data remained intact, proving successful persistence.

#### C. Application Running and Port Allocation
The application is deployed using the command: docker compose up -d.

Frontend Access: The frontend (Client) is exposed on Host Port 80 ("80:80"), allowing access via http://localhost/.

Backend Access: The backend is exposed on Host Port 5000 ("5000:5000").

3. Image Deployment and Git Workflow
A. Image Versioning and Deployment
All custom-built images were tagged using Semantic Versioning (v1.0.0) and successfully pushed to the public Docker Hub repository of jjumaaa.

jjumaaa/brian-yolo-client:v1.0.0

jjumaaa/brian-yolo-backend:v1.0.0

Proof of Image Versioning and Deployment on Docker Hub:

![alt text](<Screenshot from 2025-10-13 01-40-44.png>)