pipeline {
    agent any

    environment {
        IMAGE_NAME = "rahulrai9/jenkins-docker-demo"
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {

        stage('Verify Source Code') {
            steps {
                sh 'pwd'
                sh 'ls -la'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    def scannerHome = tool 'SonarQube Scanner installations'

                    withSonarQubeEnv('sonarqube') {
                        sh """
                        ${scannerHome}/bin/sonar-scanner \
                        -Dsonar.projectKey=jenkins-docker-demo \
                        -Dsonar.projectName=jenkins-docker-demo \
                        -Dsonar.sources=.
                        """
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$IMAGE_TAG .'
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    
                    docker.withRegistry('', 'dockerhub-creds') 
                        {
                            sh 'docker push $IMAGE_NAME:$IMAGE_TAG'
                    
                        }
                    }
                }
            }
        }
    }
}
