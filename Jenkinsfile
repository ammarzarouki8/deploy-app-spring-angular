pipeline {
    agent any

    stages {
        stage('Clone') {
            steps {
                git 'https://github.com/NouraneZouabi/deploy-app-spring-angular.git'
            }
        }

        stage('Build') {
            steps {
                sh 'docker compose up -d --build'
            }
        }
    }
}
