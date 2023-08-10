#!/usr/bin/env groovy

pipeline {
    agent any

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/amin2018/DevOps-finalexam.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    def dockerImage = docker.build("my_web_app_image:${env.BUILD_ID}", "-f Dockerfile .")
                    env.IMAGE_NAME = "my_web_app_image:${env.BUILD_ID}"
                }
            }
        }

        stage('Push Docker Image to Docker Hub') {
            environment {
                DOCKER_HUB_USERNAME = 'amin2018'
                DOCKER_HUB_REPOSITORY = 'my_web_app_image'  // Ensure this name is lowercase
            }
            steps {
                withCredentials([string(credentialsId: 'Cloud2022#', variable: 'DOCKER_HUB_PASSWORD')]) {   // Assuming 'dockerHubPassword' is your Jenkins secret ID for Docker Hub password
                    script {
                        sh "docker tag ${env.IMAGE_NAME} ${DOCKER_HUB_USERNAME}/${DOCKER_HUB_REPOSITORY}:${env.BUILD_ID}"
                        sh "docker tag ${env.IMAGE_NAME} ${DOCKER_HUB_USERNAME}/${DOCKER_HUB_REPOSITORY}:latest"
                    }
                    sh "docker login -u ${DOCKER_HUB_USERNAME} -p ${DOCKER_HUB_PASSWORD}"
                    sh "docker push ${DOCKER_HUB_USERNAME}/${DOCKER_HUB_REPOSITORY}:${env.BUILD_ID}"
                    sh "docker push ${DOCKER_HUB_USERNAME}/${DOCKER_HUB_REPOSITORY}:latest"
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                script {
                    sh "docker run -p 8000:80 -d --name my_web_app_container ${env.IMAGE_NAME}"
                }
            }
        }
    }
}
