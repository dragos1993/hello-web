pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = 'b0ca5687-0b10-4b1e-a7c0-490f156350b9'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
    }

    stages {
        stage('DEV') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                 sh '''
                    npm install netlify-cli 
                    node_modules/.bin/netlify --version
                    echo "Site ID $NETLIFY_SITE_ID"
                    node_modules/.bin/netlify status 
                    node_modules/.bin/netlify deploy --dir=build 
                '''
            }
        }

        stage('E2E DEV') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                    reuseNode true
                }
            }
            environment {
                CI_ENVIRONMENT_URL = "${env.STAGING_URL}"
            }
            steps {
                sh '''
                    npm install netlify-cli node-jq
                    node_modules/.bin/netlify --version
                    echo "Site ID $NETLIFY_SITE_ID"
                    node_modules/.bin/netlify status 
                    node_modules/.bin/netlify deploy --dir=build --json > deploy-output.json
                '''
                script {
                    env.STAGING_URL = sh(script: "node_modules/.bin/node-jq -r '.deploy_url' deploy-output.json", returnStdout: true)
                }
            }
        }

        
        stage('PROD') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                 sh '''
                    npm install netlify-cli 
                    node_modules/.bin/netlify --version
                    echo "Site ID $NETLIFY_SITE_ID"
                    node_modules/.bin/netlify status 
                    node_modules/.bin/netlify deploy --dir=build --prod
                '''
            }
        }
    }
}