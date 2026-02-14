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
        IMAGE_REPO = "engabdullah1909/solar-system"
        NAME = "solar-system"
        VERSION = "${env.GIT_COMMIT}"
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

        // stage('Run Tests') {
        //     steps {
        //         withCredentials([usernamePassword(credentialsId: 'mongo cred user and pass', passwordVariable: 'MONGO_PASSWORD', usernameVariable: 'MONGO_USERNAME')]) {
        //             sh 'npm test || true'
        //         }
        //     }
        // }

        // stage('Code Coverage') {
        //     steps {
        //         withCredentials([usernamePassword(credentialsId: 'mongo cred user and pass', passwordVariable: 'MONGO_PASSWORD', usernameVariable: 'MONGO_USERNAME')]) {
        //             catchError(buildResult: 'SUCCESS', message: 'Issue will be fixed next build', stageResult: 'UNSTABLE') {
        //                 sh 'npm run coverage'
        //             }
        //         }
        //     }
        // }

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
                sh 'docker build -t ${IMAGE_REPO}:${VERSION} .'
            }
        }

        // stage('Trivy Vulnerability Scan') {
        //     steps {
        //         sh '''
        //             echo "========== TRIVY SCAN RESULTS =========="
        //             trivy image \
        //                 --severity LOW \
        //                 --format table \
        //                 --exit-code 0 \
        //                 --quiet \
        //                 ${IMAGE_REPO}:${VERSION}
        //         '''

        //         sh '''
        //             trivy image \
        //                 --severity HIGH,CRITICAL \
        //                 --format template \
        //                 --template "@/usr/local/share/trivy/templates/html.tpl" \
        //                 --output trivy-report.html \
        //                 ${IMAGE_REPO}:${VERSION}
        //         '''
        //     }
        // }    

        stage('Push Docker Image') {
            steps {
                withDockerRegistry(credentialsId: 'Docker-Hub-UP', url: "") {
                    sh 'docker push ${IMAGE_REPO}:${VERSION}'
                }
            }
        }

        stage('Clone/Pull GitOps Repo') {
            steps {
                script {
                    if (fileExists('k8s-objects-argocd')) {
                        echo 'GitOps repo already exists - pulling latest changes'
                        dir('k8s-objects-argocd') {
                            sh 'git pull origin main'
                        }
                    } else {
                        echo 'Cloning GitOps repo'
                        sh 'git clone -b main https://github.com/Eng-Abdullah1909/k8s-objects-argocd.git'
                    }
                }
            }
        }

        stage('Update Manifest') {
            steps {
                dir("k8s-objects-argocd") {
                    sh 'sed -i "s#image:.*#image: ${IMAGE_REPO}:${VERSION}#g" deployment.yaml'
                    sh 'cat deployment.yaml'
                }
            }
        }

        stage('Commit & Push') {
            when {
                branch 'main'
            }
            steps {
                dir("k8s-objects-argocd") {

                    sh 'git config --global user.email "abdullahusama7333@gmail.com"'
                    sh 'git config --global user.name "Eng-Abdullah1909"'
                    sh 'git checkout main'


                    sh 'git add -A'
                    sh 'git commit -am "Updated image version for Build - ${VERSION}" || echo "No changes to commit"'


                    withCredentials([string(credentialsId: 'Git-hub-token', variable: 'GITHUB_TOKEN')]) {
                        sh 'git remote set-url origin https://Eng-Abdullah1909:${GITHUB_TOKEN}@github.com/Eng-Abdullah1909/k8s-objects-argocd.git'
                        sh 'git push origin main'
                    }
                }
            }
        }
    }

    post {
        always {
            junit allowEmptyResults: true, stdioRetention: '', testResults: 'test-results.xml'

            publishHTML([allowMissing: true, alwaysLinkToLastBuild: true, keepAll: true, reportDir: 'coverage/lcov-report', reportFiles: 'index.html', reportName: 'Code Coverage HTML Report'])
            publishHTML([allowMissing: true, alwaysLinkToLastBuild: true, keepAll: true, reportDir: './', reportFiles: 'trivy-report.html', reportName: 'Trivy Scan Report'])
        }
    }
}
