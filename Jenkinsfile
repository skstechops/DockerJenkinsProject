pipeline {

    agent any

    environment {
        DOCKER_BACKEND_IMAGE  = "skstechops/project-backend:1.0"
        DOCKER_FRONTEND_IMAGE = "skstechops/project-frontend:1.0"
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out source code...'
                checkout scm
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-credentials',
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {
                    sh '''
                        echo "$DOCKER_PASSWORD" | docker login \
                        -u "$DOCKER_USERNAME" \
                        --password-stdin
                    '''
                }
            }
        }

        stage('Build Backend') {
            steps {
                echo 'Building Backend Docker image...'

                sh '''
                    docker build \
                    -t $DOCKER_BACKEND_IMAGE \
                    ./Backend
                '''
            }
        }

        stage('Build Frontend') {
            steps {
                echo 'Building Frontend Docker image...'

                sh '''
                    docker build \
                    -t $DOCKER_FRONTEND_IMAGE \
                    ./Frontend
                '''
            }
        }

        stage('Push Backend') {
            steps {
                echo 'Pushing Backend image to Docker Hub...'

                sh '''
                    docker push $DOCKER_BACKEND_IMAGE
                '''
            }
        }

        stage('Push Frontend') {
            steps {
                echo 'Pushing Frontend image to Docker Hub...'

                sh '''
                    docker push $DOCKER_FRONTEND_IMAGE
                '''
            }
        }

        stage('Stop Existing Containers') {
            steps {
                sh '''
                    docker compose down || true
                '''
            }
        }

        stage('Deploy') {
            steps {
                echo 'Starting Backend and Frontend containers...'

                sh '''
                    docker compose up -d
                '''
            }
        }

        stage('Verify') {
            steps {
                sh '''
                    docker compose ps
                '''

                echo 'Checking Backend health...'

                sh '''
                    sleep 5
                    curl -f http://localhost:8000/health
                '''

                echo 'Checking Frontend health...'

                sh '''
                    curl -f http://localhost:8081
                '''
            }
        }
    }

    post {

        success {
            echo 'CI/CD Pipeline completed successfully!'
        }

        failure {
            echo 'CI/CD Pipeline failed!'
        }

        always {
            sh 'docker logout || true'
            echo 'Pipeline execution completed.'
        }
    }
}

