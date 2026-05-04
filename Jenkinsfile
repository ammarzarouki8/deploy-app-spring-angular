pipeline {
    agent any

    tools {
        maven 'maven'
        nodejs 'node'
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

        // ✅ TEST DOCKER
        stage('Test Docker') {
            steps {
                sh 'docker --version'
                sh 'docker ps'
            }
        }

        // ✅ LOGIN DOCKER HUB (une seule fois)
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

        // ✅ FRONTEND
        stage('Build Frontend Image') {
            steps {
                dir('deploy-app-spring-angular/angular-app') {
                    sh 'docker build -t ammarzarouki8/myapp-frontend .'
                    sh 'docker push ammarzarouki8/myapp-frontend'
                }
            }
        }

        // ✅ BACKEND
        stage('Build Backend Image') {
            steps {
                dir('deploy-app-spring-angular/springboot') {
                    sh 'mvn clean package'
                    sh 'docker build -t ammarzarouki8/myapp-backend .'
                    sh 'docker push ammarzarouki8/myapp-backend'
                }
            }
        }

        // ✅ DEPLOY
        stage('Deploy') {
            steps {
                dir('deploy-app-spring-angular') {
                    sh 'docker compose up -d'
                }
            }
        }
    }
}
