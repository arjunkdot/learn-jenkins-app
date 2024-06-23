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
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps{
                sh'''
                    test build/index.html
                    npm test
                '''
            }
        }
        /*
        stage('E2E') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.44.1-jammy'
                    reuseNode true
                }
            }
            steps{
                sh'''
                    npm install serve
                    node_modules/.bin/serve -s build &
                    sleep 10
                    npx playwright test
                '''
            }
        }*/
    }
    post{
        always{
            junit 'jest-results/junit.xml'
        }
    }
}
