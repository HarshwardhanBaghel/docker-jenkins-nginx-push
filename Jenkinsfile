pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'mynginx/webapp'
        DOCKER_HUB_USERNAME = 'hbprac'
        DOCKER_HUB_PASSWORD = credentials('my-docker-hub-credentials-id')
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
                    try {
                        docker.build("$DOCKER_IMAGE", "-f Dockerfile .")
                    } catch (Exception e) {
                        error "Docker build failed: ${e.message}"
                    }
                }
            }
        }

        stage('Push Docker Image to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'my-docker-hub-credentials-id') {
                        docker.image("$DOCKER_IMAGE").push('latest')
                    }
                }
            }
        }

        stage('Deploy to Docker') {
            steps {
                script {
                    sh """
                        docker pull $DOCKER_IMAGE
                        docker rm -f webapp-container || true
                        docker run -d -p 80:80 --name webapp-container $DOCKER_IMAGE
                    """
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

