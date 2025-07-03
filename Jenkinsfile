pipeline {
  agent {
    docker {
      image 'node:18'
      args '-u root'
    }
  }

  environment {
    DEPLOYMENT_TOKEN = credentials('azure-deploy-token')
    AZURE_CLIENT_ID = credentials('your-client-id-secret-id')
    AZURE_CLIENT_SECRET = credentials('your-client-secret-secret-id')
    AZURE_TENANT_ID = credentials('your-tenant-id-secret-id')
    AZURE_SUBSCRIPTION_ID = credentials('your-subscription-id-secret-id')
  }

  stages {
    stage('Install Tools & Azure Login') {
      steps {
        sh '''
          echo "Installing Azure CLI and SWA CLI"
          curl -sL https://aka.ms/InstallAzureCLIDeb | bash
          npm install -g @azure/static-web-apps-cli

          echo "Logging in to Azure"
          az login --service-principal \
            -u $AZURE_CLIENT_ID \
            -p $AZURE_CLIENT_SECRET \
            -t $AZURE_TENANT_ID

          az account set --subscription $AZURE_SUBSCRIPTION_ID
        '''
      }
    }

    stage('Deploy to Azure Static Web App') {
      steps {
        sh '''
          echo "Deploying to Azure Static Web App"
          swa deploy --app-location app --env preview --deployment-token $DEPLOYMENT_TOKEN
        '''
      }
    }
  }
}
