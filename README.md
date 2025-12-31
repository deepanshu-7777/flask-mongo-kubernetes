📌 Project Overview

This project demonstrates deploying a Python Flask application connected to MongoDB on a Kubernetes cluster using Minikube.
The setup includes containerization, secure database authentication, persistent storage, internal/external services, resource management, and Horizontal Pod Autoscaling (HPA).

🧰 Tech Stack

Backend: Python (Flask)

Database: MongoDB

Containerization: Docker

Orchestration: Kubernetes (Minikube)

Autoscaling: Horizontal Pod Autoscaler (HPA)

📁 Project Structure
flask-mongo-k8s/
│
├── app.py
├── requirements.txt
├── Dockerfile
├── README.md
│
├── screenshots/
│   ├── hpa_scaling.png
│   ├── pods_scaling.png
│   ├── app_running.png
│   ├── load_generator.png
|
└── k8s/
    ├── mongo-secret.yaml
    ├── mongo-statefulset.yaml
    ├── mongo-service.yaml
    ├── flask-deployment.yaml
    ├── flask-service.yaml
    └── hpa.yaml

⚙️ Application Features

GET /
Returns a welcome message with the current timestamp.

GET /data
Fetches data from MongoDB.

POST /data
Inserts JSON data into MongoDB.

🐳 Docker Setup
Dockerfile

The Flask application is containerized using Python 3.9 slim image.

Build Image
docker build -t deepu778/flask-mongo:1.0 .

Push Image
docker push deepu778/flask-mongo:1.0

☸️ Kubernetes Deployment
1️⃣ Start Minikube
minikube start --driver=docker
minikube addons enable metrics-server

2️⃣ MongoDB Setup
MongoDB Authentication (Secret)

Credentials are stored securely using Kubernetes Secrets.

MongoDB StatefulSet

MongoDB is deployed as a StatefulSet to ensure stable identity.

Authentication is enabled using environment variables.

Resource requests and limits are configured.

MongoDB Service

Exposed internally using ClusterIP service.

Accessible only inside the cluster.

3️⃣ Flask Application Deployment

Deployed using Deployment with 2 replicas.

Connected to MongoDB using Kubernetes DNS.

Resource requests and limits applied.

Flask Service

Exposed using NodePort service.

Accessible from local machine via Minikube.

Access the application:

minikube service flask-service

🌐 DNS Resolution in Kubernetes

Kubernetes provides an internal DNS system.
Services can be accessed using their service name.

Example:

mongodb://admin:password@mongo:27017


Here:

mongo → MongoDB service name

Kubernetes DNS resolves it to the MongoDB pod IP automatically

This enables reliable inter-pod communication without hardcoded IPs.

📦 Persistent Storage

MongoDB data is persisted using Kubernetes volumes.

This ensures data is not lost when pods restart.

📊 Resource Requests & Limits

Configured for both Flask and MongoDB:

requests:
  cpu: 0.2
  memory: 250Mi
limits:
  cpu: 0.5
  memory: 500Mi

Why this is important:

Requests guarantee minimum resources.

Limits prevent resource overuse.

Ensures cluster stability and fair resource usage.

📈 Autoscaling (HPA)
Configuration

Minimum replicas: 2

Maximum replicas: 5

Scaling metric: CPU utilization

Threshold: 70%

HPA YAML

Horizontal Pod Autoscaler is configured to automatically scale Flask pods based on CPU load.

🔥 Autoscaling Testing
Load Generation

A BusyBox pod was used to generate continuous traffic:

while true; do wget -q -O- http://flask-service:5000; done

Observations

CPU utilization crossed 70%

HPA increased Flask replicas from 2 to multiple pods

Verified using:

kubectl get hpa
kubectl get pods


📸 Screenshots included in submission show:

HPA CPU metrics

Increased number of Flask pods

Running application in browser during load

📸 Screenshots

![HPA Scaling](screenshots/hpa_scaling.png)
![Pods Scaling](screenshots/pods_scaling.png)
![Application Running](screenshots/app_running.png)
![Load Generation](screenshots/load_generator.png)


🧪 Testing Scenarios

Verified database insert & fetch operations

Tested service accessibility from browser

Tested autoscaling under high traffic

Observed automatic scale-down after stopping load

🏗 Design Choices

StatefulSet for MongoDB: ensures stable storage & identity

Deployment for Flask: supports easy scaling

ClusterIP for MongoDB: secures database from external access

NodePort for Flask: simple local access via Minikube

HPA: ensures application scalability under load

⚠️ Issues Faced & Resolution

BusyBox pod CrashLoopBackOff:
Resolved by running it with a long-lived sleep command.

TTY timeout on Windows:
Resolved using kubectl exec instead of interactive run.

✅ Conclusion

This project successfully demonstrates deploying a production-ready Flask and MongoDB application on Kubernetes with authentication, autoscaling, persistent storage, and proper resource management.

Author

Deepanshu Tayal