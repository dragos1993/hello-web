pipeline {
  agent {
    docker {
      image 'mcr.microsoft.com/azure-cli'
      args '-u root'
    }
  }
  environment {
    AZURE_CLIENT_ID = credentials('azure-client-id')
    AZURE_CLIENT_SECRET = credentials('azure-client-secret')
    AZURE_TENANT_ID = credentials('azure-tenant-id')
  }
  stages {
    stage('Deploy') {
      steps {
        sh '''
          az login --service-principal \
            -u $AZURE_CLIENT_ID \
            -p $AZURE_CLIENT_SECRET \
            --tenant $AZURE_TENANT_ID \
            --allow-no-subscriptions

          # optional: set the subscription context if you have one
          az account set --subscription "<YOUR_SUBSCRIPTION_ID>"

          az staticwebapp upload \
            --name "myApp" \
            --resource-group "Ubuntu_group" \
            --source "app"
        '''
      }
    }
  }
}
