stage('Docker build') {
      steps {
        container('docker') {
          sh """
             docker build -t aakashk33/mavenapp:$BUILD_NUMBER .
          """
        }
      }
    }
    stage('Docker image push') {
      steps {
        container('docker') {
              withCredentials([usernamePassword(credentialsId: 'docker-id', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
              sh """
                docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}
                docker push aakashk33/mavenapp:$BUILD_NUMBER
              """
          }          
        }
      }
    }    
  }
}
