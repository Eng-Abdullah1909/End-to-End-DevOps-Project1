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


        stage('NPM Dependency Audit') {
            steps {
                sh '''
                    npm audit --audit-level=critical
                    echo $?
                '''
                
            }
        }
    
    }
    
}