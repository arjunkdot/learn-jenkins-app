pipeline {
    agent any

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh'''
                    ls -la
                    node -v
                    npm -v
                    npm ci
                    npm run build
                     ls -la
                '''
            }
        }
        stage('Test') {
            steps{
                sh'''
                    test build/index.html
                    npm run test
                '''
            }
        }
    }
}
