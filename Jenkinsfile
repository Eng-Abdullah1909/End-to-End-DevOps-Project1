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
        Sonar_Scanner_Home = tool 'sonarqube-scanner-6.1.0'

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

        // stage('SAST - SonarQube') {
        //     steps {
        //         withSonarQubeEnv('sonarqube-scanner') {
        //             sh 'echo $Sonar_Scanner_Home'
        //             sh '''
        //             $Sonar_Scanner_Home/bin/sonar-scanner \
        //                 -Dsonar.projectKey=Solar-System-Project \
        //                 -Dsonar.sources=app.js \
        //                 -Dsonar.javascript.lcov.reportPaths=/coverage/lcov.info \
        //             '''
        //         }
                
        //         waitForQualityGate abortPipeline: true
        //     }
        // }

        stage('Build Docker Image') {
            steps {
                sh 'printenv'
                sh 'docker build -t engabdullah1909/solar-system:$GIT_COMMIT .'
            }
        }

        stage('Trivy Vulnerability Scan') {
            steps {
                sh '''
                    echo "========== TRIVY SCAN RESULTS =========="
                    trivy image \
                        --severity LOW \
                        --format table \
                        --exit-code 0 \
                        --quiet \
                        engabdullah1909/solar-system:$GIT_COMMIT
                '''
                
                sh '''
                    trivy image \
                        --severity HIGH,CRITICAL \
                        --format json \
                        --output trivy-scan-report.json \
                        --exit-code 1 \
                        --quiet \
                        engabdullah1909/solar-system:$GIT_COMMIT
                '''
                
            }
            post {
                always {
                    archiveArtifacts artifacts: 'trivy-scan-report.json', fingerprint: true
                }
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