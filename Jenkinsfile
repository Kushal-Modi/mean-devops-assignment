pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-cred')
    }

    stages {

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