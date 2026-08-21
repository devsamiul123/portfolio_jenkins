pipeline {
    agent any

    environment {
        IMAGE_NAME = "samiul-portfolio"
        CONTAINER_NAME = "samiul-portfolio"
        HOST_PORT = "8081"
        CONTAINER_PORT = "80"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                    docker build -t ${IMAGE_NAME}:latest .
                '''
            }
        }

        stage('Stop Old Container') {
            steps {
                sh '''
                    docker stop ${CONTAINER_NAME} || true
                    docker rm ${CONTAINER_NAME} || true
                '''
            }
        }

        stage('Deploy New Container') {
            steps {
                sh '''
                    docker run -d \
                        --name ${CONTAINER_NAME} \
                        --restart unless-stopped \
                        -p ${HOST_PORT}:${CONTAINER_PORT} \
                        ${IMAGE_NAME}:latest
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                sh '''
                    sleep 3
                    curl -f http://localhost:${HOST_PORT}
                '''
            }
        }
    }

    post {
        success {
            echo "Portfolio deployed successfully."
            echo "Access it at: http://YOUR_VM_IP:${HOST_PORT}"
        }

        failure {
            echo "Deployment failed."
        }
    }
}