pipeline {
  agent {
    kubernetes {
      label 'devops'
      defaultContainer 'jnlp'
      yaml """
apiVersion: v1
kind: Pod
metadata:
labels:
  component: ci
spec:
  # Use service account that can deploy to all namespaces
  serviceAccountName: default
  containers:
  - name: maven
    image: maven:latest
    command:
    - cat
    tty: true
    volumeMounts:
      - mountPath: "/root/.m2"
        name: m2
  - name: docker
    image: docker:latest
    command:
    - cat
    tty: true
    volumeMounts:
    - mountPath: /var/run/docker.sock
      name: docker-sock
  volumes:
    - name: docker-sock
      hostPath:
        path: /var/run/docker.sock
    - name: m2
      persistentVolumeClaim:
        claimName: jenkins
"""
}
   }
  stages {
    stage('Maven build') {
      steps {
        container('maven') {
          sh """
                mvn clean install
            """
        }
      }
    }
    stage('Docker build') {
      steps {
        container('docker') {
          sh """
             docker build -t gayathirims/northstar-dev:$BUILD_NUMBER .
          """
        }
      }
    }
    stage('Docker image push') {
      steps {
        container('docker') {
              withCredentials([usernamePassword(credentialsId: 'docker-hub', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
              sh """
                docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}
                docker push gayathirims/northstar-dev:$BUILD_NUMBER
              """
          }          
        }
      }
    }    
  }
}
