pipeline {
    agent any

    stages {
        stage('Build') {
            agent {
             docker {
                image 'node: 18-alpine'
                reuseNode true
            }
            }
            steps {
                echo 'Hello World'
                sh '''
                 node --version 
                 npm --version
                '''
            }
        }
    }
}