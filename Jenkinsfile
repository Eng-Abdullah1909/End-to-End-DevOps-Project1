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


        stage('Dependancy scanning'){
            parallel{
                stage('NPM Dependency Audit') {
                    steps {
                        sh '''
                            npm audit --audit-level=critical
                            echo $?
                        ''' 
                    }
                }

                stage('NPM Dependency fix') {
                    steps {
                        sh 'npm audit fix'
                            
                    }
                }
            }
        }

        
    
    }
    
}