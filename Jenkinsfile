pipeline {
    agent any

    triggers {
        githubPush()   // This makes Jenkins trigger the pipelinpe automatically on GitHub push
    }

    environment {
        DOCKER_IMAGE = "sundar690/jenkins-task:latest"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git url: 'https://github.com/sundargit247/Jenkins-repo', branch: 'main'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${DOCKER_IMAGE} ."
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', 
                                                 usernameVariable: 'DOCKER_USER', 
                                                 passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                sh "docker push ${DOCKER_IMAGE}"
            }
        }
    }

    post {
        success {
            echo "Docker image pushed successfully!"
        }
        failure {
            echo "Pipeline failed. Check logs for details."
        }
    }
}
