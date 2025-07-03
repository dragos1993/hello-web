pipeline {
    agent {
        docker {
            image 'mcr.microsoft.com/azure-cli'
            args '-u root'
        }
    }
    environment {
        DEPLOYMENT_TOKEN = credentials('azure-deploy-token') // securely pulls deployment token
    }
    stages {
        stage('Deploy Azure CLI') {
            steps {
                withCredentials([azureServicePrincipal(
                    credentialsId: 'jenkins-static-swa-sp',
                    clientIdVariable: 'AZURE_CLIENT_ID',
                    clientSecretVariable: 'AZURE_CLIENT_SECRET',
                    tenantIdVariable: 'AZURE_TENANT_ID',
                    subscriptionIdVariable: 'AZURE_SUBSCRIPTION_ID'
                )]) {
                    sh '''
                        npm install -g @azure/static-web-apps-cli
                        echo "Client ID: $AZURE_CLIENT_ID"
                        az login --service-principal \
                          -u $AZURE_CLIENT_ID \
                          -p $AZURE_CLIENT_SECRET \
                          -t $AZURE_TENANT_ID
                        az account set --subscription $AZURE_SUBSCRIPTION_ID
                    '''
                }
            }
        }
        stage('Deploy to SWA') {
            agent {
                docker {
                    image 'node:18-alpine' // includes npm
                }
            }
            steps {
                sh '''
                    npm install -g @azure/static-web-apps-cli
                    swa deploy \
                      --app-location app \
                      --deployment-token $DEPLOYMENT_TOKEN
                '''
            }
        }
    }
}