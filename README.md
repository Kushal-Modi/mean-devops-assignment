

# 🚀 MEAN Stack DevOps CI/CD Deployment Project

## 📌 Project Overview

This project demonstrates **end-to-end containerization, CI/CD automation, and cloud deployment** of a full-stack **MEAN (MongoDB, Express, Angular, Node.js)** application.

### 🎯 Objective

* ✅ Containerize frontend and backend applications
* ✅ Push Docker images to DockerHub
* ✅ Deploy application on AWS EC2 (Ubuntu)
* ✅ Configure Nginx reverse proxy
* ✅ Implement Jenkins CI/CD pipeline
* ✅ Enable automatic deployment on every GitHub push

---

# 🏗️ Architecture Flow

```
GitHub → Jenkins → Docker Build → DockerHub → EC2 Server → Docker Compose → Nginx → Browser
```

---

# 🛠️ Technologies Used

| Layer            | Technology                        |
| ---------------- | --------------------------------- |
| Frontend         | Angular 15                        |
| Backend          | Node.js + Express                 |
| Database         | MongoDB                           |
| Containerization | Docker                            |
| Orchestration    | Docker Compose                    |
| CI/CD            | Jenkins                           |
| Cloud            | AWS EC2 (Ubuntu 22.04 - t3.micro) |
| Reverse Proxy    | Nginx                             |

---

# 🐳 Docker Configuration



## 📦 Backend Dockerfile

📂 `backend/Dockerfile`

```dockerfile
FROM node:18

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .

EXPOSE 8080

CMD ["node", "server.js"]
```



## 📦 Frontend Dockerfile

📂 `frontend/Dockerfile`

```dockerfile
# Build Stage
FROM node:18 AS build

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .

RUN npm run build

# Production Stage
FROM nginx:latest

COPY --from=build /app/dist/angular-15-crud /usr/share/nginx/html

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

---

## 📦 docker-compose.yml

```yaml
version: "3.8"

services:
  mongo:
    image: mongo:6
    container_name: mongo
    restart: always
    volumes:
      - mongo_data:/data/db

  backend:
    build: ./backend
    container_name: backend
    restart: always
    environment:
      - MONGO_URL=mongodb://mongo:27017/mydb
    depends_on:
      - mongo

  frontend:
    build: ./frontend
    container_name: frontend
    restart: always
    ports:
      - "80:80"
    depends_on:
      - backend

volumes:
  mongo_data:
```

---

# ☁️ AWS Infrastructure Setup

---

## 🖥️ EC2 Instance Details

* **Instance Type:** t3.micro (Free Tier eligible)
* **Operating System:** Ubuntu 22.04 LTS

---

## 🔐 Security Group Configuration

| Port | Purpose            |
| ---- | ------------------ |
| 22   | SSH Access         |
| 80   | Application (HTTP) |
| 8080 | Jenkins Dashboard  |

---

# 🐳 Jenkins Setup (Dockerized)


## 🚀 Jenkins Installation (Docker)

```bash
docker run -d \
  --name jenkins \
  -u root \
  -p 8080:8080 \
  -p 50000:50000 \
  -v jenkins_home:/var/jenkins_home \
  -v /var/run/docker.sock:/var/run/docker.sock \
  --restart=always \
  jenkins/jenkins:lts
```

---

## 🔐 Jenkins Credentials Configured

* **DockerHub Credentials**

  * ID: `dockerhub-cred`

* **EC2 SSH Key**

  * ID: `server-ssh-key`

---

# ⚙️ Jenkins Pipeline Configuration

```groovy
pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-cred')
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                url: 'https://github.com/Kushal-Modi/mean-devops-assignment.git'
            }
        }

        stage('Build Backend Image') {
            steps {
                sh 'docker build -t kushalmodi220105/backend:latest ./backend'
            }
        }

        stage('Build Frontend Image') {
            steps {
                sh 'docker build -t kushalmodi220105/frontend:latest ./frontend'
            }
        }

        stage('Push Images') {
            steps {
                sh '''
                echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
                docker push kushalmodi220105/backend:latest
                docker push kushalmodi220105/frontend:latest
                '''
            }
        }

        stage('Deploy to Server') {
            steps {
                sshagent(['server-ssh-key']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no ubuntu@13.201.50.175 "
                        cd /home/ubuntu/mean-devops-assignment &&
                        docker-compose down &&
                        docker-compose pull &&
                        docker-compose up -d
                    "
                    '''
                }
            }
        }
    }
}
```

---

# 🚀 Step-by-Step Deployment Instructions

---

## 1️⃣ Clone Repository on EC2

```bash
git clone https://github.com/Kushal-Modi/mean-devops-assignment.git
cd mean-devops-assignment
```

---

## 2️⃣ Run Application

```bash
docker-compose up -d
```

---

## 3️⃣ Access Application

```
http://13.201.50.175/
```

---

# 📸 Project Screenshots

### ✅ Rendered Output:
![enter image description here](https://i.ibb.co/Mx2kxrGH/application.png)

![enter image description here](https://i.ibb.co/Gv3Qt5CZ/application2.png)


![jenkins-build](https://i.ibb.co/0yWzwh8f/jenkins-build.png)

![DockerHub Images](https://i.ibb.co/SD13Kk1n/dockerhub.png)




---

# 🎉 Final Result

✔ Fully Containerized MEAN Application
✔ Automated CI/CD Pipeline
✔ DockerHub Image Push
✔ AWS EC2 Deployment
✔ Nginx Reverse Proxy Setup
✔ Automatic Deployment on Every GitHub Push

---

# 👨‍💻 Author

**Kushal Modi**
DevOps Engineer | Cloud Enthusiast


