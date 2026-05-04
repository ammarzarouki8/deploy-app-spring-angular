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

        stage('Build Frontend Image') {
            steps {
                dir('deploy-app-spring-angular/angular-app') {

                    withCredentials([usernamePassword(
                        credentialsId: 'docker-hub-creds',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )]) {

                        bat 'echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin'
                        bat 'docker build -t ammarzarouki8/myapp-frontend .'
                        bat 'docker push ammarzarouki8/myapp-frontend'
                    }
                }
            }
        }

        stage('Build Backend Image') {
            steps {
                dir('deploy-app-spring-angular/springboot') {

                    withCredentials([usernamePassword(
                        credentialsId: 'docker-hub-creds',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )]) {

                        bat 'echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin'
                        bat 'mvn clean package'
                        bat 'docker build -t ammarzarouki8/myapp-backend .'
                        bat 'docker push ammarzarouki8/myapp-backend'
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                dir('deploy-app-spring-angular') {
                    bat 'docker compose up -d'
                }
            }
        }
    }
}
