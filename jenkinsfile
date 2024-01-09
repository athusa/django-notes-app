pipeline {
    agent any

    environment {
        GITHUB_USERNAME = 'athusa'
        GITHUB_PASSWORD = 'athindia710'
        AWS_REGION = 'us-east-1'
        DOCKER_IMAGE_NAME = 'notes-app'
        DOCKERHUB_CREDENTIALS = credentials('docker-hub')
    }

    stages {
        stage('Clone Repository') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/sakshi']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'github-credentials', url: 'https://github.com/SakshiShekharShukla/Sakshi.git']]])
            }
        }

        stage('Build and Push Docker Image') {
            steps {
                script {
                    // Build Docker image
                    sh 'docker build -t notes-app .'

                    // Log in to Docker Hub using withCredentials
                    withCredentials([usernamePassword(credentialsId: 'docker-hub', usernameVariable: 'DOCKERHUB_USERNAME', passwordVariable: 'DOCKERHUB_PASSWORD')]) {
                        withEnv(["DOCKERHUB_CREDENTIALS_PSW=${DOCKERHUB_PASSWORD}"]) {
                            sh "docker login -u $DOCKERHUB_USERNAME -p $DOCKERHUB_CREDENTIALS_PSW"

                            // Tag the Docker image
                            sh "docker tag notes-app:latest $DOCKERHUB_USERNAME/$DOCKER_IMAGE_NAME:latest"

                            // Push the Docker image to Docker Hub
                            sh "docker push $DOCKERHUB_USERNAME/$DOCKER_IMAGE_NAME:latest"
                        }
                    }
                }
            }
        }

        stage('Run App') {
            steps {
                script {
                    sh "docker run -d -p 8081:8000 notes-app:latest"
                }
            }
        }
    }
}
