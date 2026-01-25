pipeline {
    agent any

    tools {
        jdk 'jdk17'
        maven 'maven3'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main',
                url: 'https://github.com/VishalKumar923/RestWelcome.git'
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t restwelcome:latest .'
            }
        }

        stage('Run Container') {
            steps {
                sh '''
                docker stop restwelcome || true
                docker rm restwelcome || true
                docker run -d -p 8080:8080 --name restwelcome restwelcome:latest
                '''
            }
        }
    }
}
