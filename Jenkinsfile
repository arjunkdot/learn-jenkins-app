pipeline {
    agent any
    environment{
        NETLIFY_SITE_ID = '999f7818-0056-46bf-a66a-940c9519b992'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
        REACT_APP_VERSION = "1.2.$BUILD_ID"
    }
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
        // stage('Run Tests'){
        //     parallel{
        //         stage('Unit') {
        //     agent {
        //         docker {
        //             image 'node:18-alpine'
        //             reuseNode true
        //         }
        //     }
        //     steps{
        //         sh'''
        //             test build/index.html
        //             npm test
        //         '''
        //     }
        // }
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
        //     }
        // }

        stage('Deploy Staging') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh'''
                    npm install netlify-cli
                     node_modules/.bin/netlify -v
                     echo "Deploying to staging. Site ID: $NETLIFY_SITE_ID"
                     node_modules/.bin/netlify status
                     node_modules/.bin/netlify deploy --dir=build --json > deploy-output.json

                '''
                script{
                    env.STAGING_URL = sh(script: "node_modules/.bin/node-jq -r '.deploy_url' deploy-output.json", returnStdout: true)
                }
            }
        }
        stage('Approval'){
            steps{
                timeout(time: 1, unit: 'HOURS') {
                  input message: 'Ready to deploy?', ok: 'Yes, I\'m sure!'
                }
            }
        }
        stage('Deploy Prod.') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh'''
                    npm install netlify-cli
                     node_modules/.bin/netlify -v
                     echo "Deploying to production. Site ID: $NETLIFY_SITE_ID"
                     node_modules/.bin/netlify status
                      node_modules/.bin/netlify deploy --dir=build --prod 
                '''
            }
        }
        
    }
    post{
        always{
            junit 'jest-results/junit.xml'
        }
    }
}
