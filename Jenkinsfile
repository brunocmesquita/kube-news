pipeline {
  agent any

  stages {
    stage('Build Docker image') {
      steps {
        script {
          dockerapp = docker.build("brunocmesquita/kube-news:${env.BUILD_ID}", "-f ./src/Dockerfile ./src")
        }
      }
    }

    stage('Push Docker image to Dockerhub') {
      steps {
        script {
          docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
            dockerapp.push('latest')
            dockerapp.push("${env.BUILD_ID}")
          }
        }
      }
    }

    stage('Deploy Kubernetes') {
      steps {
        script {
         withKubeConfig([credentialsId: 'kubeconfig']) {
          sh 'kubectl apply -f ./k8s/deployment.yaml'
         }
        }
      }
    }
  }
}