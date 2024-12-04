pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'mynginx/webapp'
        DOCKER_HUB_USERNAME = 'hbprac'
        DOCKER_HUB_PASSWORD = credentials('my-docker-hub-credentials-id')  // Create Jenkins credentials for Docker Hub
        GITHUB_REPO = 'https://github.com/HarshwardhanBaghel/docker-jenkins-nginx-push.git'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git url: "$GITHUB_REPO", branch: 'main'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build the NGINX Docker image
                    docker.build("$DOCKER_IMAGE", "-f Dockerfile .")
                }
            }
        }

        stage('Push Docker Image to Docker Hub') {
            steps {
                script {
                    // Log in to Docker Hub
                    docker.withRegistry('https://index.docker.io/v1/', 'my-docker-hub-credentials-id') {
                        docker.image("$DOCKER_IMAGE").push()
                    }
                }
            }
        }

        stage('Deploy to Docker') {
            steps {
                script {
                    // Stop and remove the existing container (if any)
                    sh 'docker rm -f webapp-container || true'

                    // Run the new Docker container
                    sh 'docker run -d -p 80:80 --name webapp-container $DOCKER_IMAGE'
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline successfully executed.'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}


