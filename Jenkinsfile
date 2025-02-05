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
            dockerapp.push('latest')
          docker.withRegistry("https://registry.hub.docker.com", 'dockerhub') {
            dockerapp.push("${env.BUILD_ID}")
          }
        }
      }
    }

    stage('Deploy Kubernetes') {
      environment {
        tag_version = "${env.BUILD_ID}"
      }
      steps {
        script {
         withKubeConfig([credentialsId: 'kubeconfig']) {
          sh 'sed -i "s/{{TAG}}/$tag_version/g" ./k8s/deployment.yaml'
          sh 'kubectl apply -f ./k8s/deployment.yaml'
         }
        }
      }
    }
  }
}
