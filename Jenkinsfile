pipeline{
    agent any
    
    stages{
        stage('checking node version'){
            steps{
                echo "checking nodejs version"
                sh 'node -v'
            }    
        }

        stage('checking npm version'){
            steps{
                echo "checking npm version"
                sh 'npm -v'
            }    
        }

    }
}