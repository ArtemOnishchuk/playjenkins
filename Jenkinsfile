pipeline {

  environment {
    registry = "172.16.0.109:31320/test/web"
    dockerImage = ""
  }

  agent {
    kubernetes {
      label 'sample-app6'
      idleMinutes 5 
      yaml """
apiVersion: v1
kind: Pod
metadata:
  name: jenkins-slave
  namespace: ci-cd
spec:
  containers:
  - name: jenkins-slave
    image: joao29a/jnlp-slave-alpine-docker:latest
    imagePullPolicy: IfNotPresent
    volumeMounts:
      - name: docker-sock
        mountPath: /var/run/docker.sock
    command:
    - cat
    tty: true
  volumes:
    - name: docker-sock
      hostPath:
        path: /var/run/docker.sock
  restartPolicy: Always
"""
}
  }
  
  stages {

    stage('Checkout Source') {
      steps {
        
        git 'https://github.com/ArtemOnishchuk/playjenkins.git'
      }
    }

    stage('Build image') {
      steps{
        container('jenkins-slave') {
          script {
            dockerImage = docker.build registry + ":$BUILD_NUMBER"
          }
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
