pipeline {

  options {
    ansiColor('xterm') //optional can be exculede form the file
  }

  agent {
    kubernetes {
      yamlFile 'builder.yaml'
    }
  }

  stages {

    stage('Kaniko Build & Push Image') {
      steps {
        echo 'Kaniko start.'
        container('kaniko') {
          script {
            sh '''
            /kaniko/executor --dockerfile `pwd`/Dockerfile \
                             --context `pwd` \
                             --destination=eiai365/k8s-kubernetes-demo-amd64:${BUILD_NUMBER}
            '''
          }
          echo 'Kaniko finish.'
        }
      }
    }

    stage('Deploy App to Kubernetes') {     
      steps {
        echo 'Deploy start.'
        container('kubectl') {
          withKubeConfig([credentialsId: 'kubeconfig']) {
            sh """
              sed -i "s/<TAG>/${BUILD_NUMBER}/" web-app.yaml
              kubectl apply -f web-app.yaml --validate=false
            """
          }
        echo 'Deploy finish.'
        }
      }
    }
  
  }
}