pipeline {
    agent any

    environment {
        // Replace with your Docker Hub username/repository.
        IMAGE_NAME = "bavyaraghu/docker-repository"
        IMAGE_TAG = "latest"
        DOCKER_CREDENTIALS_ID = "bavyaraghu"
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out source code...'
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                bat 'docker build -t %IMAGE_NAME%:%IMAGE_TAG% .'
            }
        }

        stage('Docker Login and Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: "${DOCKER_CREDENTIALS_ID}",
                    usernameVariable: 'DOCKER_USERNAME',
                    passwordVariable: 'DOCKER_PASSWORD'
                )]) {
                    bat 'docker login -u "%DOCKER_USERNAME%" -p "%DOCKER_PASSWORD%"'
                    bat 'docker push %IMAGE_NAME%:%IMAGE_TAG%'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                echo 'Deploying application to Kubernetes...'
                bat 'kubectl apply -f deployment.yaml'
                bat 'kubectl apply -f service.yaml'
            }
        }

        stage('Verify Kubernetes Deployment') {
            steps {
                echo 'Checking Kubernetes resources...'
                bat 'kubectl get deployments'
                bat 'kubectl get pods'
                bat 'kubectl get services'
            }
        }
    }

    post {
        success {
            echo 'PIPELINE COMPLETED SUCCESSFULLY!'
            echo "Docker Image: ${IMAGE_NAME}:${IMAGE_TAG}"
            echo 'Kubernetes deployment completed successfully!'
        }
        failure {
            echo 'PIPELINE FAILED!'
            echo 'Please check the Jenkins console output.'
        }
    }
}
