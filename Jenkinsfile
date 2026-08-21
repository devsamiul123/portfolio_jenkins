pipeline {
    agent any

    environment {
        DOCKERHUB_USERNAME = "devsamiul"
        IMAGE_NAME = "${DOCKERHUB_USERNAME}/samiul-portfolio"
        CONTAINER_NAME = "samiul-portfolio"
        HOST_PORT = "8081"
        CONTAINER_PORT = "80"
        DOCKERHUB_CREDENTIALS_ID = "dockerhub-credentials"
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

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(credentialsId: "${DOCKERHUB_CREDENTIALS_ID}", passwordVariable: 'DOCKER_PASS', usernameVariable: 'DOCKER_USER')]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                    '''
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                sh '''
                    docker push ${IMAGE_NAME}:latest
                '''
            }
        }

        stage('Deploy on VM') {
            steps {
                sh '''
                    docker stop ${CONTAINER_NAME} || true
                    docker rm ${CONTAINER_NAME} || true
                    docker run -d \
                        --name ${CONTAINER_NAME} \
                        --restart unless-stopped \
                        -p ${HOST_PORT}:${CONTAINER_PORT} \
                        ${IMAGE_NAME}:latest
                '''
            }
        }
    }

    post {
        success {
            echo "Portfolio built and pushed to Docker Hub successfully."
            echo "Access it on VM at: http://192.168.56.10:${HOST_PORT}"
        }
        failure {
            echo "Deployment failed."
        }
        always {
            sh '''
                docker logout || true
            '''
        }
    }
}