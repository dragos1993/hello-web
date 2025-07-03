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
        stage('Build') {
            steps {
                echo 'No build required for static HTML!'
            }
        }
        stage('Deploy') {
            steps {
                sh '''
                  az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET --tenant $AZURE_TENANT_ID
                  az staticwebapp upload --name "myApp" --resource-group "Ubuntu_group" --source "app"
                '''
            }
        }
    }
}