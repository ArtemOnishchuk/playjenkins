pipeline {

  environment {
    registry = "172.16.0.109:31320/test/web"
    dockerImage = ""
  }

 agent any
  
  stages {

    stage('Checkout Source') {
      steps {
        
        git 'https://github.com/ArtemOnishchuk/playjenkins.git'
      }
    }

    stage('Build image') {
      steps {  
        script {
          dockerImage = docker.build registry + ":$BUILD_NUMBER"
          
        }  
      }
    }

    stage('Push Image') {
      steps{
        script {
          docker.withRegistry( "" ) {
            dockerImage.push()
          }
        }
      }
    }

    stage('Deploy App') {
      steps {
        script {
          kubernetesDeploy(configs: "myweb.yaml", kubeconfigId: "mykubeconfig")
        }
      }
    }

  }

}
