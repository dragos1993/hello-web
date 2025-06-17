pipeline {
    agent any

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
                    npm install netlify-cli@20.1.1 
                    node_modules/.bin/netlify --version
                '''
            }
        }
    }
}