# 🚀 CI/CD Pipeline using Jenkins, Docker, Amazon ECR, EventBridge & Lambda

---

## 📌 Project Overview

This project demonstrates a complete **CI/CD pipeline** that automates Docker image build and deployment using AWS services.

---

## 🏗️ Architecture

```
GitHub → Jenkins → Docker Build → Amazon ECR → EventBridge → Lambda → CloudWatch
```

---

## ⚙️ Tech Stack

* Jenkins (CI/CD)
* Docker (Containerization)
* AWS Services:

  * Amazon ECR
  * AWS Lambda
  * Amazon EventBridge
  * Amazon CloudWatch

---

# 📂 Project Structure

```
docker-app/
│── app.py
│── requirements.txt
│── Dockerfile
│── Jenkinsfile
```

---

# 💻 APPLICATION CODE

---

## 🔹 app.py

```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def home():
    return "Hello from Docker + Jenkins CI/CD 🚀"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
```

---

## 🔹 requirements.txt

```
flask
```

---

# 🐳 DOCKER SETUP

---

## 🔹 Dockerfile

```dockerfile
FROM python:3.9

WORKDIR /app

COPY . .

RUN pip install -r requirements.txt

CMD ["python", "app.py"]
```

---

# ⚙️ JENKINS PIPELINE

---

## 🔹 Jenkinsfile

```groovy
pipeline {
    agent any

    environment {
        AWS_REGION = "us-east-1"
        ACCOUNT_ID = "070648356263"
        REPO_NAME = "my-docker-repo"
        IMAGE_TAG = "latest"
    }

    stages {

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t myapp .'
            }
        }

        stage('Tag Image') {
            steps {
                sh '''
                docker tag myapp:latest $ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$REPO_NAME:$IMAGE_TAG
                '''
            }
        }

        stage('Login to ECR') {
            steps {
                sh '''
                aws ecr get-login-password --region $AWS_REGION | \
                docker login --username AWS --password-stdin $ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com
                '''
            }
        }

        stage('Push to ECR') {
            steps {
                sh '''
                docker push $ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$REPO_NAME:$IMAGE_TAG
                '''
            }
        }
    }
}
```

---

# ⚡ AWS LAMBDA CODE

---

## 🔹 lambda_function.py

```python
import json

def lambda_handler(event, context):
    print("🚀 New Docker Image Pushed to ECR!")
    print(json.dumps(event, indent=2))
    
    return {
        'statusCode': 200,
        'body': 'Success'
    }
```

---

# 🔄 WORKFLOW

1. Push code to GitHub
2. Jenkins triggers pipeline
3. Docker image is built
4. Image pushed to Amazon ECR
5. EventBridge detects push
6. Lambda is triggered
7. Logs stored in CloudWatch

---

# 🔧 SETUP STEPS

### 1. Clone repo

```
git clone https://github.com/deshmukhvansh1/docker-app.git
```

### 2. Configure AWS

```
aws configure
```

### 3. Run Jenkins pipeline

Click **Build Now**

---

# 📸 SCREENSHOTS

* Jenkins success
* Docker build logs
* ECR repository
* EventBridge rule
* Lambda function
* CloudWatch logs

---

# ⚠️ CHALLENGES

* Docker permission issue in Jenkins
* AWS credentials not configured
* EventBridge confusion (Scheduler vs Rule)

---

# 💡 FUTURE IMPROVEMENTS

* Add SNS notification
* Deploy to ECS/EKS
* Multi-stage pipeline

---

# 🎯 KEY LEARNINGS

* CI/CD automation
* Docker lifecycle
* AWS event-driven architecture

---

# 👨‍💻 Author

**Vansh Deshmukh**

---

# ⭐ Star this repo if helpful!
