pipeline {
    agent any

    stages {
        stage('Clone') {
            steps {
                git@github.com:ammarzarouki8/deploy-app-spring-angular.git
            }
        }

        stage('Build') {
            steps {
                sh 'docker compose up -d --build'
            }
        }
    }
}
