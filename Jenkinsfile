pipeline {
    agent any
    environment {
        REACT_APP_VERSION = '1.2.3'
        AZURE_CONFIG_DIR = "${env.WORKSPACE}/.azure"
        AZURE_STORAGE_ACCOUNT_NAME = 'learningjenkins'
        AZURE_CONTAINER_NAME = 'website'
        SAS_TOKEN = 'sp=racwdl&st=2024-05-30T13:34:51Z&se=2024-06-02T21:34:51Z&spr=https&sv=2022-11-02&sr=c&sig=rcObuQT7v4ztk3AZpguZWVjM90I9kPaxvvN%2FdAJ9vzQ%3D'
    }
    stages {

        stage('Azure') {
            agent {
                docker {
                    image 'mcr.microsoft.com/azure-cli'
                    reuseNode true
                }
            }
            steps {
                sh '''
                  az version
                  az storage blob list --account-name $AZURE_STORAGE_ACCOUNT_NAME --container-name $AZURE_CONTAINER_NAME --sas-token $SAS_TOKEN
                  '''
                    
                  }
                  

        }
    }
       /* stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    echo build phase
                    ls -la
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    ls -la
                '''
            }
        } 
        stage('Run Tests')
        {
            parallel {
                       stage('Unit Test') {
                            agent {
                                docker {
                                    image 'node:18-alpine'
                                    reuseNode true
                                }
                            }
                            steps {
                                echo 'Unit test phase'
                                sh '''
                                    test -f build/index.html
                                    npm test
                                '''
                            }
                        }
                        stage('E2E') {
                            agent {
                                    docker {
                                        image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                                        reuseNode true
                                    }
                            }
                            steps {

                                sh '''
                                npm install serve
                                node_modules/.bin/serve -s build &
                                sleep 10
                                npx playwright test --reporter=html
                                '''
                                }  
                            }
            }
        } */

    }
 /*   post {
        always {
            junit 'jest-results/junit.xml'
            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright HTML Report', reportTitles: '', useWrapperFileDirectly: true])
        }
    } */
}