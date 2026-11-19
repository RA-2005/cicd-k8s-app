pipeline {
  agent any
  environment {
    IMAGE = "yourdockerhubid/cicd-k8s-app"
    TAG   = "${BUILD_NUMBER}"
  }
  stages {
    stage('Build Image') { steps { sh 'docker build -t $IMAGE:$TAG .' } }
    stage('Push Image') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'U', passwordVariable: 'P')]) {
          sh 'echo $P | docker login -u $U --password-stdin && docker push $IMAGE:$TAG'
        }
      }
    }
    stage('Deploy to Kubernetes') {
      steps {
        sh '''
          sed "s|IMAGE_PLACEHOLDER|$IMAGE:$TAG|" k8s/deployment.yaml | kubectl apply -f -
          kubectl apply -f k8s/service.yaml
          kubectl rollout status deployment/cicd-app --timeout=120s
        '''
      }
    }
  }
}
