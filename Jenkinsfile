pipeline {
    agent any

    triggers {
        githubPush()
    }

    environment {
        IMAGE_NAME = "ashwinemcbalaji/cicd-node-app"
        DOCKERHUB_CREDENTIALS = "dockerhub-cred1"

        // SonarCloud config
        SONAR_PROJECT_KEY = "ashwin918_cicd-node-app"
        SONAR_ORG = "ashwin918"
        SONAR_TOKEN = credentials('sonar-token')
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Sonar Code Analysis') {
            steps {
                withSonarQubeEnv('SonarCloud') {
                    bat """
                    sonar-scanner ^
                      -Dsonar.projectKey=%SONAR_PROJECT_KEY% ^
                      -Dsonar.organization=%SONAR_ORG% ^
                      -Dsonar.sources=. ^
                      -Dsonar.host.url=https://sonarcloud.io ^
                      -Dsonar.login=%SONAR_TOKEN%
                    """
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 1, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
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
