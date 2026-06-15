pipeline {
    agent any

    environment {
        IMAGE_NAME = "rahulrai9/jenkins-docker-demo"
        IMAGE_TAG = "${BUILD_NUMBER}"
        SCANNER_HOME = tool 'sonar-scanner'
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
                withSonarQubeEnv('sonarqube') {
                    sh """
                    ${SCANNER_HOME}/bin/sonar-scanner \
                    -Dsonar.projectKey=jenkins-docker-demo \
                    -Dsonar.projectName=jenkins-docker-demo \
                    -Dsonar.sources=.
                    """
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$IMAGE_TAG .'
            }
        }

        stage('Trivy Security Scan') {
            steps {
                sh '''
                    trivy image \
                    --severity HIGH,CRITICAL \
                    --no-progress \
                    --format table \
                    --output trivy-report.txt \
                    ${IMAGE_NAME}:${IMAGE_TAG}
                '''

                archiveArtifacts artifacts: 'trivy-report.txt', fingerprint: true
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('', 'dockerhub-creds') {
                        sh 'docker push $IMAGE_NAME:$IMAGE_TAG'
                    }
                }
            }
        }

        stage('Deploy Application') {
            steps {
                sh '''
                docker stop demo-app || true
                docker rm demo-app || true

                docker pull $IMAGE_NAME:$IMAGE_TAG

                docker run -d \
                --name demo-app \
                -p 5000:5000 \
                $IMAGE_NAME:$IMAGE_TAG
                '''
            }
        }

        stage('Health Check') {
            steps {
                script {
                    sleep 10
                }

                sh '''
                curl -f http://13.203.214.237:5000
                '''
            }
        }

        stage('Cleanup Old Images') {
            steps {
                sh '''
                docker image prune -f
                '''
            }
        }
    }
}