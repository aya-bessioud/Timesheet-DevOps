pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/aya-bessioud/Timesheet-DevOps.git'
            }
        }

        stage('Compile') {
            steps {
                sh 'mvn compile'
            }
        }
    }
}