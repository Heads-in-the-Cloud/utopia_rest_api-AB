#!groovy
pipeline {
    agent any

    environment {
        image_label = "ab-flights-microservice"
        git_commit_hash ="${sh(returnStdout: true, script: 'git rev-parse --short=8 HEAD')}"
        image = ""
    }

    stages {
        stage('Package') {
            steps {
                sh "./mvnw package"
            }
        }

        stage('Build') {
            steps {
                script {
                    image = docker.build image_label
                }
            }
        }

        stage('Push to registry') {
            steps {
                script {
                    docker.withRegistry(FLIGHTS_ECR_URI, 'ecr:us-west-2:ecr-creds') {
                        image.push("$git_commit_hash")
                        image.push('latest')
                    }
                }
            }
        }

        stage('Clean up') {
            steps {
                sh "./mvnw clean"
                sh "docker rmi $image_label"
            }
        }
    }
}
