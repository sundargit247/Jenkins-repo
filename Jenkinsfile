pipeline {
    agent any 

    environment {
        IMAGE_NAME = "gokulk306/react-task"
        CONTAINER_NAME = "react-task-container"
        PORT = "3000"
    }

    stages {
        stage('Clone repo') {
            steps {
                git branch: 'main', url: 'https://github.com/sundargit247/Jenkins-repo'
            }
        }

        stage('Build Docker image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:latest .'
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentails', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    '''
                }
            }
        }

        stage('Push image to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentails', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                    docker push $IMAGE_NAME:latest
                    '''
                }
            }
        }

        stage('Deploy container') {
            steps {
                echo 'Deploying React app container on EC2...'
                sh '''
                docker stop $CONTAINER_NAME || true
                
                docker rm $CONTAINER_NAME || true

                docker pull $IMAGE_NAME:latest

                docker run -d \
                    --name $CONTAINER_NAME \
                    -p 80:$PORT \
                    $IMAGE_NAME:latest
                '''
            }
        }
        stage('Cleaning') {
            steps {
                echo 'Cleaning up Docker images and containers...'
                sh '''
                docker rmi -f $IMAGE_NAME:latest || true
                docker image prune -f
                docker container prune -f
                '''
            }
        }
    }
}
