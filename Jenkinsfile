pipeline {

  environment {
    dockerimagename = "yurass/python-fast-api"
    dockerImage = ""
  }

  agent any

  stages {

    stage('Build image') {
      steps{
        script {
          dockerImage = docker.build("${dockerimagename}:${env.BUILD_ID}") 
        }
      }
    }

    stage('Pushing Image') {
      environment {
               registryCredential = 'dockerHubYurass'
           }
      steps{
        script {
          docker.withRegistry( 'https://registry.hub.docker.com', registryCredential ) {
            dockerImage.push()
          }
        }
      }
    }
    
    stage('Deploy App on k8s') {
      steps {
        withCredentials([
            string(credentialsId: 'kubernetes')
            ]) {
             sh 'kubectl apply -f deploymentservice.yaml'
               }
            }
        }

    stage('Deploying App to Kubernetes') {
      steps {
        script {
          kubernetesDeploy(configs: "deploymentservice.yml", kubeconfigId: "kubernetes")
        }
      }
    }
  }
}