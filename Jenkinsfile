pipeline {
    agent any
    environment {
        REACT_APP_VERSION = '1.2.3'
    }
    stages {
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
        } */
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
                    image 'mcr.microsoft.com/playwright:v1.40.0-jammy'
                    reuseNode true
                }
            }
            sh '''
              npm install serve
              node_modules/.bin/serve s build &
              sleep 10
              npx playwright test
            '''
        }
    }
    post {
        always {
            junit 'jest-results/junit.xml'
        }
    }
}