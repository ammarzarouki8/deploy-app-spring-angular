pipeline {
    agent any

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

                        sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'

                        sh 'docker build -t ammarzarouki8/myapp-frontend .'

                        sh 'docker push ammarzarouki8/myapp-frontend'
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

                        sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'

                        sh 'mvn clean package'

                        sh 'docker build -t ammarzarouki8/myapp-backend .'

                        sh 'docker push ammarzarouki8/myapp-backend'
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                dir('deploy-app-spring-angular') {

                    sh 'docker compose up -d'
                }
            }
        }
    }
}
