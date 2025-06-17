pipeline {
    agent any

    environment {
        NETLIFY_SIT_ID = 'b0ca5687-0b10-4b1e-a7c0-490f156350b9'

    }

    stages {
        stage('Deploy') {
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
                    echo " Site ID $NETLIFY_SIT_ID "
                '''
            }
        }
    }
}