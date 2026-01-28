pipeline {
    agent any

    tools {
        nodejs 'NodeJS-20.10.0'
    }

    environment {
        MONGO_URI = "mongodb+srv://supercluster.d83jj.mongodb.net/superData"
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
                withCredentials([usernamePassword(credentialsId: 'mongo cred user and pass', passwordVariable: 'MONGO_PASSWORD', usernameVariable: 'MONGO_USERNAME')]) {
                    sh 'npm test'
                }

                junit allowEmptyResults: true, stdioRetention: '', testResults: 'test-results.xml'
                
            }
        }
    }
}