pipeline {
    agent any

    tools {
     nodejs 'NodeJS-25.0.0'
    }



    stages {
        stage('Installing Dependencies') {
            steps {
                sh 'npm install --no-audit'
            }
        }
    }

    stages {
        stage('Installing Dependencies') {
            steps {
                sh '''
                    npm audit --audit-level=critical
                    echo $?
                '''
                
            }
        }
    }
}