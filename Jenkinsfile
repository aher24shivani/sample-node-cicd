//declarative pipeline

pipeline {
    agent any

    environment {
        DOCKER_REGISTRY_CREDENTIALS = credentials('dockerhub')
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build and Test') {
            steps {
                script {
                    sh 'npm install'
                    sh 'npm test'
                }
            }
        }

        stage('Dockerize') {
            steps {
                script {
                    docker.build('todo-app-nodejs')
                }
            }
        }

        stage('Publish to Docker Registry') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                        docker.image('todo-app-nodejs').push("${env.BUILD_NUMBER}")
                    }
                }
            }
        }

/*
        stage('Deploy to Staging') {
            steps {
                script {
                    // Your deployment script or commands go here
                }
            }
        }
*/        
    } //stages

    post {
        always {
            cleanWs()
        }
    }
}
