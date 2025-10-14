pipeline {
    agent any 

    environment {
        IMAGE_NAME = "gokulk306/react-task"
    }

    stages {
        stage('Clone repo') {
            steps {
                git branch: 'main', url: 'https://github.com/Gokulk-306/React-app.git'
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
        stage('Cleaning the images') {
            steps {
                echo 'Cleaning up unused Docker images and containers...'
                sh '''
                docker rmi -f $IMAGE_NAME:latest || true
                
                docker image prune -f
    
                docker container prune -f
                
                '''
            }
        }

    }
}