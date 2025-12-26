pipeline {
    agent any

    environment {
        // CHANGE THIS: Replace 'your-dockerhub-username' with your actual username
        DOCKER_IMAGE = "yashrajmonani/my-flask-app"


        
        // This matches the ID you created in Step 1
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
                    // We tag it with the Build Number so every version is unique
                    sh "docker build -t ${DOCKER_IMAGE}:${env.BUILD_NUMBER} ."
                    
                    // Optional: Also tag as 'latest' for convenience
                    sh "docker tag ${DOCKER_IMAGE}:${env.BUILD_NUMBER} ${DOCKER_IMAGE}:latest"
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    // This wraps the commands with your login credentials securely
                    // The empty quotes '' mean we are using the default public Docker Hub
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
            // Clean up: Delete the image from the Jenkins server to save space
            sh "docker rmi ${DOCKER_IMAGE}:${env.BUILD_NUMBER}"
            sh "docker rmi ${DOCKER_IMAGE}:latest"
            cleanWs()
        }
        success {
            echo 'Success! Image pushed to Docker Hub.'
        }
    }
}