pipeline {
  agent none
  environment {
    DEPLOYMENT_TOKEN = credentials('azure-deploy-token')
  }
  stages {
  
    stage('Azure Login') {
      agent {
        docker {
          image 'mcr.microsoft.com/azure-cli'
          args '-u root'
        }
      }
      steps {
        withCredentials([azureServicePrincipal(
          credentialsId: 'jenkins-static-swa-sp',
          clientIdVariable: 'AZURE_CLIENT_ID',
          clientSecretVariable: 'AZURE_CLIENT_SECRET',
          tenantIdVariable: 'AZURE_TENANT_ID',
          subscriptionIdVariable: 'AZURE_SUBSCRIPTION_ID'
        )]) {
          sh '''
            echo "Installing SWA CLI via npm"
            npm install -g @azure/static-web-apps-cli || true

            echo "Azure Login"
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
          image 'node:18-alpine'
        }
      }
      steps {
        sh '''
          echo "Installing SWA CLI again inside Node container"
          npm install -g @azure/static-web-apps-cli --unsafe-perm

          echo "Deploying with swa deploy"
          swa deploy \
            --app-location app \
            --deployment-token $DEPLOYMENT_TOKEN
        '''
      }
    }

  } // end stages
}
