pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'prashik536/nodejs'
        DOCKER_CREDENTIALS_ID = 'docker'  // your credential ID
    }

    stages {
        stage('Clone Git Repository') {
            steps {
                git scm
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
                    sh """
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push $DOCKER_IMAGE:latest
                    """
                }
            }
        }
    }
}
