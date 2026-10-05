pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/aya-bessioud/Timesheet-DevOps.git'
            }
        }

        stage('Build & Test') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t backend-app:latest .'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'docker-registry',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {
                    sh 'echo "$DOCKER_PASSWORD" | docker login localhost:5000 -u "$DOCKER_USER" --password-stdin'
                }
            }
        }

        stage('Docker Push') {
            steps {
                sh 'docker tag backend-app:latest localhost:5000/backend-app:latest'
                sh 'docker push localhost:5000/backend-app:latest'
            }
        }
    }
}