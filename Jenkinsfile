pipeline {
    agent any
    environment {
        REACT_APP_VERSION = '1.2.3'
        AZURE_CONFIG_DIR = "${env.WORKSPACE}/.azure"
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
                  '''
                  script {
                    def azureStorageAccountName = 'learningjenkins'
                    def azureStorageContainerName = 'website'

                    // Use the Azure CLI to list blob files
                    def blobListCommand = "az storage blob list --account-name $azureStorageAccountName --container-name $azureStorageContainerName --sas-token "sp=racwdl&st=2024-05-30T13:34:51Z&se=2024-06-02T21:34:51Z&spr=https&sv=2022-11-02&sr=c&sig=rcObuQT7v4ztk3AZpguZWVjM90I9kPaxvvN%2FdAJ9vzQ%3D" --output json"
                     def blobListOutput = sh(script: blobListCommand, returnStdout: true).trim()

                    // Parse the JSON output to extract blob names
                    def blobNames = new groovy.json.JsonSlurper().parseText(blobListOutput)
                        .collect { it.name }
                        .join('\n')

                    echo "Blob names in container '$azureStorageContainerName':\n$blobNames"
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