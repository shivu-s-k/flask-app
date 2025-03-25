# DevOps Flask App

This project demonstrates how to create a Flask-based web application, containerize it using Docker, and deploy it on a Kubernetes cluster using Minikube or MicroK8s.

## Prerequisites

Ensure you have the following installed on your local machine:
- [Python 3.x](https://www.python.org/downloads/)
- [Docker](https://www.docker.com/get-started)
- [Minikube](https://minikube.sigs.k8s.io/docs/start/) or [MicroK8s](https://microk8s.io/docs/)
- [kubectl](https://kubernetes.io/docs/tasks/tools/)

## Steps to Deploy

### 1. Create a Flask Application
Create a simple Flask app that runs on port 5000 and returns `"Welcome to DevOps Flask App!"` when accessed via the `/` route.

**app.py:**
```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def home():
    return "Welcome to DevOps Flask App!"

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

### 2. Create a Dockerfile
Write a `Dockerfile` to containerize the Flask app.

**Dockerfile:**
```dockerfile
# Use an official Python runtime as a base image
FROM python:3.9

# Set the working directory
WORKDIR /app

# Copy the application files
COPY app.py requirements.txt /app/

# Install dependencies
RUN pip install -r requirements.txt

# Expose port 5000
EXPOSE 5000

# Run the application
CMD ["python", "app.py"]
```

### 3. Build and Run Docker Container

Build the Docker image:
```sh
docker build -t flask-app:latest .
```

Run the container:
```sh
docker run -p 5000:5000 flask-app
```

Verify it works by accessing:
```
http://127.0.0.1:5000
```

### 4. Create Kubernetes Deployment

Create a `deployment.yaml` file to deploy the Docker container.

**deployment.yaml:**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: flask-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: flask-app
  template:
    metadata:
      labels:
        app: flask-app
    spec:
      containers:
      - name: flask-app
        image: flask-app:latest
        imagePullPolicy: Never
        ports:
        - containerPort: 5000
```

### 5. Create Kubernetes Service

Create a `service.yaml` file to expose the Flask app using NodePort.

**service.yaml:**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: flask-app-service
spec:
  type: NodePort
  selector:
    app: flask-app
  ports:
    - protocol: TCP
      port: 5000
      targetPort: 5000
      nodePort: 30007
```

### 6. Deploy on Minikube or MicroK8s

Start Minikube:
```sh
minikube start
```

Load the Docker image into Minikube:
```sh
eval $(minikube docker-env)
docker build -t flask-app:latest .
```

Deploy the application:
```sh
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

### 7. Test the Deployment

Get the Minikube service URL:
```sh
minikube service flask-app-service --url
```

Access the Flask app using the NodePort URL displayed.


## Conclusion
This guide covered the end-to-end process of creating a Flask app, containerizing it with Docker, and deploying it on Kubernetes using Minikube. 🚀

Feel free to contribute or report any issues!

---


