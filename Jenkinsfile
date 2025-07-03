pipeline {
  agent any
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
    stage('InstallSWA CLI') {
        agent {
            docker {
                image 'node:18'
                args '-u root'
            }
        }
        steps {
            sh '''
            mkdir -p ~/.npm-global
            npm config set prefix '~/.npm-global'
            export PATH=~/.npm-global/bin:$PATH
            npm install -g @azure/static-web-apps-cli
            '''
        }
        }
    stage('Deploy to Azure Static Web App') {
        agent {
           docker {
                image 'node:18'
                args '-u root'
            } 
        }
        steps {
            sh '''
            export PATH=~/.npm-global/bin:$PATH
            swa deploy --app-location app --env preview --deployment-token $DEPLOYMENT_TOKEN
            '''
        }
        }

  } // end stages
}