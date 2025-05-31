# Airbnb-Price-Prediction

🛠️ MLOps Pipeline for Scalable Model Training and Deployment
This project demonstrates a complete MLOps pipeline for training, tracking, containerizing, and deploying machine learning models using MLflow, Docker, Kubernetes, Spark, Helm, and CI/CD tools like CircleCI and ArgoCD.

🚀 Features
ML model training using PySpark, H2O AutoML, and Hyperopt

MLflow tracking and model registry with production model serving

Containerization of services with Docker

Kubernetes deployment using manifests and Helm charts

CI/CD automation with CircleCI and ArgoCD

Future-ready design for model drift handling and streaming data integration

📊 MLflow Tracking and Serving
After training, all models are logged under the MLflow experiment MLOps_Experiment. Tracked models include:

4 models with RMSEs: 215.1, 214.9, 214.3, and 206.1

Best performance by: H2O AutoML

🔍 Serving a Model via MLflow
From the MLflow UI, select the desired model (e.g., RF-Hyperopt)

Register and promote it to Production

Serve it using:

bash
Copy
Edit
mlflow models serve -m "models:/RF-Hyperopt/Production" --env-manager=local -h 0.0.0.0
Access the REST API at: http://localhost:5000

You can also automate this via the MLflow Python API. See the MLflow docs for full API details.

🐳 Docker Images
We use four Docker images, three of which are Spark-related and one for model training:

1. spark-master:3.3
Custom-built Ubuntu-based Spark master node.

2. spark-worker:3.3
Extends spark-master:3.3 with worker-specific configurations.

3. mlflow-tracking-server
Based on Miniconda, includes mlflow, boto3, and pymysql.

4. pyspark-runtime
Main container for running training code.

Key libraries: pyspark, mlflow, h2o, hyperopt, nltk, matplotlib, etc.

Dockerfile
Copy
Edit
FROM python:3.9.7
# Installs Spark, OpenJDK, Python libraries and training scripts
Once built, push the images to Docker Hub or a private registry (e.g., AWS ECS).

☸️ Kubernetes & Helm Deployment
Once the images are available, deploy the system using:

bash
Copy
Edit
kubectl apply -f ./k8s
# or with Helm:
helm install mlops mlops-chart
🔁 Port Forwarding for Local Access
bash
Copy
Edit
kubectl port-forward services/mlflow-tracking 5000:5000
kubectl port-forward services/spark-master 8080:8080
kubectl port-forward services/minio 9000:9000 9001:9001
kubectl port-forward services/notebooks 8888:8888
🔄 CI/CD with CircleCI and ArgoCD
✅ CircleCI: Continuous Integration
Configured via .circleci/config.yml, it:

Builds Docker images on commit

Pushes images and Helm charts to repositories

yaml
Copy
Edit
version: 2.1
jobs:
  docker-build:
    machine:
      image: ubuntu-2204:2022.04.2
    steps:
      - checkout
      - run:
          name: Build images
          command: |
            docker build -t spark-master:3.3 -f ./Docker/Spark/Dockerfile .
            docker build -t spark-worker:3.3 -f ./Docker/Spark-worker/Dockerfile .
            docker build -t mlflow-tracking -f ./Docker/mlflow/Dockerfile .
            docker build -t pyspark-runner -f ./Docker/pySpark-runner/Dockerfile .
🚢 ArgoCD: Continuous Deployment
Install ArgoCD:

bash
Copy
Edit
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
Port-forward to access UI:

bash
Copy
Edit
kubectl port-forward svc/argocd-server -n argocd 8080:443
Configure your application in the Argo UI using your Helm/chart Git repository.

🔮 Future Improvements
Streaming Support: Integrate Apache Kafka and Spark Streaming for real-time data ingestion.

Continual Training (CT): Enable periodic retraining to handle data drift.

Model Monitoring: Integrate drift detection and model performance monitoring.
