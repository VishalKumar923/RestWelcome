pipeline {
    agent any

    environment {
        IMAGE_NAME = "vishalkumar923/restwelcome"
        IMAGE_TAG  = "${BUILD_NUMBER}"
        CONTAINER  = "restwelcome"
    }

    triggers {
        githubPush()
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/VishalKumar923/RestWelcome.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh """
                docker build -t $IMAGE_NAME:$IMAGE_TAG .
                docker tag $IMAGE_NAME:$IMAGE_TAG $IMAGE_NAME:latest
                """
            }
        }

        stage('Push Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh """
                    docker login -u $DOCKER_USER -p $DOCKER_PASS
                    docker push $IMAGE_NAME:$IMAGE_TAG
                    docker push $IMAGE_NAME:latest
                    """
                }
            }
        }

        stage('Deploy to EC2') {
            steps {
                sh """
                docker stop $CONTAINER || true
                docker rm $CONTAINER || true
                docker run -d -p 8080:8080 \
                --name $CONTAINER \
                --restart unless-stopped \
                $IMAGE_NAME:latest
                """
            }
        }
    }
}
