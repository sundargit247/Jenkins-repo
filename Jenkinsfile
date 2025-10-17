pipeline {
    agent any

    environment {
        // Use your Docker Hub username here
        DOCKER_IMAGE = "sundar690/jenkins-task:latest"
    }

    stages {

        stage('Checkout Code') {
            steps {
                echo "Checking out source code from GitHub..."
                git url: 'https://github.com/sundargit247/Jenkins-repo', branch: 'main'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building Docker image: ${DOCKER_IMAGE}"
                sh "docker build -t ${DOCKER_IMAGE} ."
            }
        }

        stage('Login to Docker Hub') {
            steps {
                echo "Logging into Docker Hub..."
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', 
                                                 usernameVariable: 'DOCKER_USER', 
                                                 passwordVariable: 'DOCKER_PASS')]) {
                    // Secure login using password-stdin
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                echo "Pushing Docker image to Docker Hub..."
                sh "docker push ${DOCKER_IMAGE}"
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully! Docker image pushed: ${DOCKER_IMAGE}"
        }
        failure {
            echo "Pipeline failed. Check logs for details."
        }
    }
}
