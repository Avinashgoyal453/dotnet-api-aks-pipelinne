pipeline {
  agent any

  environment {
    ACR_NAME = "avinashcr1"
    IMAGE_NAME = "myapi"
    RESOURCE_GROUP = "myResourceGroup"
    CLUSTER_NAME = "avinashAKSCluster"
  }

  stages {

    stage('Terraform Init & Apply') {
      steps {
        dir('terraform') {
          bat 'terraform init'
          bat 'terraform apply -auto-approve'
        }
      }
    }

    stage('Build Docker Image') {
      steps {
        bat 'docker build -t %ACR_NAME%.azurecr.io/%IMAGE_NAME%:latest .'
      }
    }

    stage('Push Docker Image to ACR') {
      steps {
        bat 'az acr login --name %ACR_NAME%'
        bat 'docker push %ACR_NAME%.azurecr.io/%IMAGE_NAME%:latest'
      }
    }

    stage('Deploy to AKS') {
      steps {
        bat 'az aks get-credentials --resource-group %RESOURCE_GROUP% --name %CLUSTER_NAME%'
        bat 'kubectl apply -f k8s\\deployment.yaml'
      }
    }
  }

  post {
    success {
      echo "✅ Deployment successful!"
    }
    failure {
      echo "❌ Deployment failed!"
    }
  }
}
