pipeline {
    agent any
    environment {
        NETLIFY_SITE_ID = 'e67d4882-f793-411c-98dd-c9c5cf835af6'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
        REACT_APP_VERSION = "1.2.$BUILD_ID"
    }

    stages {
        stage('docker') {
            steps {
                docker build -t playwright_test .
            }   
        }      
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                echo 'Building the application'
                sh '''
                 node --version 
                 npm --version
                 ls -la
                 npm ci
                 npm run build
                 ls -la
                '''
            }
        } 
        stage('Tests') {
            parallel {
                stage('unit test') {
                    agent {
                        docker {
                            image 'node:18-alpine'
                            reuseNode true
                        }
                    }
                    steps {
                        echo 'testing  the application'
                        sh '''
                        test -f build/index.html
                        npm test
                        '''
                    }
                    post {
                        always {
                        junit 'jest-results/junit.xml'
                        }   
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
                        echo 'E2E testing'
                        sh '''
                        npm install serve
                        node_modules/.bin/serve -s build &
                        sleep 10
                        npx playwright test --reporter=html
                        '''
                    } 
                    post {
                        always {
                            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright E2E Local  HTML Report', reportTitles: '', useWrapperFileDirectly: true])
                        }
                    }
                }       
            }
        }    
        stage('staging Deploy and E2E') {
                    agent {
                        docker {
                            image 'playwright_test'
                            reuseNode true
                        }
                    }
                    environment {
                        CI_ENVIRONMENT_URL ='staging_URL_TO_BE_SET'
                    }
                    steps {
                        echo 'Deploying the application to staging'
                        sh '''
                        netlify --version
                        echo "Deploying to staging Site ID : $NETLIFY_SITE_ID"
                        netlify status
                        netlify deploy --dir=build --json > staging-deploy-output.json
                        echo 'testing testing'
                        CI_ENVIRONMENT_URL=$(node-jq -r '.deploy_url' staging-deploy-output.json)
                        echo  "$CI_ENVIRONMENT_URL"
                        echo 'staging E2E testing' 
                        sleep 10
                        npx playwright test --reporter=html                  
                        '''
                    } 
                    post {
                        always {
                            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright staging HTML Report', reportTitles: '', useWrapperFileDirectly: true])
                        }
                    }
        }       
        stage('Production Deploy and E2E') {
                    agent {
                        docker {
                            image 'playwright_test'
                            reuseNode true
                        }
                    }
                    environment {
                        CI_ENVIRONMENT_URL = 'https://spiffy-begonia-d7ae97.netlify.app'
                    }
                    steps {
                          echo 'Deploying the application in production'
                          sh '''
                          netlify --version
                          echo "Deploying to production Site ID : $NETLIFY_SITE_ID"
                          netlify status
                          netlify deploy --dir=build --prod
                          echo 'Production E2E testing'
                          npx playwright test --reporter=html
                          '''
                    } 
                    post {
                        always {
                            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright production HTML Report', reportTitles: '', useWrapperFileDirectly: true])
                        }
                    }
        }       
    }
}