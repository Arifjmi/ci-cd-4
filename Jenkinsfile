pipeline {
  agent any

  stages {

    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Copy Code to Docker Server') {
      steps {
        sh '''
        ssh root@15.206.27.123 "rm -rf /tmp/html"
        scp -r * root@15.206.27.123:/tmp/html
        '''
      }
    }

    stage('Build Image on Docker Server') {
      steps {
        sh """
        ssh root@15.206.27.123 "cd /tmp/html && docker build -t arifreza/html:${BUILD_NUMBER} ."
        """
      }
    }

    stage('Push Image to DockerHub') {
      steps {
        sh """
        ssh root@15.206.27.123 "docker push arifreza/html:${BUILD_NUMBER}"
        """
      }
    }

    stage('Deploy on Web Server') {
      steps {
        sh """
        ssh root@13.127.113.88 "docker rm -f web || true"
        ssh root@13.127.113.88 "docker pull arifreza/html:${BUILD_NUMBER}"
        ssh root@13.127.113.88 "docker run -d --name web -p 80:80 arifreza/html:${BUILD_NUMBER}"
        """
      }
    }
  }
}
