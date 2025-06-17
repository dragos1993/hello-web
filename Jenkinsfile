pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = 'b0ca5687-0b10-4b1e-a7c0-490f156350b9'
        NETLIFY_AUTH_TOKEN = credentials('netlify_token')
        CI_ENVIRONMENT_URL = 'https://685137ba4d2d8200563f6d56--aquamarine-twilight-4f5869.netlify.app/'
    }

    stages {
        stage('Deploy staging') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    npm install netlify-cli node-jq
                    node_modules/.bin/netlify --version
                    echo "Site ID $NETLIFY_SITE_ID"
                    node_modules/.bin/netlify status 
                    node_modules/.bin/netlify deploy --dir=build --json > deploy-output.json
                    

                    npm ci || npm install
                    npx playwright install
                '''
            }
            script {
                env.STAGING_URL = sh(script: "node_modules/.bin/node-jq -r '.deploy_url' deploy-output.json", returnStdout: true)
            }
        }

        stage('Staging E2E') {
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
                    npx playwright test --reporter=html
                '''
            }
        }

        stage('Deploy PROD') {
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
                    npm ci || npm install
                    npx playwright install
                '''
            }
        }
    }
}