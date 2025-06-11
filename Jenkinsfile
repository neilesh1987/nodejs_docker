pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'prashik536/nodejs'
        DOCKER_CREDENTIALS_ID = 'docker'     // Docker Hub credentials ID
        SSH_CREDENTIALS_ID = 'deploy-key'    // SSH key credentials ID
        DEPLOY_USER = 'prashik'               // Username on remote server
        DEPLOY_HOST = '172.31.94.51'       // Replace with your server IP
        CONTAINER_NAME = 'nodejs-app'
        APP_PORT = '3000'
        HOST_PORT = '80'
    }

    stages {
        stage('Clone Git Repository') {
            steps {
                checkout scm
            }
        }

        stage('Check Docker Version') {
            steps {
                sh 'docker --version'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE:latest .'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: "${DOCKER_CREDENTIALS_ID}", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push $DOCKER_IMAGE:latest
                    '''
                }
            }
        }

        stage('Deploy to Remote Server') {
            steps {
                sshagent (credentials: ["${SSH_CREDENTIALS_ID}"]) {
                    sh '''
                        ssh -o StrictHostKeyChecking=no $DEPLOY_USER@$DEPLOY_HOST << EOF
                        docker pull $DOCKER_IMAGE:latest
                        docker stop $CONTAINER_NAME || true
                        docker rm $CONTAINER_NAME || true
                        docker run -d --name $CONTAINER_NAME -p $HOST_PORT:$APP_PORT $DOCKER_IMAGE:latest
                        EOF
                    '''
                }
            }
        }
    }
}
