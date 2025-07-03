pipeline {
  agent {
    docker {
      image 'mcr.microsoft.com/azure-cli'
      args '-u root'
    }
  }
  stages {
    stage('Deploy') {
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
            swa deploy --app-location app --deployment-token "d343a9774b14e01a235fdd0458ab9cc8e90153503df66f045764915ecc63aef601-eeb3908c-6006-431e-83ae-ccddecd319ea00f200005f22ee0f"
          '''
        }
      }
    }
  }
}