pipeline {
    agent any

    environment {
        IMAGE_NAME = "rahulrai9/jenkins-docker-demo"
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout') {
            steps {
                git 'https://github.com/rahulraidevops/jenkins-docker-demo.git'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                echo "SonarQube Scan Placeholder"
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$IMAGE_TAG .'
            }
        }

        stage('Push Docker Image') {
            steps {
                echo "Docker Push Placeholder"
            }
        }
    }
}
