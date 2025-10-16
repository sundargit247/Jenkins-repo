pipeline {
    agent any 

    environment {
        IMAGE_NAME = "sundar306/jenkins-task"
        CONTAINER_NAME = "jenkins-task-container"
        PORT = "3000"
    }

    stages {
        stage('Build Docker image') {
            steps {
                echo 'Building Docker image...'
                sh '''
                export DOCKER_BUILDKIT=1
                docker build --pull -t $IMAGE_NAME:latest .
                '''
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                }
            }
        }

        stage('Push image to Docker Hub') {
            steps {
                echo 'Pushing Docker image to Docker Hub...'
                sh 'docker push $IMAGE_NAME:latest'
            }
        }

        stage('Deploy container') {
            steps {
                echo 'Deploying container on EC2...'
                sh '''
                docker stop $CONTAINER_NAME || true
                docker rm $CONTAINER_NAME || true
                docker pull $IMAGE_NAME:latest
                docker run -d --name $CONTAINER_NAME -p 80:$PORT $IMAGE_NAME:latest
                '''
            }
        }

        stage('Cleanup (Optional)') {
            when { expression { false } } // disable cleanup by default
            steps {
                echo 'Cleaning Docker images and containers...'
                sh '''
                docker rmi -f $IMAGE_NAME:latest || true
                docker system prune -af
                '''
            }
        }
    }
}
