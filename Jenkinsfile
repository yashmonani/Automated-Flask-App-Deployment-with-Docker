pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "yashrajmonani/my-flask-app"
        REGISTRY_CREDENTIALS = "docker-hub-login" 
    }
    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }
        stage('Install Dependencies') {
            steps {
                sh 'pip install -r requirements.txt'
            }
        }
        stage('Run Unit Tests') {
            steps {
                sh 'pytest'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    echo 'Building Docker Image...'
                    sh "docker build -t ${DOCKER_IMAGE}:${env.BUILD_NUMBER} ."                    
                    sh "docker tag ${DOCKER_IMAGE}:${env.BUILD_NUMBER} ${DOCKER_IMAGE}:latest"
                }
            }
        }
        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('', REGISTRY_CREDENTIALS) {
                        echo 'Pushing image to Docker Hub...'
                        sh "docker push ${DOCKER_IMAGE}:${env.BUILD_NUMBER}"
                        sh "docker push ${DOCKER_IMAGE}:latest"
                    }
                }
            }
        }
    }
    post {
        always {
            sh "docker rmi ${DOCKER_IMAGE}:${env.BUILD_NUMBER}"
            sh "docker rmi ${DOCKER_IMAGE}:latest"
            cleanWs()
        }
        success {
            echo 'Success! Image pushed to Docker Hub.'
        }
    }
}
