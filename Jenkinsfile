pipeline {
  agent { docker { image 'node:20'; args '-u root:root' } }

  environment {
    IMAGE  = "${env.DOCKERHUB_USER}/practica_7.1"
    TAG    = "${env.BUILD_NUMBER}"
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Install & Test') {
      steps {
        sh 'npm install'
        sh 'npm run test'
      }
    }

    stage('Build Docker Image') {
      steps {
        script {
          app = docker.build("${IMAGE}:${TAG}")
        }
      }
    }

    stage('Push to Docker Hub') {
      steps {
        withCredentials([usernamePassword(
          credentialsId: 'dockerhub-creds',
          usernameVariable: 'DOCKER_USER',
          passwordVariable: 'DOCKER_PASS'
        )]) {
          sh "echo $DOCKER_PASS | docker login --username $DOCKER_USER --password-stdin"
          sh "docker push ${IMAGE}:${TAG}"
        }
      }
    }
  }

  post {
    always {
      cleanWs()
    }
  }
}
