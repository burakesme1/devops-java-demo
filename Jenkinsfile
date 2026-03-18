pipeline {
 
agent any
 
environment {
  DOCKER_IMAGE = "fethars/devops-java-app"
  IMAGE_TAG = "v1.${BUILD_NUMBER}"
}
 
tools {
  maven 'maven'
}
 
stages {
 
  stage('Checkout') {
            steps {
                checkout scm
            }
        }
  }
 
  stage('Build') {
   steps {
    sh 'mvn clean package'
   }
  }
 
  stage('SonarQube Scan') {
   steps {
    withSonarQubeEnv('SonarQubeServer') {
     sh 'mvn sonar:sonar'
    }
   }
  }
 
  stage('Docker Build') {
   steps {
    sh 'docker build -t $DOCKER_IMAGE:$IMAGE_TAG .'
   }
  }
 
  stage('Trivy Scan') {
   steps {
    sh 'trivy image --severity HIGH,CRITICAL $DOCKER_IMAGE:$IMAGE_TAG'
   }
  }
 
  stage('Push Docker Image') {
   steps {
    withCredentials([usernamePassword(
      credentialsId: 'dockerhub-credentials',
      usernameVariable: 'USER',
      passwordVariable: 'PASS'
    )]) {
 
      sh '''
      echo $PASS | docker login -u $USER --password-stdin
      docker push $DOCKER_IMAGE:$IMAGE_TAG
      '''
 
    }
   }
  }
 
}
 
}