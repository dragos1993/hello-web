pipeline {
    agent {
        docker {
            image 'mcr.microsoft.com/azure-cli:2.56.0'
            args '-u root'
        }
    }
    environment {
        DEPLOYMENT_TOKEN = credentials('azure-deploy-token') // store as Secret Text in Jenkins
    }
    stages {
        stage('Install Node.js') {
            steps {
                sh '''
                    curl -fsSL https://deb.nodesource.com/setup_18.x | bash -
                    apt-get install -y nodejs
                    node -v
                    npm -v
                '''
            }
        }
        stage('Login to Azure') {
            steps {
                withCredentials([azureServicePrincipal(
                    credentialsId: 'jenkins-static-swa-sp',
                    clientIdVariable: 'AZURE_CLIENT_ID',
                    clientSecretVariable: 'AZURE_CLIENT_SECRET',
                    tenantIdVariable: 'AZURE_TENANT_ID',
                    subscriptionIdVariable: 'AZURE_SUBSCRIPTION_ID'
                )]) {
                    sh '''
                        az login --service-principal \
                          -u $AZURE_CLIENT_ID \
                          -p $AZURE_CLIENT_SECRET \
                          -t $AZURE_TENANT_ID

                        az account set --subscription $AZURE_SUBSCRIPTION_ID
                    '''
                }
            }
        }
        stage('Deploy to Azure Static Web Apps') {
            steps {
                sh '''
                    npm install -g @azure/static-web-apps-cli
                    swa deploy \
                      --app-location app \
                      --deployment-token "$DEPLOYMENT_TOKEN"
                '''
            }
        }
    }
}