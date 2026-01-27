pipeline {
    agent any

    tools 'NodeJS-25.0.0'

    stages {
        stage('VM Node Version') {
            steps {
                sh '''
                    node -v
                    npm -v
                '''
            }
        }
    }
}