pipeline {
    agent any

    environment {
        IMAGE_NAME = "ashwinemcbalaji/cicd-node-app"
        DOCKERHUB_CREDENTIALS = "dockerhub-cred1"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${IMAGE_NAME}:latest")
                }
            }
        }

        stage('Push to DockerHub') {
            steps {
                script {
                    docker.withRegistry('', DOCKERHUB_CREDENTIALS) {
                        docker.image("${IMAGE_NAME}:latest").push()
                    }
                }
            }
        }

        stage('Deploy Container') {
            steps {
                script {
                    bat 'docker stop cicd-container || exit 0'
                    bat 'docker rm cicd-container || exit 0'
                    bat 'docker run -d -p 4003:3000 --name cicd-container ashwinemcbalaji/cicd-node-app:latest'
                }
            }
        }
    }
}
