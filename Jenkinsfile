pipeline {
    agent any

    tools {
        maven 'maven'
        nodejs 'node'
    }

    environment {
        DOCKER_IMAGE_FRONT = "ammarzarouki8/myapp-frontend"
        DOCKER_IMAGE_BACK  = "ammarzarouki8/myapp-backend"
    }

    stages {

        stage('Clean') {
            steps {
                deleteDir()
            }
        }

        stage('Clone repo') {
            steps {
                git 'https://github.com/ammarzarouki8/deploy-app-spring-angular.git'
            }
        }

        stage('Test Docker') {
            steps {
                sh 'docker --version'
                sh 'docker ps'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'docker-hub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                }
            }
        }

        stage('Build Frontend') {
            steps {
                dir('angular-app') {
                    sh 'docker build -t $DOCKER_IMAGE_FRONT .'
                    sh 'docker push $DOCKER_IMAGE_FRONT'
                }
            }
        }

        stage('Build Backend') {
            steps {
                dir('springboot') {
                    sh 'mvn clean package'
                    sh 'docker build -t $DOCKER_IMAGE_BACK .'
                    sh 'docker push $DOCKER_IMAGE_BACK'
                }
            }
        }

        stage('Deploy') {
            steps {
                sh 'docker compose up -d'
            }
        }
    }
}
