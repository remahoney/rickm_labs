pipeline {
  agent {
    docker {
      image 'dlambrig/gradle-agent:latest'
      args '-v /var/run/docker.sock:/var/run/docker.sock'
      alwaysPull true
      customWorkspace '/home/jenkins/.gradle/workspace'
    }
  }
  environment {
    REGISTRY = "https://localhost:5001" // Replace with actual registry address
    REGISTRY_HOST = "localhost:5001" // Replace with actual registry address
    PROJECT_DIR = "Chapter08/sample1"
    IMAGE_NAME = "calculator"
    IMAGE_TAG = "${BUILD_NUMBER}" // Example tag
  }
  stages {
    stage('Checkout code and prepare environment') {
      steps {
        git clone -b master https://github.com/remahoney/Continuous-Delivery-with-Docker-and-Jenkins-Second-Edition.git
        sh """
          set -e
          cd ${PROJECT_DIR}
          chmod +x gradlew
        """
      }
    }
    stage('Build') {
      steps {
        sh """
          set -e
          cd ${PROJECT_DIR}
          ./gradlew build
        """
      }
    }
    stage('Login to Registry and build image') {
      steps {
        script {
          withCredentials([usernamePassword(credentialsId: 'docker-registry', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
        sh """
          set -e
          cd ${PROJECT_DIR}
          echo "\$DOCKER_PASS" | docker login \$REGISTRY -u \$DOCKER_USER --password-stdin
          docker build -t ${IMAGE_NAME} .
          docker tag ${IMAGE_NAME} ${REGISTRY_HOST}/${IMAGE_NAME}:${IMAGE_TAG}
          docker push ${REGISTRY_HOST}/${IMAGE_NAME}:${IMAGE_TAG}
        """}
        }
      }
    }
  }
}
