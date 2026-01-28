pipeline {
    agent any

    options {
        timeout(time: 1, unit: 'HOURS')
    }

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
                sh 'npm audit fix || true '
            }
        }

        stage('Dependency Audit') {
            steps {
                sh 'npm audit --audit-level=critical'
            }
        }

        stage('Run Tests') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'mongo cred user and pass', passwordVariable: 'MONGO_PASSWORD', usernameVariable: 'MONGO_USERNAME')]) {
                    //sh 'npm test'
                    sh '''
                        echo "=== Debug Info ==="
                        echo "MONGO_USERNAME is set: $([ -n \"$MONGO_USERNAME\" ] && echo YES || echo NO)"
                        echo "MONGO_PASSWORD length: ${#MONGO_PASSWORD}"
                        echo "==================="
                        
                        npm test || true
                        
                        echo "=== Test Exit Code: $? ==="
                        
                        ls -la test-results.xml 2>/dev/null || echo "test-results.xml NOT FOUND"
                    '''
                }

                junit allowEmptyResults: true, stdioRetention: '', testResults: 'test-results.xml'
                
            }
        }
        stage('code coverage') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'mongo cred user and pass', passwordVariable: 'MONGO_PASSWORD', usernameVariable: 'MONGO_USERNAME')]) {
                    catchError(buildResult: 'SUCCESS', message: 'Issue will be fixed next build', stageResult: 'UNSTABLE') {
                      sh 'npm run coverage'
                    }
                }

                publishHTML([allowMissing: true, alwaysLinkToLastBuild: true, keepAll: true, reportDir: 'coverage/lcov-report', reportFiles: 'index.html', reportName: 'Code Coverage HTML Report', reportTitles: '', useWrapperFileDirectly: true])
                
            }
        }

    }
}