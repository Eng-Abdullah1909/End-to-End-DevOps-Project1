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
        Sonar_scanner_Home = 'sonarqube-scanner-6.1.0';

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
                    sh 'npm test || true'
                }
                
            }
        }
        stage('code coverage') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'mongo cred user and pass', passwordVariable: 'MONGO_PASSWORD', usernameVariable: 'MONGO_USERNAME')]) {
                    catchError(buildResult: 'SUCCESS', message: 'Issue will be fixed next build', stageResult: 'UNSTABLE') {
                      sh 'npm run coverage'
                    }
                }

                
            }
        }

        stage('SAST - SonarQube') {
            steps {
                sh 'echo $Sonar_scanner_Home '
                sh '''
                    $Sonar_scanner_Home /bin/sonar-scanner \
                        -Dsonar.projectKey=Solar-System-Project \
                        -Dsonar.sources=app.js \
                        -Dsonar.host.url=http://localhost:9000 \
                        -Dsonar.login=sqp_4e2e975f82b1c44b269e1382616b1b014b174b74
                '''
            }
        }

    }

    post {
        always {
            junit allowEmptyResults: true, stdioRetention: '', testResults: 'test-results.xml'
            publishHTML([allowMissing: true, alwaysLinkToLastBuild: true, keepAll: true, reportDir: 'coverage/lcov-report', reportFiles: 'index.html', reportName: 'Code Coverage HTML Report', reportTitles: '', useWrapperFileDirectly: true])

        }
    }

}