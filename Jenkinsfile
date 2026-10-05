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

        stage('Deploy') {
            steps {
                sh 'docker rm -f backend-app || true'

                sh 'docker pull localhost:5000/backend-app:latest'

                sh '''
                    docker run -d \
                      --name backend-app \
                      --network timesheet-network \
                      -p 8083:8082 \
                      -e SPRING_DATASOURCE_URL="jdbc:mysql://mysql:3306/timesheet-devops-db?useUnicode=true&useJDBCCompliantTimezoneShift=true&useLegacyDatetimeCode=false&serverTimezone=UTC" \
                      -e SPRING_DATASOURCE_USERNAME=root \
                      -e SPRING_DATASOURCE_PASSWORD= \
                      localhost:5000/backend-app:latest
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                sh 'docker ps'
                sh 'docker logs backend-app --tail 30'
            }
        }
    }
}