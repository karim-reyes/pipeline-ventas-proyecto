pipeline {
  agent any
  environment {
    IMAGE = "registry.example.com/dataops/comisiones:${env.BUILD_NUMBER}"
  }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    stage('Build') {
      steps {
        sh 'docker build -t $IMAGE .'
      }
    }

    stage('Test job') {
      steps {
        sh '''
          docker run --rm \
            -v $WORKSPACE/config.json:/app/config.json:ro \
            -v $WORKSPACE/data:/app/data:ro \
            $IMAGE
        '''
      }
    }

    stage('Push image') {
      when { branch 'main' }
      steps {
        withCredentials([usernamePassword(credentialsId: 'registry-creds',
                                          usernameVariable: 'REG_USER',
                                          passwordVariable: 'REG_PASS')]) {
          sh '''
            echo $REG_PASS | docker login registry.example.com -u $REG_USER --password-stdin
            docker push $IMAGE
          '''
        }
      }
    }
  }

  post {
    success {
      archiveArtifacts artifacts: 'ComisionesCalculadas.xlsx', fingerprint: true
    }
  }
}
