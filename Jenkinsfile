pipeline {
  agent any

  tools {
    jdk 'jdk17'
    maven 'maven3'
  }

  environment {
    PROJECT_KEY = "my-java-app"
  }

  stages {

    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Build') {
      steps {
        sh 'mvn -B clean compile'
      }
    }

    stage('Sonar Analysis') {
      steps {
        withSonarQubeEnv('sonarqube-server') {
          withCredentials([string(credentialsId: 'token-sonar', variable: 'SONAR_TOKEN')]) {
            sh """
              mvn -B sonar:sonar \
                -Dsonar.login=$TOKEN_SONAR \
                -Dsonar.projectKey=${PROJECT_KEY} \
                -Dsonar.projectName=${PROJECT_KEY}
            """
          }
        }
      }
    }

    stage('Quality Gate') {
      steps {
        timeout(time: 10, unit: 'MINUTES') {
          waitForQualityGate abortPipeline: true
        }
      }
    }
  }
}
