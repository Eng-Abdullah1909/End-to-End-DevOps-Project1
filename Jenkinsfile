pipeline {
    agent any

    tools {
        nodejs 'NodeJS-20.10.0'
    }

    stages {
        stage('Installing Dependencies') {
            steps {
                sh 'npm install --no-audit'
            }
        }

        stage('Dependency Fix') {
            steps {
                sh 'npm audit fix || true'
            }
        }

        stage('Dependency Audit') {
            steps {
                sh 'npm audit --audit-level=critical || true'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'npm test'
            }
        }
    }
}