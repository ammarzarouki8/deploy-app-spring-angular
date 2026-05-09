pipeline {
    agent any

    environment {
        DOCKER_HUB = "ammarzarouki8"
        FRONT_IMAGE = "myapp-frontend"
        BACK_IMAGE  = "myapp-backend"
        REPO_URL = "https://github.com/ammarzarouki8/deploy-app-spring-angular.git"
    }

    stages {

        stage('Clean Workspace') {
            steps {
                deleteDir()
            }
        }

        stage('Clone Repository') {
            steps {
                git branch: 'main', url: "${REPO_URL}"
            }
        }

        stage('Verify Tools') {
            steps {
                sh '''
                    echo "Checking tools versions..."
                    docker --version
                    java -version || true
                    mvn -version || true
                    node -v || true
                    npm -v || true
                '''
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'docker-hub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                        echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    '''
                }
            }
        }

        stage('Build Frontend') {
            steps {
                dir('angular-app') {
                    sh '''
                        echo "Installing Angular dependencies..."
                        npm install

                        echo "Building Angular app..."
                        npm run build
                    '''

                    sh '''
                        docker build -t $DOCKER_HUB/$FRONT_IMAGE \
                        -f Dockerfile .
                    '''
                }
            }
        }

        stage('Build Backend') {
            steps {
                dir('springboot') {
                    sh '''
                        echo "Building Spring Boot app..."
                        mvn clean package -DskipTests
                    '''

                    sh '''
                        docker build -t $DOCKER_HUB/$BACK_IMAGE \
                        -f Dockerfile .
                    '''
                }
            }
        }

        stage('Push Images') {
            steps {
                sh '''
                    docker push $DOCKER_HUB/$FRONT_IMAGE
                    docker push $DOCKER_HUB/$BACK_IMAGE
                '''
            }
        }

        stage('Deploy with Docker Compose') {
            steps {
                sh '''
                    docker compose down || true
                    docker compose up -d --build
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline executed successfully"
        }

        failure {
            echo "❌ Pipeline failed - check logs"
        }

        always {
            cleanWs()
        }
    }
}
